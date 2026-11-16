# OpenSpec Architecture Summary & Critique (Post-PRs #2–#6)

Status: brainstorm
Kind: review
Summary: Whole-spec summary and architectural critique of openPractice across the five merged OpenSpec changes and domain framework (`0a82fc2`), identifying the missing Revenue Cycle Core, cross-spec identity and money-format seams, LLM deletion unenforceability, cold-start operational friction, and framing/ratification hygiene debt.
Topics: openpractice-spec-review, architecture-critique, openspec
Captured: 2026-11-15

## Scope Reviewed

Reviewed against `opensoft/openPractice-spec` `main` (`0a82fc2`) and assembly root `opensoft/openPractice` `main` (`bf4926b`), covering:

- Framing & synthesis: `ideation/review/openpractice-framing-decisions.md`, `ideation/review/openpractice-feat-review-synthesis.md`
- Domain & revenue-recognition framework: `ideation/brainstorm/openpractice-domain-framework.md`
- Merged OpenSpec changes under `openspec/changes/`:
  - `add-reimbursement-retention-control` (`RR-01`–`RR-04`)
  - `define-mvp-practice-foundation` (`patient-registry` `PR-01`–`PR-14`, `ehr-boundary` `EB-01`–`EB-18`, `billing-accounting-boundary` `BAB-01`–`BAB-17`, `accounting-integration` `AI-01`–`AI-23`)
  - `define-customer-financial-data-boundaries` (`customer-role-boundaries`, `financial-domain-routing`, `purpose-bound-business-data`)
  - `define-actors-agents-and-work-inbox` (`actor-model` `AM-01`–`AM-06`, `agent-operations` `AO-01`–`AO-12`, `work-inbox` `WI-01`–`WI-07`, `remote-va-safeguards` `VA-01`–`VA-05`, `ai-data-handling` `AH-01`–`AH-05`)
  - `define-patient-gateway-and-escalation` (`patient-app-gateway` `PG-01`–`PG-15`, `escalation-routing` `ER-01`–`ER-18`, `payment-hold` `PH-01`–`PH-09`, `mobile-approver` `MA-01`–`MA-07`)

---

## 1. What openPractice Does (Executive Summary)

