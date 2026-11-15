# Owner Rulings Index: Rulings 1 to 111

Status: navigation — not normative, not ratified
Kind: index
Summary: one row for each of the owner's numbered rulings 1 to 111, with its date, a one-line statement and the repository and change whose design.md "Owner rulings" table records it; decision 80 is open. Written under ruling 108 alongside Framing Decisions v2.
Topics: framing-decisions, owner-rulings
Captured: 2026-11-15

**The "Owner rulings" table in the `design.md` of each change remains the source of truth for every ruling. This index is navigation.** Where a row here and a table differ, the table governs. Rulings are not ratification: every change that records them is a draft until the owner ratifies it.

The rulings were made by **Brett Heap**, the product owner. Rulings 1 to 64 were made on 2026-11-14. Rulings 65 to 111 were made on 2026-11-15; rulings 79 to 98 were accepted in one answer ("accept recomendations. on all but 80. for 80 explain that more") and rulings 99 to 111 in another ("accept your recs for 99-111. explain 80 again"). **Decision 80 is open.**

[Framing Decisions v2](openpractice-framing-decisions-v2.md), a draft for the owner's review, folds in the rulings that bear on a framing decision and cites them by these numbers.

## How each row was written

- **Statement.** For a ruling recorded in a public repository, the statement is taken from a public table, shortened to one line in that table's words. The table it comes from is listed first and in bold under "Recorded in". It is the table of the change the ruling chiefly governs, unless two public tables state the ruling differently in substance; then the fuller one is used, and the row is marked † and explained under "Notes".
- **Private only.** For a ruling recorded only in private repositories, the row gives the number, the date, a neutral topic and where it is recorded, marked *private: topic only*. Nothing else from a private repository is copied here.
- **Rulings 99 to 111.** Rulings 99 to 107 are recorded in public tables on the open draft `opensoft/openPractice-spec#8`, which quote the owner's accepted answer of 2026-11-15 with the question in brief; the statement is taken from them. Rulings 108 to 111 are in no table yet; their statement is the owner's accepted answer, and the row says "no table yet".
- **Recorded in** lists every table found to record the ruling, by the codes below. A table that does not list a number only means the ruling was not relayed to that change.

| Code | Repository | Change |
|---|---|---|
| `OP:mvp` | `opensoft/openPractice-spec` (public) | `define-mvp-practice-foundation` |
| `OP:cfdb` | `opensoft/openPractice-spec` (public) | `define-customer-financial-data-boundaries` |
| `OP:actors` | `opensoft/openPractice-spec` (public) | `define-actors-agents-and-work-inbox` |
| `OP:gateway` | `opensoft/openPractice-spec` (public) | `define-patient-gateway-and-escalation` |
| `OC:boundary` | `opensoft/openChart-spec` (public) | `define-openpractice-boundary` |
| `OC:inbox` | `opensoft/openChart-spec` (public) | `define-clinical-message-inbox` |
| `OC:aiwrite` | `opensoft/openChart-spec` (public) | `define-ai-authored-clinical-records` |
| `OC:acts` | `opensoft/openChart-spec` (public) | `define-clinical-acts-foundation` |
| `OC:rx` | `opensoft/openChart-spec` (public) | `define-prescribing-safety-checks` |
| `HL:gateway` | `opensoft/HealthLinc` (private) | `define-practice-gateway-integration` |
| `MXF:ops` | `MedxSoft/medXfactory` (private) | `define-care-organization-hermes-openpractice-operations` |
| `MXF:conn` | `MedxSoft/medXfactory` (private) | `define-patient-medical-ai-connection` |
| `MXE:overlay` | `MedxSoft/MedxEHR-spec` (private) | `define-medical-ai-record-overlay` |

