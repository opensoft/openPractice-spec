## Context

openPractice is an assembly root whose code leg is empty. Constitution v1.0.0 (`.specify/memory/constitution.md` in `opensoft/openPractice`, ratified 2026-08-24) is in force. Its principles are I (HIPAA by design), II (security review), III (test-first), IV (clinical boundary), V (versioned contracts) and VI (portability). The framing decisions FD-1 to FD-11 were ratified on 2026-08-27. FD-5 is the governance primitive: four fixed tiers, approval kept only for tier 4, and "the second human does not exist; approval queues rot within a month."

Five OpenSpec changes are merged on `main` at `0a82fc2`, all `Status: draft` and unratified. Each fails closed at its own gates. The written critique of 2026-11-15, item C.1 ("Fail-Closed Cold-Start Paralysis"), says that together they leave a small practice unable to go live until four authorities have signed nearly a dozen artifacts, with no statement of how those roles map onto one physician-owner. Its recommended action: keep fail-closed runtime enforcement, add one readiness checklist and a policy starter kit, and define the role mapping for one person in several roles.

The owner ruled on that finding on 2026-11-15 (ruling 107, below). This change encodes ruling 107 and nothing more. **Rulings are not ratification.** This change stays `Status: draft`, and `owner-ratification.md` stays PENDING until the owner completes it.

Requirement IDs `RC-nn` (`readiness-checklist`), `SK-nn` (`policy-starter-kit`) and `SR-nn` (`signer-roles`) prefix every requirement name in the three spec deltas. Requirement IDs of other changes are cited as those changes define them; a requirement of `add-reimbursement-retention-control` or `define-customer-financial-data-boundaries`, which carry no IDs, is cited by its name.

### Sources

- The five merged changes on `main`, read in full.
- Draft PR opensoft/openPractice-spec#7 (branch `change/follow-up-ruling-93-contract-gaps`, head `e746f58`), read at its head for the three changes it edits.
- The corrections change, draft PR opensoft/openPractice-spec#8 (branch `change/critique-2026-11-15-corrections`, stacked on PR #7), appeared on the remote while this change was being drafted and was read at its head `957c745`. It is still being written, so this change cites its requirement IDs only, never its line numbers.
- [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md): FD-1, FD-5 and the 2026-11-14 amendments.
- [`ideation/brainstorm/openpractice-domain-framework.md`](../../../ideation/brainstorm/openpractice-domain-framework.md) §5A.4 and D8: the accountant's approval items. Proposal status; ruling 30 adopted D8.
- The critique, `ideation/review/openpractice-spec-critique-2026-11-15.md`. Draft PR #8 adds it to the repository under ruling 111; it is not on `main`, so it is not cited by link.
- For ruling 98 only: the public `opensoft/openChart-spec` change `define-clinical-acts-foundation` (a draft, not merged; read at `28d604f`), located through the owner-rulings index on draft PR #9 (`docs/framing-decisions-v2`), which is navigation and not normative.

## Owner rulings that govern this change

Rows 107, 98, 102, 104, 109 and 110 have the source **Brett Heap, 2026-11-15**: 98 among the decisions 79 to 98 accepted that day except 80, and 102 to 110 among the decisions 99 to 111 accepted that day in answer to the critique ("accept your recs for 99-111. explain 80 again"). The other rows have the source **Brett Heap, 2026-11-14, authoring session**, and are quoted from the "Owner rulings" table of the change named. **Rulings are not ratification.**