**openPractice** is an original, Frappe-native **practice-operations, revenue-cycle, and administrative routing system** for small ambulatory clinics (1–5 clinicians), designed from day one for an **agent-operated practice** where AI agents (such as MedxFactory's Hermes) and remote staff execute bounded routine work while humans handle exceptions, policy, and high-risk approvals.

It sits strictly between two external systems of record and refuses to duplicate either:

1. **Clinical side (`openChart` or another EHR):** Owns encounters, charts, orders, clinical AI transcripts, and clinical messaging. openPractice holds **zero clinical content**—only cross-system patient links (`openpractice-ehr-patient-link/1.0`), coded billable-fact references, and clinical escalation routing metadata (`openpractice-clinical-handoff/1.0`).
2. **General-ledger side (`ERPNext` or `MSBC`, QBO later):** Owns the books, chart of accounts, AP, and bank truth. openPractice is the **operational receivables subledger** (charges, claims, remittances, patient balances, deposits) and exports balanced, idempotent journal postings (`openpractice-accounting-integration/1.0`, `summary-journal` by default with benign service categories and ASC 606 / IFRS 15 constrained-entitlement revenue estimates).

Across the merged OpenSpec changes, openPractice currently specifies five pillars:

- **Patient Registry & EHR Boundary (`define-mvp-practice-foundation`):** Administrative demographics, guarantors/coverages, confidential-communication restrictions, deterministic EHR patient linking, and consumption of signed billable clinical references.
- **Customer & Financial Routing (`define-customer-financial-data-boundaries`):** Separates healthcare/guarantor obligations (owned by openPractice medical billing) from retail/membership commerce obligations, with purpose-bound data visibility and BAA-gated customer projections.
- **Accounting Boundary & Retention (`define-mvp-practice-foundation`, `add-reimbursement-retention-control`, `openpractice-domain-framework.md`):** Subledger-to-GL boundary, ASC 606 / IFRS 15 expected-entitlement revenue recognition with period-close true-ups, deposit explanation, bank-detail protection, and 10-year Medicare Advantage / reimbursement record retention holds.
- **Actors, Agents & Work Inbox (`define-actors-agents-and-work-inbox`):** Unified roster for clinicians, staff, external billers, remote VAs (with bulk-export/offshore gates), and openWallet-identified AI agents governed by a per-action autonomy ladder (`observe -> propose -> act_with_notice -> act_after_delay`), blast-radius limits, a practice-wide pause switch, a controller day-close digest, and strict untrusted-input caps (`AH-05`).
- **Patient Gateway, Escalation, Payment Holds & Mobile Approver (`define-patient-gateway-and-escalation`):** Makes openPractice the gateway/router (`openpractice-patient-gateway/1.0`) for the **HealthLinc** patient app—checking entitlements, routing clinical or urgent items reference-only to `openChart` with shadow SLA clocks and tracked on-call fallbacks, routing administrative questions to Hermes (read-only answers or drafts) or staff lanes, enforcing safety-bounded payment holds, and providing a zero-local-PHI mobile approver client.

---

## 2. Architectural & Product Critique

### A. Critical Gap: "The Missing Middle" (Perimeter Before Core)

Right now, the specification set is inverted: the **perimeter contracts** (EHR link, GL export, agent governance, HealthLinc gateway, payment holds) are specified down to SHA-256 JSON Schema digests, while the **core revenue-cycle engine** that justifies openPractice's existence has no OpenSpec specifications yet:

- **No spec for the `FD-7` Encounter-to-Cash Thread or Administrative Appointment model** (despite Ruling 31 making openPractice the administrative appointment system of record, and `PH-01` deferring its in-app scheduling block until a ratified scheduling change exists).
- **No spec for Service Catalog, Fee Schedules, Charge Capture, Claim Lifecycle, or the Medicare Claim Pathway Adapter** (only conceptual tables in `ideation/brainstorm/openpractice-domain-framework.md`).
- **No spec for the Money Subledger, Reason-Code Taxonomy, Remittance/ERA Posting, Patient Statements, or Good Faith Estimates (GFEs)**—yet `BAB-03`/`BAB-05` (`billing-accounting-boundary`), `PH-06` (`payment-hold` GFE dispute windows), and `PH-07` already normatively depend on them.

**Recommendation:** Pause perimeter expansion and write the **Revenue Cycle Core** change next (Appointment/Thread + Catalog/Charge + Money Subledger + Claim/Remittance + Patient Balance/GFE). Implementing Slice 0 (`patient-registry`) in Frappe before the Thread and Money Subledger data shapes are fixed risks avoidable DocType rework.

---

### B. Cross-Spec Contradictions & Data-Model Seams

1. **Three competing person/identity models (`Party` vs. `Patient Registry` vs. `Actor/Customer`):**
   - `ideation/brainstorm/openpractice-domain-framework.md` (§3.2, D1) specifies **one `Party` record per human/org** with `PartyRole` records (`patient`, `guarantor`, `subscriber`, `provider`, `staff`, `customer`).
   - `openspec/changes/define-mvp-practice-foundation/specs/patient-registry/spec.md` (`PR-01`–`PR-07`) models a patient-centric **Registry record** with attached contacts, guarantors, and subscribers.
   - `openspec/changes/define-actors-agents-and-work-inbox/specs/actor-model/spec.md` (`AM-01`) defines an **Actor/Roster** model, while `define-customer-financial-data-boundaries` (`CRB-01`–`CRB-04`) assumes shared party/account records with separate role evaluations.
   - **Action:** Reconcile the root identity entity before creating Frappe DocTypes—decide explicitly whether Frappe's root table is `OP Party` (linked from `OP Patient`, `OP Guarantor`, `OP Roster Entry`, `OP Commerce Customer`) or separate master tables.

2. **Conflicting money representations across specs:**
   - `ideation/brainstorm/openpractice-domain-framework.md` (§7.3) specifies **integer minor units** plus ISO 4217 currency code.
   - `openpractice-accounting-integration/1.0` (`AI-03`) uses **two-decimal strings** (`"123.45"`), noted via a pointer patch in §7 of the framework doc.
   - **Action:** Standardize on one canonical money representation across all `openpractice-*` JSON contracts before adding payment or claim contracts.

3. **`ER-12` LLM prompt deletion requirement is technically unenforceable as written:**
   - Under `PG-10` and `ER-12` (`openspec/changes/define-patient-gateway-and-escalation/specs/escalation-routing/spec.md`), when an administrative inquiry processed by Hermes is later recategorized as clinical, openPractice removes its stored text *and* must *"require those copies to be deleted under the AH-02 agreements, and SHALL record the request and its confirmation with no PHI"* for both the agent operator and model provider.
   - Commercial LLM inference APIs do not expose synchronous per-prompt deletion APIs or deletion receipts after inference; instead, healthcare-compliant deployments rely on **Zero Data Retention (ZDR)** endpoints where prompts/completions are never persisted by the model provider.
   - **Action:** Amend `AH-02`/`AH-04` and `ER-12` so model providers MUST operate under verified **Zero Data Retention** (ephemeral inference with zero persistence), while stateful agent-operator memory (`MedxFactory`) implements explicit deletion + receipt on `inquiry.recategorized`.

---

### C. Operational & Product Friction Risks

1. **"Fail-Closed Cold-Start Paralysis" for a 1–5 Clinician Practice:**
   Each safety gate is individually sound, but stacked together, a small practice cannot go live or use core features until **four distinct authorities** sign off on nearly a dozen governance artifacts:
   - **Accountant:** D8 revenue-recognition policy + estimator version (`BAB-02`, `AI-06`, `MA-07`).
   - **Counsel:** Hold policy review (`PH-03`), proxy policy review (`PG-06`), HealthLinc operator agreement (`PG-15`), accounting/agent/VA BAAs (`AI-14`, `AH-02`, `VA-01`).
   - **Medical Director:** Routing-clock policy (`ER-08`), hold policy (`PH-03`), proxy policy (`PG-06`), messaging-continuity policy (`PG-04`), and 24/7 hour-by-hour on-call coverage (`ER-13`).
   - **Controller / Manager:** Daily agent digest sign-off (`AO-10`), per-action autonomy ladder opt-ins (`BAB-17`).
   - **Action:** Keep fail-closed runtime enforcement, but specify a unified **Practice Readiness & Policy Starter Kit** (vetted baseline templates awaiting counsel/director/accountant sign-off on one readiness checklist) and explicitly define how authority roles map when a single physician-owner holds multiple hats (Medical Director + Practice Manager + Controller).

2. **Mixed-question ping-pong across three systems (`ER-12`):**
   - Patients routinely combine clinical and administrative topics in one message. Requiring staff to flag the item, openPractice to delete the text and hand the ref to `openChart`, and `HealthLinc` to resubmit only the administrative part via `inquiry.recategorized` creates brittle multi-system state synchronization and poor UX.
   - **Action:** Prefer immutable segment-level references in `HealthLinc` (or staff/patient-confirmed administrative excerpt minting in `HealthLinc`) over full-message deletion plus resubmission loops.

3. **Double re-authentication friction on Mobile Approver (`MA-04` + `MA-07`):**
   - `MA-04` requires re-authentication on every push-notification deep link, and `MA-07` requires re-authentication again at the moment of signing. Implemented naively with passwords, approvers will stop using mobile approvals.
   - **Action:** Specify OS-backed passkey/WebAuthn biometric step-up with a short cryptographic freshness window (e.g., 30–60 seconds) or single biometric sign-off assertion so mobile approvals remain fast without weakening Principle I.

---

### D. Spec Governance & Hygiene Debt

1. **Changes are merged to `main`, but none are ratified or promoted to canonical specs:**
   - All five changes in `openspec/changes/` remain `Status: draft` with pending `owner-ratification.md`, and `openspec/specs/` holds no canonical capability specs yet.
   - Merged artifacts (`proposal.md`, `design.md`, `PG-04`, `PH-07`, `openpractice-domain-framework.md`) still contain stale cross-branch caveats such as *"PR #3, not yet on main"* and *"branch `change/define-mvp-practice-foundation`, not yet on main"*.

2. **Accumulated "Conscious Exceptions" against `FD-1` and `FD-5`:**
   - `ideation/review/openpractice-framing-decisions.md` still states the August 2026 baseline (`FD-1` *"no routing engines, no approval ladders"*; `FD-5` *"replace approval gates below tier 4 with reversibility"*), forcing newer changes to carry large defensive tension tables (`T-1`–`T-17` in MVP foundation, `T-1`–`T-6` in actors/inbox, `T-1`–`T-12` in patient gateway), 71+ scattered session rulings (with numbers 55–67 recorded in sibling repos), and 45 unratified design calls (`DC-1`–`DC-45`).
   - **Action:**
     1. Publish a **Framing Decisions v2** revision that cleanly incorporates Rulings 1–71 (explicitly establishing the agent-operated frame, fixed safety routing, and tier-2 human confirmations for identity/untrusted patient inputs alongside `FD-5` reversibility).
     2. Complete owner ratification and promote/archive the merged OpenSpec deltas into canonical `openspec/specs/<capability>/spec.md` files before starting Frappe implementation or drafting the Revenue Cycle Core change.

---

## 3. Recommended Next Sequence

1. **Spec hygiene pass:** Fold Rulings 1–71 + approved `DC-*` calls into Framing Decisions v2, clean up *"not yet on main"* references, and ratify/promote the five merged changes into canonical `openspec/specs/`.
2. **Resolve the three cross-spec seams:** Root `Party` vs. `Patient`/`Actor` model, canonical money format across JSON schemas, and `ER-12`/`AH-02` Zero Data Retention wording.
3. **Specify the Revenue Cycle Core:** Appointment & `FD-7` Thread, Service Catalog & Fee Schedule, Money Subledger & Reason Taxonomy, Charge Capture & Claim Pathway Adapter, Remittance Posting, and Patient Balance / Statements / GFE.
4. **Scaffold `open_practice` in `code/`:** Establish the Frappe app, CI contract-test harness against canonical schema digests, and Slice 0 implementation.