The four `OP:` changes are merged on `main` as drafts. "(PR #7)" marks a row that is not on `main` but is on the open draft follow-up `opensoft/openPractice-spec#7` (branch `change/follow-up-ruling-93-contract-gaps`). "(PR #8)" marks a row that is only on the open draft `opensoft/openPractice-spec#8` (branch `change/critique-2026-11-15-corrections`, stacked on #7). The `OC:` changes are open drafts on their own `change/<change id>` branches of `opensoft/openChart-spec`. The private changes are cited by repository and change id only.

**Revisions read (2026-11-15).** `opensoft/openPractice-spec`: `main` at `0a82fc2`, `change/follow-up-ruling-93-contract-gaps` at `e746f58` and `change/critique-2026-11-15-corrections` at `957c745`. `opensoft/openChart-spec`: `define-openpractice-boundary` at `ebb3037`, `define-clinical-message-inbox` at `0d56f69`, `define-ai-authored-clinical-records` at `fa0b404`, `define-clinical-acts-foundation` at `28d604f`, `define-prescribing-safety-checks` at `d2fb4a3`. The private changes were read at their branch heads of the same day. Several of these branches were still moving; a ruling recorded after these revisions is not listed under "Recorded in".

## Rulings

| # | Date | Statement | Recorded in |
|---|---|---|---|
| 1 | 2026-11-14 | Handoff shape **A**: an ordered multi-feature exception; the field in `owner-ratification.md` is left for the owner to confirm. | **`OP:mvp`**, `OC:boundary` |
| 2 | 2026-11-14 | Accounting customers are the guarantor account (patient balances) and each payer (insurance balances). Refined by ruling 28: only in `customer-detail` mode. | **`OP:mvp`**, `OC:boundary` |
| 3 | 2026-11-14 | **Superseded by ruling 25.** Kept as history: a patient-balance customer is identified by account number only, and no guarantor or patient name reaches the destination. | **`OP:mvp`**, `OC:boundary` |
| 4 | 2026-11-14 | Accounting dates: line date of service; GL date is the date of service if that period is open, otherwise the first open date with a late-charge flag (FD-3); invoice date is the finalization date; due date from statement terms; the sync timestamp is never an accounting date. | **`OP:mvp`**, `OC:boundary` |
| 5 | 2026-11-14 | Banks: a register of practice accounts and payer deposit links; openPractice reconciles deposits only, reading revenue bank lines from the destination; the accounting system owns bank balances and the full reconciliation. | **`OP:mvp`**, `OC:boundary` |
| 6 | 2026-11-14 | A practice-operated destination (for example self-hosted ERPNext) satisfies the BAA gate with an attestation naming the operator. | **`OP:mvp`**, `OC:boundary` |
| 7 | 2026-11-14 | Posting mode is chosen before a destination's first released batch; after that any mode change is refused until the owner ratifies a continuity procedure; a BAA lapse halts delivery with no silent conversion. Refined by ruling 28. | **`OP:mvp`**, `OC:boundary` |
| 8 | 2026-11-14 | The MVP calls ERPNext only through its REST API, same-site or remote, with no in-process calls; in-process use stays conditional on counsel. | **`OP:mvp`**, `OC:boundary` |
| 9 | 2026-11-14 | A record created in error with no linked activity is outside every retention record family; its hard delete is tier 4 and leaves a PHI-free tombstone. | **`OP:mvp`**, `OC:boundary` |
| 10 | 2026-11-14 | Confidential-communication instructions are in the MVP registry and carried in the link snapshot. | **`OP:mvp`**, `OC:boundary` |
| 11 | 2026-11-14 | Preferred language is added and preferred name stays; sex assigned at birth and sexual orientation stay out (clinical; owned by openChart). | **`OP:mvp`**, `OC:boundary` |
| 12 | 2026-11-14 | Tier 4 applies only to hard delete and to enabling `customer-detail` mode, as a conscious FD-5 exception; `link_existing` and review resolution are tier 2; the FD-5 deadline escape is stated for gates. | **`OP:mvp`**, `OC:boundary` |
| 13 | 2026-11-14 | Coverage is not in link contract 1.0; it may come in a later MINOR. | **`OP:mvp`**, `OC:boundary` |
| 14 | 2026-11-14 | The date-of-birth hold is accepted, implemented as a link-level suspension. | **`OP:mvp`**, `OC:boundary` |
| 15 | 2026-11-14 | The "agree" check for `link_existing` is accepted and made deterministic against the built DocType. | **`OP:mvp`**, `OC:boundary` |
| 16 | 2026-11-14 | Applied name and date of birth are projected onto `OC Patient` with history; `sex_recorded` is never overwritten. | **`OC:boundary`**, `OP:mvp` |
| 17 | 2026-11-14 | openChart refuses direct edits to a linked patient's projected fields and routes them to openPractice as correction requests, intake flows included. | **`OP:mvp`**, `OC:boundary` |
| 18 | 2026-11-14 | The integration role is named and scoped in planning, narrower than System Manager. | **`OC:boundary`**, `OP:mvp` |
| 19 | 2026-11-14 | Namespace tokens `openpractice` and `openchart` are confirmed. | **`OP:mvp`**, `OC:boundary` |
| 20 | 2026-11-14 | Coverages are zero or more; self-pay patients have none. | **`OP:mvp`**, `OC:boundary` |
| 21 | 2026-11-14 | Manual charge entry is allowed for a linked patient while that EHR reports no billable-facts capability; it is marked manual, with provenance. | **`OP:mvp`**, `OC:boundary` |
| 22 | 2026-11-14 | **Superseded by ruling 30.** Kept as history: accrual valuation at the billed charge plus an expected contractual adjustment from the payer's observed history (FD-2), trued up at remittance. | **`OP:mvp`**, `OC:boundary` |
| 23 | 2026-11-14 | Full bank account and routing numbers are stored encrypted, visible only to owner and billing-admin roles, masked elsewhere, every view audited, and never in logs, errors, exports, postings or keys. | **`OP:mvp`**, `OC:boundary` |
| 24 | 2026-11-14 | Membership, subscription, retail and other non-medical commerce invoicing are out of scope. Revised by ruling 26: their routing and ownership belong to `define-customer-financial-data-boundaries`, and the accounting contract never creates a second collectible balance for them. | **`OP:mvp`**, `OC:boundary` |
| 25 | 2026-11-14 | **Supersedes ruling 3.** A medical-billing customer may carry its name and billing address or contact in the destination once its BAA gate is satisfied, only in `customer-detail` mode; clinical content and service-revealing reason categories are always excluded. | **`OP:mvp`**, `OP:cfdb`, `OC:boundary` |
| 26 | 2026-11-14 | **Ownership split.** `define-mvp-practice-foundation` owns the posting mechanics of the accounting contract; `define-customer-financial-data-boundaries` owns customer and party roles, routing by offering, obligation ownership and purpose-bound business views. | **`OP:mvp`**, `OP:cfdb`, `OC:boundary` |
| 27 | 2026-11-14 | Confidential-communication instructions stay in the link snapshot; openChart, and anything it drives (for example HealthLinc), must honor them for every patient communication it originates. | **`OP:mvp`**, `OC:boundary` |
| 28 | 2026-11-14 | **Accounting posture.** `summary-journal` is the MVP default and recommended setup; `customer-detail` is a per-destination opt-in, after the BAA gate and before first release. Refines rulings 2, 7 and 25. | **`OP:mvp`**, `OP:cfdb`, `OC:boundary` |
| 29 | 2026-11-14 | **Demographics.** openPractice is the source for administrative demographics and publishes them to a linked EHR; each system owns its own record. | **`OP:mvp`**, `OC:boundary` |
| 30 | 2026-11-14 | **Revenue recognition; supersedes ruling 22.** Net revenue is the constrained expected entitlement, estimated at the date of service and re-estimated at each close; the practice's accountant approves the policy items (D8) before go-live. | **`OP:mvp`**, `OC:boundary` |
| 31 | 2026-11-14 | **Appointment authority; closes R-7.** openPractice is the system of record for the administrative appointment and the EHR owns the encounter; appointment events are a future MINOR version. | **`OP:mvp`**, `OC:boundary`, `OC:acts` |
| 32 | 2026-11-14 | **Framework D1.** One Party with roles; "customer" is a party role only for membership and retail buyers; a medical-billing accounting customer is derived from the guarantor or payer role. | **`OP:mvp`**, `OP:cfdb`, `OC:boundary` |
| 33 | 2026-11-14 | **Framework D7; FD-5 governs.** Automation stays propose-only; where a tier calls for an approval, release decision or attestation, a named human or an opted-in named AI agent with an openWallet ID may sign; tier 4 stays human-only. | **`OP:mvp`**, `OP:cfdb`, `OC:boundary`, `MXF:ops` |
| 34 | 2026-11-14 | openPractice is the gateway and router: an active patient not on payment hold gets access; clinical questions go to openChart, administrative ones to Hermes or staff; openPractice keeps only a routing record, with no clinical content. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `OC:acts`, `OC:rx`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 35 | 2026-11-14 | For now the physician uses openChart, on mobile or workstation; the openPractice app covers administrative work and approvals; a current-phase decision that can be revisited. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `OC:aiwrite`, `OC:acts`, `OC:rx`, `MXE:overlay` |
| 36 | 2026-11-14 | Amends FD-10(b): the HealthLinc integration contract and escalation routing come into the MVP; HealthLinc's own app features stay on HealthLinc's roadmap. | **`OP:actors`**, `OP:gateway` |
| 37 | 2026-11-14 | Autonomy ladder per action type (observe, propose, act with notice, act after a delay); every action type starts at propose and the practice opts each one up; tier 4 stays human-only; an agent never acts with less friction than the action's FD-5 tier requires. | **`OP:actors`**, `OP:gateway`, `MXF:ops` |
| 38 | 2026-11-14 | Amends FD-1: the agent-operated practice is the primary frame, so screens are built for exceptions and approvals; the 2–4 staff mode remains for when agents are off. | **`OP:actors`**, `OP:gateway`, `MXF:ops` |
| 39 | 2026-11-14 | There is no master patient identity across HealthLinc, openPractice and openChart; HealthLinc links to each backend through that backend's own link contract. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:conn` |
| 40 | 2026-11-14 | Packaging: two new changes; the current four are ratified separately; HealthLinc, openChart and MedxFactory/Hermes each need a companion change, listed and not drafted there. | **`OP:gateway`**, `OP:actors`, `MXF:ops` |
| 41 | 2026-11-14 | A payment hold blocks non-urgent clinical messaging and new non-urgent scheduling only, never urgent escalations, billing contact or own records; placing or lifting it is logged and tiered, and the patient is told; the medical director approves the policy, which needs counsel. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:conn` |
| 42 | 2026-11-14 | "Active" means the patient's registry record is active and, where the practice sells memberships, the membership is current. | **`OP:gateway`**, `OP:actors`, `HL:gateway` |
| 43 | 2026-11-14 | openChart owns the queue and response clock for routed clinical questions; openPractice gets an acknowledgment for each handoff and alerts the office manager if openChart does not acknowledge or the clock runs out. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:conn` |
| 44 | 2026-11-14 | HealthLinc's AI proposes category and urgency and openPractice's routing rules apply them; staff can re-route a misfiled item but cannot answer outside their lane; a mixed question is split; urgency is never downgraded. Amended by ruling 51. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:ops` |
| 45 | 2026-11-14 | Hermes may reply to a patient without a human only with read-only answers that change nothing and give the signed-in patient their own administrative information or practice-approved information; anything that changes something stays a proposal or follows the ladder. | **`OP:actors`**, `OP:gateway`, `OC:inbox`, `HL:gateway`, `MXF:ops` |
| 46 | 2026-11-14 | The read-only answer types are in the MVP agent operations contract (1.0), alongside the registry operations; in the MVP, Hermes answers routine administrative questions. | **`OP:actors`**, `OP:gateway`, `HL:gateway`, `MXF:ops` |
| 47 | 2026-11-14 | Draft the openChart companion: `define-clinical-message-inbox` in `opensoft/openChart-spec`. | **`OP:gateway`**, `OP:actors`, `OC:inbox` |
| 48 | 2026-11-14 | Draft the HealthLinc companion: `define-practice-gateway-integration` in `opensoft/HealthLinc`, a private repository cited by repository and change id only. | **`OP:gateway`**, `OP:actors`, `HL:gateway` |
| 49 | 2026-11-14 | Draft the medXfactory companion: `define-care-organization-hermes-openpractice-operations` in `MedxSoft/medXfactory`. | **`OP:gateway`**, `OP:actors`, `MXF:ops` |
| 50 | 2026-11-14 | Cut-offs other than a payment hold get safeguards: routine clinical messages still route if the access check is unavailable; a lapse or deactivation stops messaging only after notice and a grace period; a clinician can keep access open. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:conn` |
| 51 | 2026-11-14 | HealthLinc's in-app AI is a coach, not a doctor; clinical AI doctors live in medXfactory. Amends ruling 44: the coach or medXfactory's medical AI proposes category and urgency, with conservative defaults (uncertain category clinical, uncertain urgency urgent, never lowered). | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `OC:aiwrite`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 52 | 2026-11-14 | Non-urgent medXfactory medical-AI consults sit behind the practice's access check, and a payment hold may withhold them with the same safeguards; urgent is never withheld; HealthLinc's coach is never gated by the practice. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 53 | 2026-11-14 | The medical AI's exchanges with a patient are recorded in the chart through medXfactory's own governed path (MedxEHR and openChart), never through openPractice. Refined by ruling 70. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `OC:aiwrite`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 54 | 2026-11-14 | HealthLinc's coach gives general information only; anything individualized is handed to medXfactory's medical AI or to the doctor; the not-medical-advice statement is required in addition. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 55 | 2026-11-14 | *private: topic only* — drafting a companion change | `HL:gateway`, `MXF:conn` |
| 56 | 2026-11-14 | In advice mode the medical AI may inform, assess and recommend next steps; it may not prescribe, order, change a treatment plan or record a diagnosis of record. Trimmed by ruling 66. | **`OC:aiwrite`**, `MXF:conn`, `MXE:overlay` |
| 57 | 2026-11-14 | Medical-AI answers reach the patient without prior clinician sign-off; every consult is in the chart for the patient's doctor; the medical director sets a review sample; flagged or high-risk categories are always reviewed. | **`OC:aiwrite`**, `MXF:conn`, `MXE:overlay` |
| 58 | 2026-11-14 | Nothing is switched on for patients until the medical director approves the scope and red-flag rules and a legal and regulatory review is on record. | **`OC:aiwrite`**, `MXF:conn`, `MXE:overlay` |
| 59 | 2026-11-14 | The medical director sets the medical AI's mode: advice only (the default), limited practice or full practice; only the medical director, a named human, sets or changes it, and can narrow or pause at once. † | **`OC:aiwrite`**, `OC:inbox`, `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 60 | 2026-11-14 | Limited practice is an allowlist the medical director approves, each entry an act type with its conditions and the standing order or protocol behind it; anything not listed goes to the human doctor. | **`OC:aiwrite`**, `MXF:conn`, `MXE:overlay` |
| 61 | 2026-11-14 | In full practice a reserved list stays with a human clinician unless the recorded legal review clears it; the AI never manages an emergency in any mode. | **`OC:aiwrite`**, `MXF:conn`, `MXE:overlay` |
| 62 | 2026-11-14 | In the practice modes every AI order, prescription or diagnosis is queued for clinician co-review after the fact, within a window the medical director sets. † | **`OC:aiwrite`**, `OC:acts`, `MXF:conn`, `MXE:overlay` |
| 63 | 2026-11-14 | Build order: advice only, then limited practice, then full practice. | **`OC:aiwrite`**, `MXF:conn`, `MXE:overlay` |
| 64 | 2026-11-14 | The openChart and MedxEHR AI write-path changes are drafted now ("draft the openChart and MedxEHR AI write path changes too"). | **`OC:aiwrite`**, `OC:inbox`, `MXF:conn`, `MXE:overlay` |
| 65 | 2026-11-15 | The chart keeps its own record of the medical director's authorization and checks every AI write against it; design call AD-1 is binding. | **`OC:aiwrite`**, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 66 | 2026-11-15 | Advice-only is a patient-consult class: answers may reach the patient without prior sign-off, giving information, assessment and next steps with no diagnosis language or treatment directives, with AI disclosure and red-flag routing; the medical director's sampled review replaces review-before-send for this class only. Trims ruling 56. † | **`OC:inbox`**, `OC:aiwrite`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 67 | 2026-11-15 | The ratified texts stand, among them openChart intake's bar on any prescription, diagnosis or order created by an intake, API response or extension event; limited and full practice stay specified but off, revisited only through a future governed amendment after advice-only has run and counsel's review is in. † | **`OC:aiwrite`**, `OC:inbox`, `OC:acts`, `OC:rx`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 68 | 2026-11-15 | Hermes's administrative patient messages are separated from clinical ones in medXfactory's local policy; they follow ruling 45's guardrails with no clinical review; consent and data-sharing conditions still apply. | **`OP:gateway`**, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 69 | 2026-11-15 | openChart owns one neutral conversation-retrieval contract, `openchart-conversation-retrieval/1.0`, which HealthLinc and medXfactory implement. | **`OC:inbox`**, `OP:gateway`, `OC:aiwrite`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 70 | 2026-11-15 | The medical AI's records reach openChart through MedxEHR, which relays them with receipts; medXfactory does not write to openChart directly. Refines ruling 53. | **`OC:aiwrite`**, `OP:gateway`, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 71 | 2026-11-15 | When a patient writes straight to the doctor with no AI triage, HealthLinc asks a short red-flag checklist: any red flag proposes `urgent` with emergency guidance, none `routine`, a skipped checklist `priority`; the patient can raise urgency but never lower it. | **`OP:gateway`**, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 72 | 2026-11-15 | The clinical-acts foundation is drafted now, as `define-clinical-acts-foundation`; it accepts only `clinician` as author type and provides the signing and co-signature model that Tier 2 builds on. † | **`OC:aiwrite`**, `OC:acts`, `MXE:overlay` |
| 73 | 2026-11-15 | The clinical-acts foundation's scope is encounters, clinical notes, diagnoses, procedures, orders, prescriptions, and authorship, signing and amendment; results ingestion, care and treatment plans, immunizations, clinical decision support and electronic prescribing are left for later changes. † | **`OC:acts`**, `OC:aiwrite`, `OC:rx` |
| 74 | 2026-11-15 | The prescription record is specified in the foundation, but the prescription feature is not buildable until allergy and interaction checking is specified; no prescription can be signed where that gate is not met. | **`OC:acts`**, `OC:aiwrite`, `OC:rx` |
| 75 | 2026-11-15 | "Draft the allergy and interaction checking change too." | **`OC:rx`**, `OC:acts` |
| 76 | 2026-11-15 | When the knowledge source is unavailable at signing, a clinician may sign only with a recorded statement that checks were unavailable and a manual review was done; the prescription is flagged and re-checked; a non-clinician author, including any future AI author, can never use that path. | **`OC:rx`**, `OC:aiwrite`, `OC:acts` |
| 77 | 2026-11-15 | The first checks are allergy, drug–drug interaction and duplicate therapy, with drug–supplement interaction only where the source declares support; dose range, drug–condition, pregnancy and lactation, renal and hepatic, paediatric and geriatric rules come in later changes. | **`OC:rx`**, `OC:acts` |
| 78 | 2026-11-15 | openChart bundles no drug knowledge; each practice connects a licensed or verified source through the adapter contract, and no prescription can be signed without one. | **`OC:rx`**, `OC:acts` |
| 79 | 2026-11-15 | *private: topic only* — scope of an existing requirement | `MXF:conn`, `MXE:overlay` |
| 80 | open | **open: a question about a ratified rule in a private repository** | not ruled |
| 81 | 2026-11-15 | "'Next steps' means how to get care and general self-care information: see your doctor, book a visit, go to urgent care. It never means a treatment instruction for the patient's own condition." | **`OC:aiwrite`**, `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 82 | 2026-11-15 | A verified source that is stale or down uses the outage path, with the medical director alerted; signing stops after a staleness limit set by policy; a practice that never had a verified source cannot sign. | **`OC:rx`**, `OC:acts` |
| 83 | 2026-11-15 | Prescriptions for controlled substances, or with an unknown classification, are refused in the prescribing-checks change; controlled-substance prescribing goes into a later change alongside electronic prescribing. | **`OC:rx`**, `OC:acts` |
| 84 | 2026-11-15 | Read openChart's ratified bar on an "API response" creating a prescription, diagnosis or order narrowly: it bars automatic creation from intake, and an alert to the clinician is not a treatment recommendation; add a small clarifying amendment to the ratified text. | **`OC:acts`**, `OC:inbox`, `OC:aiwrite`, `OC:rx` |
| 85 | 2026-11-15 | Allow the clinical-acts foundation the same ordered multi-feature exception that openPractice was given in ruling 1. | **`OC:acts`**, `OC:inbox` |
| 86 | 2026-11-15 | Before a HealthLinc conversation is escalated, the patient confirms which turns are shared; the escalated question is always shared; for urgent and priority items the turns that triggered the escalation are always shared and the patient is told. | **`OC:inbox`**, `HL:gateway` |
| 87 | 2026-11-15 | openChart publishes only confirmed diagnoses to openPractice as billable facts; if a published diagnosis is later refuted, a retraction is sent. | **`OP:mvp (PR #7)`**, `OC:boundary`, `OC:acts` |
| 88 | 2026-11-15 | After a patient link is revoked or superseded, retractions of facts already published under it are still delivered, as pointers only; no new facts and no reads are served. | **`OP:mvp (PR #7)`**, `OC:boundary`, `OC:acts` |
| 89 | 2026-11-15 | Add a retraction reason code to the clinical-facts contract now, while 1.0 is still unratified, in a small openPractice-spec follow-up. | **`OP:mvp (PR #7)`**, `OC:boundary`, `OC:acts` |
| 90 | 2026-11-15 | Keep quarantining every consult that fails an authorization check, mismatches included: "Whatever the patient was told should be in front of a clinician, whichever check failed." | **`OC:aiwrite`**, `OC:inbox`, `MXF:conn`, `MXE:overlay` |
| 91 | 2026-11-15 | As revised: "medXfactory keeps it and resends; MedxEHR stores nothing", because MedxEHR must store no conversation text. | **`OC:aiwrite`**, `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 92 | 2026-11-15 | Inbox authority lives in the clinical-acts foundation's single grant model, set by the medical director; clinical roles and authority become their own small first feature. | **`OC:acts`**, `OC:inbox`, `OC:aiwrite` |
| 93 | 2026-11-15 | One draft follow-up PR to the merged openPractice drafts, carrying the agent operations Hermes lacks and the consent check, a verifiable entitlement, a clinician-acknowledged status, the representative's relationship and authority, the retraction reason code, and an erratum to the gateway change's companion list. | **`OP:actors (PR #7)`**, `OP:mvp (PR #7)`, `OP:gateway (PR #7)`, `OC:boundary`, `OC:inbox`, `OC:acts`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 94 | 2026-11-15 | *private: topic only* — a medical-AI consult safeguard | `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 95 | 2026-11-15 | The chart's grant is the root of the medical AI's authority; medXfactory's configuration can only narrow it, and a pause in the chart stops non-urgent answering at once. | **`OC:aiwrite`**, `OC:inbox`, `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 96 | 2026-11-15 | MedxEHR registers the patient's chart link when the patient enrols for medical-AI consults; no link, no non-urgent consult. | **`OC:aiwrite`**, `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 97 | 2026-11-15 | Hermes needs no per-patient consent before a model provider processes administrative data: the provider agreement is enough for practice operations, with counsel to confirm; consent applies to AI answering the patient directly. | **`OP:actors (PR #7)`**, `OP:gateway (PR #7)`, `HL:gateway`, `MXF:ops` |
| 98 | 2026-11-15 | The first medical director is confirmed by a second named person; a one-administrator practice records licence verification against the board's public record. | **`OC:acts`**, `OC:inbox`, `MXF:conn` |
| 99 | 2026-11-15 | May Hermes read the administrative question it is answering? "Yes. It cannot answer what it cannot read. Clinical text and documents stay refused." | **`OP:actors (PR #8)`**, `MXF:ops` |
| 100 | 2026-11-15 | A request mode below the rung set for an agent: "Not in version 1. Lower the rung in openPractice instead." | **`OP:actors (PR #8)`**, `MXF:ops` |
| 101 | 2026-11-15 | Alert when an urgent item has no clinician acknowledgment in time: "Yes: alert the office manager. It changes no clinical path." | **`OP:gateway (PR #8)`**, `OC:inbox` |
| 102 | 2026-11-15 | What "counsel to confirm" means in ruling 97: "Design on your answer now, and make counsel's confirmation a go-live condition." | **`OP:actors (PR #8)`**, `OP:gateway (PR #8)`, `HL:gateway`, `MXF:ops` |
| 103 | 2026-11-15 | One money format for every contract: "Decimal text plus a currency code on the wire, as the accounting contract already does. Whole cents in storage." | **`OP:mvp (PR #8)`** |
| 104 | 2026-11-15 | What model providers may keep: "Nothing: zero data retention, verified the way the BAA is. Deletion with a receipt applies to the agent operator only." | **`OP:actors (PR #8)`**, `OP:gateway (PR #8)`, `MXF:ops` |
| 105 | 2026-11-15 | Mobile approver sign-in: "Face, fingerprint or passkey for both steps, never a typed password. Opening skips the prompt if you verified in the last 60 seconds. Every sign-off still needs its own." | **`OP:gateway (PR #8)`** |
| 106 | 2026-11-15 | A mixed question found late: "Keep deleting its text from openPractice. Make the administrative part come back as the same question, and keep a reminder on the lane so it is never lost." | **`OP:gateway (PR #8)`** |
| 107 | 2026-11-15 | One person in several roles: "Allowed, with each sign-off recording the role used. A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change." | **`OP:mvp (PR #8)`** |
| 108 | 2026-11-15 | Framing Decisions v2: "I draft a v2 for your review that folds in rulings 1–98 and adds an index of every ruling. Version 1 stays as history." | no table yet. Carried out by this index and the v2 draft beside it. |
| 109 | 2026-11-15 | Promote to canonical specs before building? "No. Ratify first, which is yours to do, then archive each change after its feature lands." | no table yet |
| 110 | 2026-11-15 | Write the revenue-cycle core next and pause perimeter work: "Yes. Its scope and decisions will follow as their own list." | no table yet |
| 111 | 2026-11-15 | Add the critique file to the spec repo: "Yes, in the corrections PR." | no table yet |
## Notes

**Dates of rulings 65 to 71.** The table in `OP:gateway` gives one source date, 2026-11-14, for every row, including rows 68 to 71. Every other table that records rulings 65 to 71 dates them 2026-11-15. This index follows the majority; the owner can confirm.

**Rows marked †** (two public tables state the ruling differently in substance; the fuller wording is used):

- **59.** `OC:inbox` adds that the medXfactory change owns the mode framework and its gates. `OC:aiwrite` adds that advice only is the default, that only the medical director sets or changes the mode, and that he can narrow or pause at once.
- **62.** `OC:acts` records clinician co-review without "after the fact, within a window the medical director sets", which `OC:aiwrite` records.
- **66.** `OC:aiwrite` omits the AI disclosure, the red-flag routing and "for this class only", which `OC:inbox` records.
- **67.** `OC:inbox` records "The never-grantable rule stays"; `OC:acts` records "No AI is granted clinical authority now"; `OC:aiwrite` names the ratified texts that stand and the condition for revisiting the practice modes.
- **72.** `OC:acts`, the change the ruling chiefly governs, records only the instruction to draft it. `OC:aiwrite` adds that the foundation accepts only `clinician` as author type and provides the signing and co-signature model.
- **73.** `OC:rx` adds that the prescribing-checks change is the narrow, required exception that ruling 74 names. `OC:acts`'s wording is used.

**Superseded rulings.** Ruling 3 is superseded by ruling 25, and ruling 22 by ruling 30. Both are kept as history in `OP:mvp`.

**Counts.** 111 rows. Three are *private: topic only* (55, 79 and 94). One is open (80). Four rulings are in no table yet (108 to 111).

## Relationships

Navigation over the "Owner rulings" tables of the changes listed above, which remain the source of truth. Companion to [Framing Decisions v2](openpractice-framing-decisions-v2.md), a draft for the owner's review, and to version 1, [Framing Decisions](openpractice-framing-decisions.md), which stays unchanged as history. Written under ruling 108.