| # | Ruling (accepted words, or the governing sentence of the table row) | Governs here |
|---|---|---|
| 107 | (One person in several roles.) "Allowed, with each sign-off recording the role used. A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change." | This change: SR-01 to SR-04, RC-01 to RC-07, SK-01 to SK-06 |
| 98 | (Confirming the first medical director.) "A second named person confirms; a one-administrator practice records licence verification against the board's public record." | The model one-person alternative (SR-03); P-4 below. No requirement in this repository encodes it; openChart's `define-clinical-acts-foundation` AU-01 does, for its own designation |
| 102 | (What "counsel to confirm" means in ruling 97.) "Design on your answer now, and make counsel's confirmation a go-live condition." | RI-47, encoded in the corrections change's AH-06 and AH-01; SR-04 applies to the confirmation |
| 104 | (What model providers may keep.) "Nothing: zero data retention, verified the way the BAA is. Deletion with a receipt applies to the agent operator only." | RI-46, encoded in the corrections change's AH-02 and AH-01 |
| 109 | (Promote to canonical specs before building.) "No. Ratify first, which is yours to do, then archive each change after its feature lands." | `tasks.md` §4; no canonical promotion |
| 110 | (Write the revenue-cycle core next and pause perimeter work.) "Yes." | T-7: this change adds no contract and no perimeter behavior |
| 97 | (2026-11-15, recorded in PR #7.) "No; the provider agreement is enough for practice operations, with counsel to confirm. Consent applies to AI answering the patient directly." | The confirmation ruling 102 makes a go-live condition; RI-26 |
| 6 | "A practice-operated destination (for example self-hosted ERPNext) satisfies the BAA gate with an attestation naming the operator." (`define-mvp-practice-foundation`) | RI-07 |
| 12 | "Tier 4 applies only to hard delete and to enabling `customer-detail` mode, as a conscious FD-5 exception (T-13)." (`define-mvp-practice-foundation`) | RI-08 |
| 30 | "D8: the practice's accountant approves the reporting basis, estimator, classification, materiality and reopening policy before go-live." (`define-mvp-practice-foundation`) | RI-01, RI-02, SR-04, starter template 8 |
| 33 | "Agent signing is opt-in per action type and never on by default. Every sign-off records the signer's identity, including an agent's openWallet ID, with provenance. Tier 4 stays human-only." (`define-mvp-practice-foundation`) | SR-02 extends this record with the role; RI-22 |
| 37 | "Every action type starts at propose, and the practice opts each one up. Tier 4 stays human-only." (`define-actors-agents-and-work-inbox`) | RI-22, SK-02, SK-04, starter template 6 |
| 41 | "The medical director approves the hold policy, and the exact policy needs the practice's counsel." (`define-patient-gateway-and-escalation`) | RI-32, P-3, starter template 1 |
| 45 | "each answer type is switched on by the practice" (`define-actors-agents-and-work-inbox`) | RI-24, RI-25, starter template 9 |
| 50 | "A lapsed membership or a deactivation stops messaging only after the patient is told and a policy-set grace period has passed." (`define-patient-gateway-and-escalation`) | RI-33, starter template 4 |

## Goals / Non-Goals

**Goals**

- One place that shows every go-live gate and policy sign-off the other changes require, with its enforcing requirement, authority, holder, state and consequence.
- A starter template for each policy a gate needs, inert until signed, and signed exactly like a policy written from scratch.
- Ruling 107 encoded exactly: several roles per person, the role on every sign-off, one-person alternatives for two-person rules, and no self-approved accountant or counsel sign-off.

**Non-Goals**

- The text of any policy, template, agreement or consent form, and any legal, accounting or clinical rule.
- Weakening a gate, adding a gate, or changing who signs a gate, beyond ruling 107's role record and self-approval bar.
- Editing another change. Every amendment below is proposed for its owner.
- A new contract.

## Decisions

### 1. One checklist that reads the gates and never stands in for them

A readiness item is anything an existing requirement makes a precondition of a function, recorded once per practice, counterparty, destination, agent or version: a policy version, an approval, a verification, an agreement record, a roster or coverage fact, or a configuration fact (design call RD-1). A sign-off made once per transaction, such as a batch release or a link confirmation, is a work item in the inbox (WI-07) and is not a row. Items the product owner decides at release, such as the grant sets of the new recipes, are shown read-only in their own section (RD-2).

Each row reads its state from the gate's own record (RC-03). The checklist keeps no state that a gate consults, offers no write path except the gate's own action, and enables nothing. Its rows follow the requirements (RC-02): the inventory below is the starting set, a later change that alters a gate alters its row, and a ruling that no requirement encodes produces no row.

### 2. A starter kit of inert, versioned templates

The kit holds one template for each policy, profile or plan a readiness gate needs (SK-01). A template is never in force (SK-02). A practice copies it into its own draft, and the authority the gate names approves that draft through the gate's own action, with the same record as a policy written from scratch (SK-03). The version records the template it came from as provenance only (RD-6). Nothing is adopted by default or in bulk (SK-04). A published template version never changes, and a newer one never touches a policy in force (SK-05). Templates assert no rule and may show only values a requirement already states (SK-06, RD-7).

### 3. Roles, and the two-person rules

A role is a recipe, an actor type, or a capacity a requirement names (RD-9). One person may hold several, and each sign-off records the one role it was given in (SR-01, SR-02, RD-10). A rule that needs two different people fails closed until its owning change names a one-person alternative (SR-03). A rule that only loses its meaning when one person signs both sides keeps working, with every role recorded and the sign-off shown on the checklist (SR-03, RC-06). The accountant's and counsel's sign-offs are never self-approved, and no one-person alternative applies to them (SR-04, RD-11).

The one-person alternatives below are chosen in this order of preference (RD-13): another eligible named human, where the roster has one; an independent external record, as ruling 98 uses the board's public record; and, where neither exists, the sign-off given in both roles, each recorded and shown on the checklist.

## The inventory

Every row is a readiness item under RD-1. "Authority" is in the enforcing requirement's own words. "Two people today?" asks whether an existing rule, or ruling 107, needs two different people for the item. "One-person alternative" is PROPOSED unless it is ruling 107's own text. RI numbers are this design's; the checklist uses the enforcing requirement IDs.

### A. Accounting go-live (`define-mvp-practice-foundation`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-01 | Revenue-recognition policy: reporting basis, estimator, receivable classification, materiality approach, reopening policy (D8) | AI-20, AI-12, BAB-17 (restated in WI-03, AO-04, MA-07) | The practice's accountant only; never an agent | No destination goes live | Yes, by ruling 107: the accountant may not be self-approved | None (SR-04): an accountant outside the practice signs |
| RI-02 | Estimator versions: the first, then each new one | AI-12, AI-20, BAB-17 | The practice's accountant only; never an agent | That version produces no estimate; the first is needed for go-live | As RI-01 | As RI-01 |
| RI-03 | Destination verification: line service dates and cash-basis reporting | AI-20 | Not named ("its adapter SHALL verify and record"); a destination without line service dates waits for "the owner" before customer-detail | That destination's go-live | No | — |
| RI-04 | Reason taxonomy defined before any entry posts | BAB-06 (the categories are separately governed) | Not named | No ledger entry posts | No | — |
| RI-05 | Account mapping per destination and posting mode | BAB-06; governance tiers ("Change an account mapping", tier 2) | No sign-off; tier 2 with notification | Release refused for any batch with an unmapped line | No | — |
| RI-06 | Legacy-collections account, for a practice with legacy run-off | BAB-08; Open Question 4 | "The bookkeeper decides" | That practice's first release | No | — |

### B. Customer-detail posting, optional (`define-mvp-practice-foundation`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-07 | Verified BAA, or operator attestation, for the destination | AI-14; consulted by `define-customer-financial-data-boundaries` ("Accounting receives a limited customer and financial record"; "Membership and retail obligations use connected business ledgers") | "An authorized person" records and verifies | Customer-detail mode; PHI-bearing customer records in that destination; activating a PHI-bearing offering against it | No | — |
| RI-08 | Enabling customer-detail mode, before the first release | AI-09, AI-14, BAB-17 | Named human only, tier 4 | The destination stays in `summary-journal` | No | — |
| RI-09 | Recipient profile for the destination | AI-10, AI-16, AI-23 | Not named (each field's purpose, recipient and authority documented) | Customer-detail batches, each of which records its profile version; any field beyond the default | No | — |

### C. Memberships, routing and business outputs (`define-customer-financial-data-boundaries`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-10 | Offering routes and approved membership terms | BAB-16; "Offerings determine financial routing at line level"; "Membership and retail obligations use connected business ledgers" | Not named ("approved terms") | In a practice that sells memberships, every member's charge is held; posting with no owner or mapping is held as review work | No | — |
| RI-11 | Recipient profile for each business output | "Every business output has a scoped recipient profile" | Not named | That projection or export | No | — |
| RI-12 | Outreach policy | "Audience analysis does not authorize outreach" | Not named ("the deployment's approved policy") | The affected disclosure or send | No | — |
| RI-13 | Vendor arrangements, including BAAs, for outsourced accounting and billing firms | "Accountant and biller access are independently bounded" | Not named | Not stated by the requirement | No | — |

### D. Retention and lifecycle evidence (`add-reimbursement-retention-control`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-14 | A jurisdiction selected for each record family | "Unselected jurisdiction blocks duration automation" | Not named | Automated retention duration, disposition horizon and elapsed-time disposition | No | — |
| RI-15 | Retention policy profile versions approved, including any exact Medicaid profile | "Retention policy profiles are versioned and sourced"; "Exact Medicaid duration profiles remain disabled until fully qualified and approved" | "Human approval" and a "review owner", neither named | That profile creates no obligation; a Medicaid profile stays disabled | Two roles named, not stated as two people | One person may hold both, each role recorded (SR-02); no amendment |
| RI-16 | A neutral public-envelope profile selected and pinned, optional | "Public envelopes conform to a selected version-pinned neutral profile" | Not named | Public witnessing | No | — |

### E. Agents (`define-actors-agents-and-work-inbox`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-17 | Agent operator BAA | AH-02, AH-01 | "An authorized person" records and verifies; "counsel confirms each agreement's form" | Every agent grant at any rung; all PHI to that path | Yes, by ruling 107: counsel may not be self-approved | None for counsel (SR-04) |
| RI-18 | Model-provider BAA for each provider on the path, with both agreements in a chain | AH-02, AH-01 | As RI-17 | As RI-17 | As RI-17 | As RI-17 |
| RI-19 | Recipient profiles for the operator and each provider, per action type | AH-03, AH-01 | Not named | Every agent grant; fields outside the profile are withheld | No | — |
| RI-20 | Agent logging and untrusted-content controls recorded | AH-04, AH-05, AH-01 | Not named | Every agent grant | No | — |
| RI-21 | Agent roster entry: name, openWallet ID, active sponsoring human, dates | AM-04; governance tiers (tier 2) | Practice manager; named human only | The agent; without an active sponsor it drops to `observe` | No | — |
| RI-22 | Grants per action type, and rungs above `propose`, which are the BAB-17 agent-signing opt-ins | AO-03, AO-04, BAB-17 | Practice manager, or controller or office manager; named human only; tier 2 | The agent acts on nothing it holds no grant for, and every action type stays at `propose` | Loses meaning when the granter is the sponsor AO-03 notifies (P-8) | RA-3 |
| RI-23 | Rate and blast-radius limit values | AO-08; design call D-9; Open Question 2 | Set in planning; changed by the practice manager or the controller or office manager, tier 2 | Fail closed; per Open Question 2, opt-ins above `propose` | No | — |

### F. Agent answers to patients (`define-actors-agents-and-work-inbox`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-24 | Read-only answer type and template versions | AH-05 exception 1 (conditions 1, 4 and 5); AO-11 | "A named human"; anything health-adjacent also the medical director; never opted in | That answer type sends nothing; its items go to staff | Two sign-offs for a health-adjacent type (P-6) | RA-4: one person gives both, each in its role |
| RI-25 | Answer type switched on, with its cancel window and sampled-audit rate | AH-05 exception 1 (conditions 6, 7 and 10); AO-04; Open Question 5 | Practice manager, or controller or office manager; named human (AO-03) | The answer type stays at `propose` | The audit may be done by the template's own approver (P-10) | Roles recorded; no amendment |
| RI-26 | Consent to AI-authored administrative messaging: its form and wording, and the record design | AH-06 (PR #7); Open Question 9 (PR #7) | "Counsel confirms the consent's form and wording"; the owner dispositions D-22, D-23 and D-27 to D-29 | No agent answer reaches a patient with no recorded grant; per Open Question 9, no answer type can be switched on | Yes, by ruling 107, for counsel | None for counsel (SR-04) |

### G. Remote VAs (`define-actors-agents-and-work-inbox`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-27 | VA company BAA | VA-01 | "An authorized person" records and verifies | Every VA's PHI access | No | — |
| RI-28 | VA device, session and location rules | VA-04 | "The practice approves"; exact rules set in planning | VA access, which fails closed | No | — |
| RI-29 | VA roster entries: named individuals under their company, each with an end date | VA-04, VA-05; governance tiers | Practice manager; named human | Access ends on the end date; no shared logins | No | — |

### H. Patient gateway (`define-patient-gateway-and-escalation`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-30 | HealthLinc operator agreement | PG-15 | "An authorized person" records and verifies; "counsel decides what agreement is required and its form" | Every gateway message is refused | Yes, by ruling 107, for counsel | None for counsel (SR-04) |
| RI-31 | Proxy policy | PG-06; DC-12 | Medical director approves; counsel's review recorded; never opted in (DC-28) | Every representative link is refused | Yes: the medical director and counsel are two people (P-3) | None for counsel (SR-04) |
| RI-32 | Hold policy | PH-03 | Medical director approves; counsel's review recorded before approval; never opted in | No hold can be placed | Yes, as RI-31 (P-3) | None for counsel (SR-04) |
| RI-33 | Messaging-continuity policy | PG-04; DC-31 | Medical director; never opted in | Protective: a deactivation or a lapse withholds nothing ruling 50 protects | No | — |

### I. Clinical routing (`define-patient-gateway-and-escalation`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-34 | On-call roster covering every hour, or naming the fallback | ER-13; DC-27 | Not named (practices configure it; the medical director is the fallback recipient) | Clinical routing, and so clinical and urgent intake, refused under PG-10 | Loses meaning when the on-call clinician is the medical director (P-9) | RA-2 |
| RI-35 | Second channel for tracked alerts | ER-09; DC-29; Open Question 13 | Set in planning; not named | Enabling clinical routing (gateway `tasks.md` §3.5) | No | — |
| RI-36 | Routing-clock policy, with the alert-acknowledgment window, the re-send interval and the clinician-acknowledgment alert window | ER-08, ER-09; ER-20 (corrections change); DC-3, DC-29, DC-58 | Medical director; never opted in | Nothing: the fail-closed default windows apply | No | — |

### J. Relying parties (`define-patient-gateway-and-escalation`, PR #7)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-37 | The relying party's operator agreement | PG-16 (PR #7); DC-47 | "An authorized person" records and verifies "the agreement counsel requires" | No assertion for that audience, so no non-urgent consult with that relying party | Yes, by ruling 107, for counsel | None for counsel (SR-04) |
| RI-38 | Relying-party registration: issuer, capabilities, key-set location and pin | PG-16 (PR #7); governance tiers (PR #7) | Practice manager, or controller or office manager; named human; tier 2 | As RI-37 | No | — |

### K. Risk-analysis entries that requirements name

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-39 | The VA screen-capture residual risk; operator and provider retention; records a connected ledger writes outside openPractice | VA-02; AH-04; "Membership and retail obligations use connected business ledgers" | Not named | Not stated: nothing waits on them | No | — |

### L. Release prerequisites, decided by the product owner and shown read-only (RD-2)

| # | Item | Enforced by | Authority | What stays off until done |
|---|---|---|---|---|
| RI-40 | Grant sets of the five new recipes: remote VA, nurse, medical director, controller or office manager, agent | AM-02; D-1 | "The owner", before the recipe ships | Each recipe grants nothing, so every sign-off that the medical director or the controller or office manager gives waits on it |
| RI-41 | Identity verification mechanism for gateway links | PG-05; gateway Open Question 1 | Decided in planning | Gateway link go-live |
| RI-42 | Retention decision for administrative communication records | PG-10; gateway Open Question 10 | The owner and counsel | Gateway Feature 1 go-live (gateway `tasks.md` §3.5) |
| RI-43 | The assertion's pin form, registration record, key-removal authority and policy values | PG-16 (PR #7); gateway Open Question 18 (PR #7) | Planning; the owner approves | Issuing assertions |
| RI-44 | The AI-authorship disclosure duty | PG-11; DC-18; gateway Open Question 9 | Counsel, or the owner accepting DC-18 | Hermes drafted replies |
| RI-45 | The agent-operations MINOR for drafted replies and the hold action types | DC-22; gateway Open Question 11 | Change A's owner | Agents get no drafted-reply or hold action type |

### M. Agent go-live conditions in the corrections change (draft PR #8, stacked on PR #7, not merged)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-46 | Zero data retention at each model provider, for the endpoints and models it covers, verified the way the BAA is (ruling 104) | AH-02, AH-01 (corrections change); D-30 | "An authorized person" records and verifies it on the BAA pattern | No PHI reaches that provider, endpoint or model, and no agent grant is made on that path | No | — |
| RI-47 | Counsel's confirmation of ruling 97's answer: the provider agreement is enough for practice operations (ruling 102) | AH-06, AH-01 (corrections change); D-31 | "An authorized person SHALL record counsel's confirmation", naming the confirming counsel | No PHI reaches any model provider, and no agent grant is made on any path that uses one | Yes, by ruling 107, for counsel | None for counsel (SR-04) |

### Rulings that no requirement here encodes yet (no rows until one does, RC-02)

| # | Item | Ruling | Expected home | What it would keep off |
|---|---|---|---|---|
| — | Confirmation of the first medical director | 98 | Not in this repository: not on `main`, in PR #7 or in PR #8 at `957c745`. Encoded in openChart: `opensoft/openChart-spec` `define-clinical-acts-foundation` AU-01 | openChart's designation of the first medical director. No openPractice function waits on it (Open Question 5) |

### The critique's citations, checked against the requirements

The critique's item C.1 cites fourteen requirement IDs. Each was read on `main`; the inventory follows the requirements, not the critique (RC-02).

| Cited ID | The critique's attribution | What the requirement says | Inventory |
|---|---|---|---|
| BAB-02 | Accountant: D8 policy and estimator | The accounting system is the books of record; it names no sign-off. The accountant-only D8 and estimator approvals are in AI-12, AI-20 and BAB-17 | RI-01, RI-02, through AI-12, AI-20 and BAB-17 |
| AI-06 | Accountant: D8 policy and estimator | A person releases each export batch, which is frozen at release: a per-transaction sign-off, not an accountant gate | Not a row (WI-07) |
| MA-07 | Accountant: D8 policy and estimator | Mobile sign-offs follow FD-5 and BAB-17, with re-authentication at signing; it restates that the D8 and estimator approvals are the accountant's only. PR #8 gives each sign-off its own verification (ruling 105) | Cited on RI-01 |
| PH-03 | Counsel; medical director | The medical director approves; counsel's review is recorded before approval | RI-32 |
| PG-06 | Counsel; medical director | The medical director approves; counsel's review is recorded on the policy | RI-31 |
| PG-15 | Counsel | An authorized person records and verifies the operator agreement; counsel decides what agreement is required and its form | RI-30 |
| AI-14 | Counsel | An authorized person records and verifies a BAA or an operator attestation; counsel is not named | RI-07 |
| AH-02 | Counsel | An authorized person records and verifies the BAAs; counsel confirms each agreement's form. PR #8 adds verified zero data retention (ruling 104) | RI-17, RI-18, RI-46 |
| VA-01 | Counsel | An authorized person records and verifies the VA company's BAA; counsel is not named | RI-27 |
| ER-08 | Medical director | The medical director's policy sets the windows, and fail-closed defaults apply until it does, so nothing waits on it | RI-36 |
| PG-04 | Medical director | Defines an active patient; the messaging-continuity policy the medical director approves (DC-31) is protective and blocks no go-live | RI-33 |
| ER-13 | Medical director: hour-by-hour on-call cover | Items route at every hour with on-call cover; it names no medical-director sign-off, and the medical director is the fallback recipient | RI-34 |
| AO-10 | Controller or manager | The controller or office manager signs each day's agent digest; a per-day sign-off that blocks nothing | Not a row (WI-07); P-7 |
| BAB-17 | Controller or manager: autonomy opt-ins | The signer rules: an agent signs only where opted in and never at tier 4, and the D8 and estimator approvals are the accountant's only. The opt-ins are granted under AO-03 and AO-04 | RI-01, RI-02, RI-22 |

Beyond the critique: draft PR #7 adds AH-06's consent (RI-26) and PG-16's relying parties (RI-37, RI-38, RI-43), and PR #8 encodes rulings 102 and 104 (RI-47, RI-46).

### Not readiness items

- **Per-transaction sign-offs** (in the work inbox under WI-07, not on the checklist): EB-04, EB-05, EB-08, EB-09, EB-17, AI-06, AI-07, AI-08, PR-12, refunds and material adjustments at tier 4, PG-05 and PG-06 confirmations, PG-07, PG-08, PG-09, PH-04, ER-12, AO-09, the AH-01 suspension lift, AO-10's daily digest, AH-06's per-patient grants, and the retention change's holds and disposition approvals. Each appears in the role analysis below where one person can sign both sides.
- **Technical setup with no human attestation:** the issuer (EB-02), destination credentials (AI-18), the ERPNext version pin, practice hours and staff lanes (ER-11, ER-13), and the integration operator of EB-15 (PR #7), who receives exceptions but signs nothing.
- **Product governance:** each change's own ratification, the mode continuity procedure (AI-09), the scheduling change PH-01 waits for, and per-feature security reviews.

## The starter kit

| # | Template | Serves | Signs the policy made from it | What the template asks for: the items its requirement names, plus prompts from that change's open questions |
|---|---|---|---|---|
| 1 | Hold policy | PH-03 | Medical director, with counsel's review recorded | Placement criteria; disputed balances and open GFE windows (PH-06); membership obligations (PH-07); countdown length; notice wording; lift conditions and review interval; scope across a guarantor's dependents (gateway Open Question 4) |
| 2 | Proxy policy | PG-06 | Medical director, with counsel's review recorded | Relationships covered; authority evidence; scope per relationship, including billing; expiry or review dates; minors' confidentiality (gateway Open Question 2) |
| 3 | Routing-clock policy | ER-08, ER-09, ER-20 | Medical director | Acknowledgment and response windows per urgency, ordered (ER-08 defaults shown as such); alert-acknowledgment window and re-send interval (DC-29 defaults shown as such); the clinician-acknowledgment alert window of ER-20 (corrections change; DC-58's default shown as such) |
| 4 | Messaging-continuity policy | PG-04 | Medical director | Grace period; notice template for `access_grace_started` and `access_restored` |
| 5 | On-call coverage plan | ER-13 | Not named (RI-34) | Every hour covered, or the fallback named; administrative cover |
| 6 | Autonomy opt-in set | AO-03, AO-04, BAB-17 | Practice manager, or controller or office manager, one opt-in at a time (SK-04) | Action types, each with the highest rung AO-04 allows; every one starts at `propose` |
| 7 | Agent limit values | AO-08 | Practice manager, or controller or office manager | Actions per window and records per action, per action type |
| 8 | Revenue-recognition policy and first estimator approval | AI-12, AI-20 | The practice's accountant only (SR-04) | The five D8 items as questions, with no option chosen; the framework's §5A.4 items listed as prompts only |
| 9 | Read-only answer types | AH-05 exception 1; AO-11 | A named human; health-adjacent also the medical director | For each of the four families: template reference, allowlisted fields (no identifier for coverage on file), topics, cancel window, sampled-audit rate |
| 10 | Consent to AI-authored messaging | AH-06 (PR #7) | Counsel confirms form and wording (SR-04) | The consent's form and wording; how a withdrawal is given |
| 11 | Recipient profiles | AI-10, AI-23, AH-03, "Every business output has a scoped recipient profile" | Not named (RI-09, RI-11, RI-19) | The AI-23 shape: recipient, purpose, row scope, provenance, sensitivity, channels, each field's purpose, recipient and authority |
| 12 | Remote VA access rules | VA-04 | "The practice" (RI-28) | Device approval, idle timeout, session and location rules |
| 13 | Outreach policy | "Audience analysis does not authorize outreach" | Not named (RI-12) | Purposes, audience origins, channel authority, the authorizations and exceptions the practice relies on |
| 14 | Offering routes and membership entitlements | BAB-16; "Offerings determine financial routing at line level" | Not named (RI-10) | Route, owner, entity, category, mapping and entitlement per offering |
| 15 | Retention policy profile | "Retention policy profiles are versioned and sourced" | Not named (RI-15) | The profile fields, all empty: no duration, jurisdiction, program, provider type, effective date or source |
| 16 | Account mapping | BAB-06 | No sign-off (tier 2) | Reason category to account, per destination and mode; shipped only once a governed reason taxonomy exists |

The kit holds no agreement text (SK-01). The agreements of RI-07, RI-17, RI-18, RI-27, RI-30 and RI-37 belong to their parties and to counsel.

## Rules that need two people, or lose their meaning when one person signs both sides

| # | Rule | Enforced by | Why one person is a problem | One-person alternative | Amendment |
|---|---|---|---|---|---|
| P-1 | Accountant sign-offs | AI-12, AI-20, BAB-17, WI-03, MA-07 | Ruling 107: never self-approved | None (SR-04): an accountant outside the practice | CA-2, conforming |
| P-2 | Counsel sign-offs | PH-03, PG-06, AH-02, AH-06, PG-15, PG-16; AH-06's confirmation of ruling 97's answer (PR #8) | Ruling 107: never self-approved | None (SR-04): counsel outside the practice | CA-3 to CA-9, conforming |
| P-3 | Medical director's approval and counsel's review of one policy version | PH-03, PG-06 | Needs two people under ruling 107 | The medical director side may be held with other roles; the counsel side is outside the practice (SR-04, third bullet) | CA-3, CA-4 |
| P-4 | Confirming the first medical director | Ruling 98 | Two people, with ruling 98's own alternative | Ruling 98's own: a one-administrator practice records licence verification against the board's public record | None here. Encoded in `opensoft/openChart-spec` `define-clinical-acts-foundation` AU-01 for openChart's designation, not in this repository (Open Question 5) |
| P-5 | The decider is never the proposer | WI-03, ER-14 | The proposer is an agent or a patient, so one human decider suffices. The only one-person case is a staff member deciding a proposal they submitted as a patient (PG-08, PG-09), which WI-03 refuses | **PROPOSED:** none; the refusal stands (SR-03). The staff member's ordinary registry path under PR-04 and PR-10 is unaffected | None |
| P-6 | A health-adjacent answer type needs a named human's approval and the medical director's sign-off | AH-05 exception 1, condition 5 | Can be read as needing two people | **PROPOSED:** two sign-offs by one person who holds both roles, each recording its role (SR-02) | RA-4, clarifying |
| P-7 | The controller or office manager signs the daily agent digest | AO-10, with AM-04 | When the signer sponsors an agent, the person accountable for the agent reviews its actions | **PROPOSED:** another named human with the practice manager or the controller or office manager recipe who does not sponsor that agent signs that agent's lines; where none exists, the sponsor signs them in both roles, each recorded, and the digest marks them | RA-1, tightens where a second person exists |
| P-8 | A grant is notified to the agent's sponsoring human | AO-03 | When the granting person is the sponsor, the notice goes to themselves | **PROPOSED:** the notice also goes to every other holder of the practice manager or the controller or office manager recipe; where none exists, the checklist shows the grant | RA-3, adds a notice only |
| P-9 | An unacknowledged tracked alert escalates to the medical director; with nobody on call, the medical director gets it | ER-09, ER-13; DC-27; ER-20 (corrections change) | In a one-physician practice the on-call clinician is the medical director, so the escalation reaches the same person. ER-20's alert to the office manager can reach that person too; openPractice cannot tell who should have acknowledged, so no amendment is proposed for ER-20, and the checklist warns (RC-06) | **PROPOSED:** escalate to another named human with the clinician or nurse recipe; where none exists, keep re-sending on both channels, record that the escalation reached no second person, and for a `priority` item also send the guidance instruction | RA-2, adds an alert path |
| P-10 | A sampled human audit reviews read-only answers | AH-05 exception 1, condition 10 | The auditor may be the template's own approver | **PROPOSED:** allowed; both roles recorded (SR-02, SR-03) and shown on the checklist | None |
| P-11 | An absent owner's items pass to a named cover, else to the controller or office manager | WI-02, WI-05 | With one person, the cover and the controller are the absent person | **PROPOSED:** the checklist warns (RC-06); clinical items keep ER-13's on-call path and ER-09's urgent fallback | None |
| P-12 | A staff member signs an act whose subject is their own record, link, balance or refund | PG-05, PG-06, PH-04, BAB-17 (a tier-4 refund); PR-15 (corrections change) | The signer is on both sides | Already stated by PR-15 under ruling 107 (its design call F-7, PROPOSED): another roster entry that holds the grant decides; where none exists, the person acts, recording the role used and that their own party holds a role. PR-15 leaves how such acts are reviewed in a one-person practice to this change: each is recorded and listed on the checklist (SR-03, RC-06) | None while PR-15 stands; RA-5 is the fallback if F-7 is rejected |
| P-13 | A retention profile has a review owner and a human approval | "Retention policy profiles are versioned and sourced" | Two roles, not stated as two people | One person may hold both, each role recorded (SR-02) | None |
| P-14 | An agent's sponsor decides that agent's proposal | WI-03, with AM-03 naming the sponsor as principal | The principal decides a proposal made on their behalf | **PROPOSED reading:** the agent is the proposer, so the sponsor may decide; both roles recorded | None |

## Required amendments (for each owning change's owner; this change edits none)

Each one-person alternative that needs another change's text takes effect only when that change's owner adopts it. Until then the rule stays as written, and this change only records the roles and shows the case on the checklist (SR-03). RA-5 is a fallback: PR-15 of the corrections change already states its rule for every requirement RA-5 lists, so RA-5 is needed only if the owner rejects that change's design call F-7.

| # | Change and requirement | Effect | Current sentence | Proposed sentence |
|---|---|---|---|---|
| RA-1 | `define-actors-agents-and-work-inbox` AO-10 | Tightens where a second eligible person exists; refuses nothing that blocks operations, because an unsigned digest blocks nothing | "The digest SHALL enter the day close, and the controller or office manager, a named human, SHALL sign it." | "The digest SHALL enter the day close, and the controller or office manager, a named human, SHALL sign it. The lines for an agent whom that signer sponsors SHALL be signed by another named human who holds the practice manager or the controller or office manager recipe and does not sponsor that agent; where the practice has no such person, the signer SHALL sign those lines in both roles, each recorded, and the digest SHALL mark them as reviewed by the agent's own sponsor (`define-practice-readiness` SR-03)." |
| RA-2 | `define-patient-gateway-and-escalation` ER-09 | Adds an escalation target, and in a one-clinician practice a guidance instruction for `priority` items. The gateway schema's `guidance.instruct` carries no urgency field, but its description names urgent items only, so the owner would also change that description and the digest | "When the recipient does not acknowledge within the alert-acknowledgment window, openPractice SHALL re-send the alert and escalate it to the medical director, and SHALL keep re-sending to both until one of them acknowledges." | Add after it: "When the medical director is the same person as the recipient, openPractice SHALL escalate instead to another named human who holds the clinician or nurse recipe, and SHALL keep re-sending to both; when the practice has no such person, it SHALL keep re-sending to the recipient through both channels, record that the escalation reached no second person, and, for an item whose applied urgency is `priority`, also send HealthLinc `guidance.instruct` with cause `clinical_response_overdue` (`define-practice-readiness` SR-03)." |
| RA-3 | `define-actors-agents-and-work-inbox` AO-03 | Adds a notice only | "Granting or widening a scope SHALL be a tier-2 action with notification to the sponsoring human." | "Granting or widening a scope SHALL be a tier-2 action with notification to the sponsoring human; when the granting person is that sponsor, the notification SHALL also go to every other named human who holds the practice manager or the controller or office manager recipe, and where there is none, the readiness checklist SHALL show the grant as made by the agent's own sponsor (`define-practice-readiness` SR-03, RC-06)." |
| RA-4 | `define-actors-agents-and-work-inbox` AH-05 exception 1, condition 5 | Clarifies, under ruling 107 | "A named human approves each answer type and each template version. Anything health-adjacent also needs the medical director's sign-off. Neither approval SHALL be opted in for agent signing." | "A named human approves each answer type and each template version. Anything health-adjacent also needs the medical director's sign-off, given as a separate sign-off; a medical director who is also the approving named human gives both, each recording its role (`define-practice-readiness` SR-02). Neither approval SHALL be opted in for agent signing." |
| RA-5a | `define-patient-gateway-and-escalation` PG-05 | Fallback, only if PR-15's F-7 is rejected; tightens where a second eligible person exists | The second condition for `linked`: "a named human confirms, at tier 2, the registry record that the practice's invitation or an exact match (PR-08) identifies, after seeing it side by side with the identity claim." | Add after the list of conditions: "That named human SHALL NOT be the person the link is for, or the account holder, while another named human who may confirm it is on the roster; where none is, the confirmation SHALL record both roles and be shown on the readiness checklist (`define-practice-readiness` SR-03)." |
| RA-5b | `define-patient-gateway-and-escalation` PG-06 | As RA-5a | "The link also needs the representative's verified identity and a named human's tier-2 confirmation of the authority." | Add after it: "That named human SHALL NOT be the representative or the patient while another named human who may confirm it is on the roster; where none is, the confirmation SHALL record both roles and be shown on the readiness checklist (`define-practice-readiness` SR-03)." |
| RA-5c | `define-patient-gateway-and-escalation` PH-04 | As RA-5a | "Every placement and lift SHALL record the policy version, the coded criteria met, the actor and actor type (AM-03), the signer where one signs (BAB-17), the time and the provenance." | Add after it: "A named human SHALL NOT place or lift a hold on a patient whose balance they owe as patient or guarantor while another named human who may do so is on the roster; where none is, the act SHALL record both roles and be shown on the readiness checklist (`define-practice-readiness` SR-03)." |
| RA-5d | `define-mvp-practice-foundation` BAB-17 | As RA-5a | "Tier-4 actions SHALL be signed by a named human only." | Add after it: "A named human SHALL NOT sign a refund leaving the practice to themselves, or to a guarantor account they hold, while another named human who may sign it is on the roster; where none is, the sign-off SHALL record both roles and be shown on the readiness checklist (`define-practice-readiness` SR-03)." |

## Conforming amendments (recommended; SR-02 and SR-04 already bind)

SR-02 and SR-04 apply to every sign-off they name, and none of the sentences below permits what they forbid, so there is no conflict while these wait. Each makes the owning requirement say the rule where its sign-off is defined.

| # | Change and requirement | Current sentence | Proposed sentence |
|---|---|---|---|
| CA-1 | `define-mvp-practice-foundation` BAB-17 | "Every sign-off SHALL record the signer's identity, including the openWallet ID for an agent, with the action, the time and the provenance." | "Every sign-off SHALL record the signer's identity, including the openWallet ID for an agent, the role under which it was given (`define-practice-readiness` SR-02), the action, the time and the provenance." |
| CA-2 | `define-mvp-practice-foundation` BAB-17, AI-12 and AI-20 | BAB-17: "The D8 approval and every estimator-version approval SHALL be given by the practice's accountant only, and SHALL NOT be opted in for agent signing (accounting-integration AI-12, AI-20)." AI-12 and AI-20: "The D8 approval and every estimator-version approval SHALL be given by the practice's accountant only, and neither SHALL be opted in for agent signing (BAB-17)." | Append to each: "Neither SHALL be self-approved (`define-practice-readiness` SR-04)." |
| CA-3 | `define-patient-gateway-and-escalation` PH-03 | "Counsel's review SHALL be recorded on each version, by reference, before approval." | "Counsel's review SHALL be recorded on each version, by reference and naming the counsel who gave it, before approval, and SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |
| CA-4 | `define-patient-gateway-and-escalation` PG-06 | "The practice's medical director approves the policy, and counsel's review is recorded on it." | "The practice's medical director approves the policy, and counsel's review is recorded on it, naming the counsel who gave it; that review SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |
| CA-5 | `define-actors-agents-and-work-inbox` AH-02 | "This change asserts no legal rule; counsel confirms each agreement's form." | "This change asserts no legal rule; counsel confirms each agreement's form, and that confirmation SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |
| CA-6 | `define-patient-gateway-and-escalation` PG-15 | "This change asserts no legal rule; counsel decides what agreement is required and its form." | "This change asserts no legal rule; counsel decides what agreement is required and its form, and counsel's decision SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |
| CA-7 | `define-actors-agents-and-work-inbox` AH-06 (PR #7) | "This change asserts no legal rule; counsel confirms the consent's form and wording." | "This change asserts no legal rule; counsel confirms the consent's form and wording, and that confirmation SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |
| CA-8 | `define-patient-gateway-and-escalation` PG-16 (PR #7) | "Registration records the relying party's issuer, the capability codes it may rely on, and where it reads the practice's JWK Set with its pin, and SHALL wait until an authorized person has recorded and verified the agreement counsel requires with that relying party's operator, following PG-15's pattern (design call DC-47, PROPOSED)." | "Registration records the relying party's issuer, the capability codes it may rely on, and where it reads the practice's JWK Set with its pin, and SHALL wait until an authorized person has recorded and verified the agreement counsel requires with that relying party's operator, following PG-15's pattern, with counsel's decision never self-approved (`define-practice-readiness` SR-04; design call DC-47, PROPOSED)." |
| CA-9 | `define-actors-agents-and-work-inbox` AH-06 (corrections change, still moving) | "Before any PHI reaches a model provider for practice operations on the strength of the provider agreement alone, an authorized person SHALL record counsel's confirmation of ruling 97's answer, on the pattern of AH-02 and `define-mvp-practice-foundation` AI-14: the confirming counsel, the date, the scope (the processing for practice operations it covers, naming the agent operators and model providers by their AH-02 records), an owner-local document reference, and the recording person and time (design call D-31, PROPOSED)." | Add after it: "That confirmation SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |

## Governance tiers (FD-5) for actions this change adds

| Action | Tier | Signer (BAB-17) | Agent ceiling (AO-04) |
|---|---|---|---|
| Read the checklist | Not an action | — | No agent action type exists in 1.0 |
| Create a practice draft from a template | 1 (do + undo) | No sign-off; a person whose recipe may prepare that policy (planning) | No agent action type exists in 1.0 |
| Approve a version made from a template | The gate's own tier | The gate's own signer, exactly as for a version written from scratch (SK-03) | The gate's own ceiling; never above `propose` for any approval that is never opted in |
| State the role at signing | Part of each sign-off | The signer | — |
| Use a one-person alternative | As the owning requirement, once adopted | As that requirement | As that requirement |

**Deadline escapes (FD-5).** No action this change adds holds anything deadline-bearing. The checklist holds nothing. SR-04's refusals hold the D8 approval, estimator versions and policy or agreement sign-offs, none of which stalls a claim: accounting postings carry no filing deadline (`define-mvp-practice-foundation`, "Deadline escapes"), and holds and representative links are refused meanwhile. SR-03's fail-closed default applies only to rules that already need two people, each of which keeps its own escape.

## Constitution Check (pre-handoff, against v1.0.0)

| Principle or gate | Obligation | Status |
|---|---|---|
| I. HIPAA by design | The checklist holds no PHI (RC-07). No gate is relaxed (RC-03). The accountant and counsel stay independent (SR-04). | Requirement level |
| II. Security review | The feature touches authorization: who may sign, and in which role. It needs a security review and a risk-analysis update. Nothing autonomous is added; no template is adopted by default (SK-04). | Owed per feature |
| III. Test-first | Tests cover RC-03 (no write path, nothing enabled), the RC-04 states, SK-02 to SK-05, the SR-02 role refusal and the SR-04 refusals. | Owed per feature |
| IV. Clinical boundary | No clinical content anywhere. Templates assert no clinical standard (SK-06). | Satisfied |
| V. Versioned contracts | No contract is added. The checklist is read through the versioned API (RC-07). | Satisfied |
| VI. Portability | Nothing vendor-specific. | Satisfied |
| AGENTS.md: no autonomous financial or patient-impacting actions by default | No template is adopted by default, no opt-in is taken in bulk, and every action type still starts at `propose` (SK-04). | Satisfied |

## Design calls for owner review

Each item below goes beyond ruling 107. Each is PROPOSED, not ruled, and each is also routed as an open question.

| # | Design call | Where |
|---|---|---|
| RD-1 | **PROPOSED.** The inclusion rule: a readiness item is recorded once per practice, counterparty, destination, agent or version, and makes a function wait; per-transaction sign-offs stay in the work inbox. | RC-01 |
| RD-2 | **PROPOSED.** Release prerequisites the product owner decides are shown read-only in their own section, not as practice rows. | RC-01; RI-40 to RI-45 |
| RD-3 | **PROPOSED.** Five row states, with `not_required` only from the enforcing requirement's own condition read from configuration, never from a person's mark. | RC-04 |
| RD-4 | **PROPOSED.** Where a requirement names no authority, the row says so and the checklist names none; the gaps go to their owners (I-4). | RC-02 |
| RD-5 | **PROPOSED.** Rows are grouped by the function they gate, so a practice that turns a function off sees which rows it no longer needs. | RC-01 |
| RD-6 | **PROPOSED.** A version made from a template records the template's identifier and version as provenance only. | SK-03 |
| RD-7 | **PROPOSED.** A template may show only values an existing requirement already states, marked as that requirement's; everything else is blank. | SK-06 |
| RD-8 | **PROPOSED.** A newer template version is shown beside each version started from an earlier one, and is never applied. | SK-05 |
| RD-9 | **PROPOSED.** A role is a recipe, an actor type, or a capacity a requirement names as a signer or an owner of work. | SR-01 |
| RD-10 | **PROPOSED.** One act counts for one role; a person signing in two roles gives two sign-offs. This matches MA-07 as the corrections change words it under ruling 105, where each mobile sign-off has its own verification. | SR-02 |
| RD-11 | **PROPOSED.** The reading of "self-approved": the signer is an owner, holds any other role in the practice (the accountant may also hold the bookkeeper recipe), signs another side of the same item, or is an agent. The stricter reading, with no bookkeeper exception, would stop an accountant who keeps the books from signing at all, because an accountant needs some recipe to act in openPractice (I-1). | SR-04 |
| RD-12 | **PROPOSED.** A one-person alternative applies only when the roster holds no second eligible person at the moment of the act; each use is recorded and shown. | SR-03 |
| RD-13 | **PROPOSED.** Order of preference for one-person alternatives: another eligible person, then an independent external record as ruling 98 uses, then the sign-off in both roles, each recorded and shown. | Decision 3 |
| RD-14 | **PROPOSED.** The alternatives of P-5 to P-14, the amendments RA-1 to RA-4, and RA-5 as a fallback. | Tables above |
| RD-15 | **PROPOSED.** Handoff: exactly one Speckit feature. Its plan lands SR-02 and SR-04 in the shared sign-off record before any sign-off is recorded in production, and adds each checklist row with the feature that builds its gate. | `tasks.md` §2 |
| RD-16 | **PROPOSED.** Which recipes may read the checklist is decided in planning; it holds no PHI either way. | RC-07 |

## Conflicts and tensions

| # | Tension | Status |
|---|---|---|
| T-1 | **FD-5** says "the second human does not exist; approval queues rot within a month." | **Consistent.** This change adds no approval. Ruling 107's one person in several roles matches FD-5's premise. SR-04 departs from it only for the accountant and counsel, by ruling 107. |
| T-2 | **FD-1** rejects configuration surfaces that ask a practice to author policy it never articulated. | **Conscious exception proposed.** The kit lowers the authoring burden FD-1 warns about, and the checklist replaces a scatter of gates with one view. Both are fixed and shipped; the practice authors no rule. |
| T-3 | **AM-01** declares exactly nine actor types, and none is the practice's accountant or counsel, yet SR-04 must identify both. | **Recorded.** SR-04 works on the person named on the sign-off. The corrections change gives every real person one party (PR-15) and every human roster entry one party (AM-01 as corrected), so SR-04 can compare the named accountant or counsel with the parties behind roster entries. This change adds no actor type; Open Question 3. |
| T-4 | **AH-05 exception 1, condition 5** can be read as needing two different people for a health-adjacent answer type. | **RA-4 proposed**, on ruling 107's words. |
| T-5 | SR-02 and SR-04 bind sign-offs defined in other changes before CA-1 to CA-9 land, so the rule has two homes for a while. PR-15 of the corrections change also states the self-approval bar and the role record for own-record acts. | **Recorded.** No conflict: no target sentence permits what SR-02 or SR-04 forbids, and SR-04 supplies the meaning of "self-approved" (RD-11) that PR-15 leaves open. |
| T-6 | Rulings 102 and 104 are encoded only in the corrections change, which is unmerged and still moving. | **Deliberate.** RI-46 and RI-47 cite that change's requirement IDs; under RC-02 they follow whatever it finally says. |
| T-7 | **Ruling 110** pauses perimeter work. | **Consistent.** This change adds no contract and no perimeter behavior, and ruling 107 orders it. Rows that depend on the Revenue Cycle Core (RI-04, RI-05, starter template 16) follow that work when it lands (RC-02). |
| T-8 | **Ruling 109**: no promotion to canonical specs before building. | **Consistent.** `tasks.md` archives this change only after its feature lands. |

## Inconsistencies found while building the inventory (reported, not fixed)

| # | Finding | Where |
|---|---|---|
| I-1 | The practice's accountant has no actor type in AM-01 and no recipe in AM-02, yet six requirements make that person the only signer of the D8 approval and estimator versions. Counsel has none either, though four requirements on `main` record counsel's part, and PR #7 and PR #8 add more in AH-06 and PG-16. AM-01 also refuses any action from an actor whose type it cannot determine. The corrections change's party register (PR-15) helps identify the person, but gives no actor type. | AM-01, AM-02; AI-12, AI-20, BAB-17, WI-03, AO-04, MA-07; PH-03, PG-06, AH-02, PG-15, AH-06, PG-16 |
| I-2 | BAB-10 restricts full bank numbers to "the owner and billing-administrator roles", and neither is a recipe in AM-02. | BAB-10, AM-02 |
| I-3 | Five agreement records say "an authorized person" without naming which role is authorized. Three of them involve counsel (AH-02, PG-15, PG-16); AI-14 and VA-01 follow the same record pattern without counsel. PR #8 adds two more of the same kind: the zero-data-retention record (AH-02) and the record of counsel's confirmation (AH-06). | AI-14, AH-02, VA-01, PG-15, PG-16; AH-06 (PR #8) |
| I-4 | Gates whose requirement names no authority: jurisdiction selection and profile approval in the retention change; public-envelope profile selection; recipient profiles (AI-10, AI-23, AH-03, business outputs); approved membership terms and offering routes; the outreach policy; outsourced-firm vendor arrangements; VA session and location rules; the on-call roster and the second alert channel; the reason taxonomy; the destination verification record. AH-05 condition 5's "named human" is tied to no recipe, while switching the same answer type on is (AO-03). | RI-03, RI-04, RI-09 to RI-16, RI-19, RI-20, RI-28, RI-34, RI-35, RI-39; AH-05 |
| I-5 | "The owner" is ambiguous between the practice owner and the product owner: AI-20 ("until the owner decides how to proceed"), AI-09 ("until the owner ratifies a continuity procedure"), AM-02 ("approved by the owner before the recipe ships"), and gateway Open Questions 9 and 10. | AI-20, AI-09, AM-02 |
| I-6 | PH-03 records counsel's review "before approval"; PG-06 records it "on" the version and says nothing of order. | PH-03, PG-06 |
| I-7 | Security reviews and risk-analysis entries per customer-detail destination, per agent grant, per opt-in above `propose`, per answer type and per VA company appear only in designs and tasks, not in any requirement, so RC-02 can give them no row. | Foundation proposal "Impact", design Constitution Check, `tasks.md` 2.3; actors design Constitution Check, `tasks.md` 3.1 |
| I-8 | Every medical-director and controller-or-office-manager sign-off waits on AM-02's grant-set approval for those recipes, which is a release act by the product owner. Until it is made, no practice can approve a hold, proxy, routing-clock or messaging-continuity policy, sign a digest or grant an agent. | AM-02 with PH-03, PG-06, ER-08, PG-04, AO-10, AO-03 |
| I-9 | In a one-physician practice, DC-27's on-call cover can be met by one person who is also the medical director, so ER-09's escalation, ER-13's fallback and, in the corrections change, ER-20's alert to the office manager can all reach the person who did not answer. | ER-09, ER-13, DC-27; ER-20 |
| I-10 | Ruling 98 is not encoded anywhere in this repository: not on `main`, in PR #7 or in PR #8 at `957c745`. It is encoded in `opensoft/openChart-spec` `define-clinical-acts-foundation` AU-01 (draft, not merged; read at `28d604f`), for openChart's designation of the first medical director. No openPractice requirement puts the same check on giving a person the medical director recipe, so the checklist has no row for it (RC-02). | AM-02; openChart-spec AU-01 |
| I-11 | The critique's citations differ from the specs (table "The critique's citations, checked against the requirements"): BAB-02 and AI-06 do not concern the accountant (D8 lives in AI-12, AI-20 and BAB-17); ER-13 names no medical-director sign-off for the on-call roster; AO-10's unsigned digest blocks nothing; ER-08 blocks nothing, because defaults apply; PG-04 keeps cut-offs off rather than blocking go-live; AI-14 and VA-01 do not name counsel. | Critique item C.1 |
| I-12 | The framework brainstorm says account-mapping changes "need approval" (§5A.2.9), while the foundation's governance tiers make a mapping change tier 2 with no sign-off. The brainstorm has proposal status and the change governs. | Framework §5A.2.9; foundation governance tiers |

## Dependencies

- **On `main` (`0a82fc2`):** the five merged changes. Every requirement ID cited here exists on `main`, except those listed next.
- **Only in draft PR #7** (`change/follow-up-ruling-93-contract-gaps`, head `e746f58`): AH-06 and the actors design calls D-22, D-23 and D-27 to D-29 it cites; the integration operator in EB-15; PG-16, design call DC-47 and the gateway governance-tier row for relying-party registration; actors Open Question 9 and gateway Open Question 18; ruling 97. This change should merge after PR #7. If PR #7 changes before it merges, RI-26, RI-37, RI-38, RI-43, CA-7, CA-8 and starter template 10 follow it.
- **The corrections change, draft PR opensoft/openPractice-spec#8** (`change/critique-2026-11-15-corrections`, stacked on PR #7, read at `957c745` and still moving): rulings 102 and 104 in AH-01, AH-02 and AH-06 (RI-46, RI-47, CA-9); PR-15 and its design call F-7 (P-12, RA-5); ER-20 and DC-58 (RI-36, starter template 3, P-9); AM-01's party reference (T-3, I-1); MA-07's per-sign-off verification (RD-10). PR-15 and ER-20 exist only there. This change should merge after it, and follows it if it changes (`tasks.md` 1.2).
- **Rulings 98 to 111** are recorded here only as far as they govern this change; their own homes are the changes that encode them.

## Risks / Trade-offs

- **[The checklist is mistaken for a gate]** → It has no write path and enables nothing (RC-03); tests prove it.
- **[The checklist drifts from the requirements]** → Rows derive from requirements and follow them (RC-02); a ruling with no requirement has no row.
- **[A template is treated as approved policy]** → Templates are never read by a gate (SK-02); approval is the gate's own act (SK-03); nothing is adopted by default or in bulk (SK-04).
- **[Templates give legal or clinical comfort they cannot give]** → Each carries a statement that it asserts no rule (SK-06); this change writes no content.
- **[One person signs everything without anyone noticing]** → Every sign-off records its role (SR-02); the checklist shows each person's roles and every case where one person signed both sides (RC-06).
- **[A solo practice cannot find an accountant or counsel]** → Ruling 107 accepts that cost; SR-04 says so plainly, and the checklist shows it.
- **[Many amendments for other owners]** → Required amendments change behavior only when adopted (SR-03); conforming amendments restate what SR-02 and SR-04 already bind.

## Migration Plan

1. Validate strictly. Merge after PR #7 and the corrections change. The owner completes `owner-ratification.md`.
2. Each owning change's owner dispositions RA-1 to RA-4, the fallback RA-5 if it is needed, and CA-1 to CA-9.
3. One Speckit feature (RD-15): the role record and the self-approval bar first, built into the shared sign-off record; then the checklist and the kit, with each row added as its gate's feature lands.
4. Template content is drafted and reviewed outside this change before the kit ships (Open Question 4).
5. Rollback disables the checklist view or the kit; no gate is affected, because none depends on either.
6. Archive only after the feature lands and `closure-evidence.md` is complete (ruling 109).

## Open Questions

Every question is routed in `tasks.md` §3.4 to the work it blocks.

1. **The inclusion rule and the release-prerequisite section** (RD-1, RD-2, RD-5). The owner.
2. **The reading of "self-approved"** (RD-11), and whether the bookkeeper exception stands. The owner.
3. **How the practice's accountant and counsel are identified in openPractice** (T-3, I-1), given that AM-01 has no actor type for either. The owner, with `define-actors-agents-and-work-inbox`'s owner.
4. **Who drafts and reviews starter-template content before it ships**, and with which counsel, accountant and medical-director review. The owner.
5. **Ruling 98**: openChart encodes it, for its own designation of the first medical director (`opensoft/openChart-spec` `define-clinical-acts-foundation` AU-01). Whether openPractice's assignment of the medical director recipe should carry the same check is open. Not proposed here, because it would add a gate. The owner.
6. **RA-1 to RA-4**, and RA-5 if the owner rejects PR-15's F-7: accept, change or reject each. The owner of each change, then the owner.
7. **RA-2's guidance instruction for `priority` items** in a practice with one clinician. The owner, with the medical director.
8. **The gates whose requirement names no authority** (I-4, RD-4). The owners of those changes.
9. **"The owner" in AI-20, AI-09 and AM-02** (I-5). The owner.
10. **Rulings 102 and 104**: the corrections change words them in AH-01, AH-02 and AH-06, with design calls D-30 and D-31 PROPOSED there. RI-46 and RI-47 follow whatever the owner rules on them. The owner, in that change.
11. **The handoff shape** (RD-15). The owner.
12. **Who may read the checklist** (RD-16). Planning.
13. **Security reviews and risk-analysis entries that live only in designs and tasks** (I-7): whether they should become requirements, so the checklist can show them. The owner.
