# Owner Rulings Index: Rulings 1 to 151

Status: navigation — not normative, not ratified
Kind: index
Summary: one row for each of the owner's numbered rulings 1 to 151, with its date, a one-line statement and the repository and change whose design.md "Owner rulings" table records it, or where it is being encoded; ruling 80's approved wording, in its public form, has a section of its own. Written under ruling 108 alongside Framing Decisions v2.
Topics: framing-decisions, owner-rulings
Captured: 2026-11-15

**Built on 2026-11-15** from the tables at the revisions listed under "Revisions read". Whatever this index says about a change or pull request being merged, open or in draft describes that date.

**The "Owner rulings" table in the `design.md` of each change remains the source of truth for every ruling. This index is navigation.** Where a row here and a table differ, the table governs. Rulings are not ratification: every change that records them is a draft until the owner ratifies it.

The rulings were made by **Brett Heap**, the product owner. Rulings 1 to 64 were made on 2026-11-14. Rulings 65 to 151 were made on 2026-11-15. Rulings 79 and 81 to 98 were accepted in one answer ("accept recomendations. on all but 80. for 80 explain that more"); rulings 99 to 111 in another ("accept your recs for 99-111. explain 80 again"); rulings 112 to 135 with the words "for the rest, go with your recomendations"; ruling 80 and rulings 136 to 150 with the words "approve 80. accept your recs for 136-150"; and ruling 151 after "approve 80", as the owner's addition to clause 12 of ruling 80.

[Framing Decisions v2](openpractice-framing-decisions-v2.md), a draft for the owner's review, folds in the rulings that bear on a framing decision and cites them by these numbers.

## How each row was written

- **Statement.** For a ruling recorded in a public repository, the statement is taken from a public table, shortened to one line in that table's words. The table it comes from is listed first and in bold under "Recorded in". It is the table of the change the ruling chiefly governs, unless two public tables state the ruling differently in substance; then the fuller one is used, and the row is marked † and explained under "Notes".
- **Private only.** For a ruling recorded only in private repositories, the row gives the number, the date, a neutral topic and where it is recorded, marked *private: topic only*. Nothing else from a private repository is copied here.
- **Rulings 99 to 111.** Rulings 99 to 107 are recorded in public tables added by the critique corrections, 102, 104, 107, 109 and 110 also in `OP:readiness`, and 101 also in `OC:inbox`. Those tables quote the owner's accepted answer of 2026-11-15 with the question in brief, and the statement is taken from them. `OP:readiness` quotes ruling 110's answer only as "Yes.", so that row gives the whole accepted answer. Rulings 108 and 111 are in no table yet; their statement is the owner's accepted answer, and the row says "no table yet".
- **Rulings 112 to 150.** The statement is the question as it was put to the owner and the recommendation he accepted, copied by script from the messages that put them to him. Rulings 112 to 135 are already recorded in public tables, which quote the same words; ruling 132's accepted words include a three-item list, kept here as a list (`OP:rcc` gives it as running text). Rulings 136 to 150 are in no table yet, and the row says where each is being encoded, or is to be encoded next.
- **Rulings 80 and 151.** Row 80 gives ruling 80's one-line rule and points to the section [Ruling 80](#ruling-80-additions-to-a-patients-chart) below, which reproduces the approved wording in the form approved for public repositories. Row 151 gives the owner's words verbatim. Neither is in a table yet.
- **Pointers.** An italic note after a statement points to a later ruling that qualifies or partly supersedes it. It is this index's pointer, not table text.
- **Recorded in** lists every table found to record the ruling, by the codes below. A table that does not list a number only means the ruling was not relayed to that change.

| Code | Repository | Change |
|---|---|---|
| `OP:mvp` | `opensoft/openPractice-spec` (public) | `define-mvp-practice-foundation` |
| `OP:cfdb` | `opensoft/openPractice-spec` (public) | `define-customer-financial-data-boundaries` |
| `OP:actors` | `opensoft/openPractice-spec` (public) | `define-actors-agents-and-work-inbox` |
| `OP:gateway` | `opensoft/openPractice-spec` (public) | `define-patient-gateway-and-escalation` |
| `OP:readiness` | `opensoft/openPractice-spec` (public) | `define-practice-readiness` |
| `OP:rcc` | `opensoft/openPractice-spec` (public) | `define-revenue-cycle-core` |
| `OC:boundary` | `opensoft/openChart-spec` (public) | `define-openpractice-boundary` |
| `OC:inbox` | `opensoft/openChart-spec` (public) | `define-clinical-message-inbox` |
| `OC:aiwrite` | `opensoft/openChart-spec` (public) | `define-ai-authored-clinical-records` |
| `OC:acts` | `opensoft/openChart-spec` (public) | `define-clinical-acts-foundation` |
| `OC:rx` | `opensoft/openChart-spec` (public) | `define-prescribing-safety-checks` |
| `HL:gateway` | `opensoft/HealthLinc` (private) | `define-practice-gateway-integration` |
| `MXF:ops` | `MedxSoft/medXfactory` (private) | `define-care-organization-hermes-openpractice-operations` |
| `MXF:conn` | `MedxSoft/medXfactory` (private) | `define-patient-medical-ai-connection` |
| `MXE:overlay` | `MedxSoft/MedxEHR-spec` (private) | `define-medical-ai-record-overlay` |

A marker after an `OP:` code names the change set that added the row to that change's table, so it stays true after a merge: "(ruling-93 follow-up)" is `opensoft/openPractice-spec#7` (branch `change/follow-up-ruling-93-contract-gaps`), and "(critique corrections)" is `opensoft/openPractice-spec#8` (branch `change/critique-2026-11-15-corrections`, stacked on #7). `OP:readiness` is the change that `opensoft/openPractice-spec#10` adds (branch `change/define-practice-readiness`), and `OP:rcc` the change that `opensoft/openPractice-spec#11` adds (branch `change/define-revenue-cycle-core`). On 2026-11-15 the other four `OP:` changes were merged on `main` as unratified drafts; #7, #8, #10 and #11 were open drafts; and the `OC:` changes were open drafts on their own `change/<change id>` branches of `opensoft/openChart-spec`. The private changes are cited by repository and change id only.

**Revisions read (2026-11-15).** `opensoft/openPractice-spec`: `main` at `0a82fc2`, `change/follow-up-ruling-93-contract-gaps` at `e746f58`, `change/critique-2026-11-15-corrections` at `f3bed0a`, `change/define-practice-readiness` at `d4724e1` and `change/define-revenue-cycle-core` at `4ee6b85`. `opensoft/openChart-spec`: `define-openpractice-boundary` at `ebb3037`, `define-clinical-message-inbox` at `0d56f69`, `define-ai-authored-clinical-records` at `fa0b404`, `define-clinical-acts-foundation` at `28d604f`, `define-prescribing-safety-checks` at `d2fb4a3`; its `main` at `048097a` holds no "Owner rulings" table. Rulings 112 to 150 were copied from the messages that put them to the owner, and rulings 80 and 151 from the record of ruling 80 the owner approved. The private changes were read at their branch heads of the same day. Several of these branches were still moving; a ruling recorded after these revisions is not listed under "Recorded in".

## Rulings

| # | Date | Statement | Recorded in |
|---|---|---|---|
| 1 | 2026-11-14 | Handoff shape **A**: an ordered multi-feature exception; the field in `owner-ratification.md` is left for the owner to confirm. | **`OP:mvp`**, `OP:rcc`, `OC:boundary` |
| 2 | 2026-11-14 | Accounting customers are the guarantor account (patient balances) and each payer (insurance balances). Refined by ruling 28: only in `customer-detail` mode. | **`OP:mvp`**, `OC:boundary` |
| 3 | 2026-11-14 | **Superseded by ruling 25.** Kept as history: a patient-balance customer is identified by account number only, and no guarantor or patient name reaches the destination. | **`OP:mvp`**, `OC:boundary` |
| 4 | 2026-11-14 | Accounting dates: line date of service; GL date is the date of service if that period is open, otherwise the first open date with a late-charge flag (FD-3); invoice date is the finalization date; due date from statement terms; the sync timestamp is never an accounting date. | **`OP:mvp`**, `OP:rcc`, `OC:boundary` |
| 5 | 2026-11-14 | Banks: a register of practice accounts and payer deposit links; openPractice reconciles deposits only, reading revenue bank lines from the destination; the accounting system owns bank balances and the full reconciliation. | **`OP:mvp`**, `OC:boundary` |
| 6 | 2026-11-14 | A practice-operated destination (for example self-hosted ERPNext) satisfies the BAA gate with an attestation naming the operator. | **`OP:mvp`**, `OP:readiness`, `OC:boundary` |
| 7 | 2026-11-14 | Posting mode is chosen before a destination's first released batch; after that any mode change is refused until the owner ratifies a continuity procedure; a BAA lapse halts delivery with no silent conversion. Refined by ruling 28. | **`OP:mvp`**, `OC:boundary` |
| 8 | 2026-11-14 | The MVP calls ERPNext only through its REST API, same-site or remote, with no in-process calls; in-process use stays conditional on counsel. | **`OP:mvp`**, `OC:boundary` |
| 9 | 2026-11-14 | A record created in error with no linked activity is outside every retention record family; its hard delete is tier 4 and leaves a PHI-free tombstone. | **`OP:mvp`**, `OC:boundary` |
| 10 | 2026-11-14 | Confidential-communication instructions are in the MVP registry and carried in the link snapshot. | **`OP:mvp`**, `OC:boundary` |
| 11 | 2026-11-14 | Preferred language is added and preferred name stays; sex assigned at birth and sexual orientation stay out (clinical; owned by openChart). | **`OP:mvp`**, `OC:boundary` |
| 12 | 2026-11-14 | Beyond FD-5's list, tier 4 applies only to hard delete and to enabling `customer-detail` mode, as a conscious FD-5 exception (T-13); `link_existing` and review resolution are tier 2; the FD-5 deadline escape is stated for gates. | **`OP:mvp`**, `OP:readiness`, `OC:boundary` |
| 13 | 2026-11-14 | Coverage is not in link contract 1.0; it may come in a later MINOR. | **`OP:mvp`**, `OC:boundary` |
| 14 | 2026-11-14 | The date-of-birth hold is accepted, implemented as a link-level suspension. | **`OP:mvp`**, `OC:boundary` |
| 15 | 2026-11-14 | The "agree" check for `link_existing` is accepted and made deterministic against the built DocType. | **`OP:mvp`**, `OC:boundary` |
| 16 | 2026-11-14 | Applied name and date of birth are projected onto `OC Patient` with history; `sex_recorded` is never overwritten. | **`OC:boundary`**, `OP:mvp` |
| 17 | 2026-11-14 | openChart refuses direct edits to a linked patient's projected fields and routes them to openPractice as correction requests, intake flows included. | **`OP:mvp`**, `OC:boundary` |
| 18 | 2026-11-14 | The integration role is named and scoped in planning, narrower than System Manager. | **`OC:boundary`**, `OP:mvp` |
| 19 | 2026-11-14 | Namespace tokens `openpractice` and `openchart` are confirmed. | **`OP:mvp`**, `OC:boundary` |
| 20 | 2026-11-14 | Coverages are zero or more; self-pay patients have none. | **`OP:mvp`**, `OP:rcc`, `OC:boundary` |
| 21 | 2026-11-14 | Manual charge entry is allowed for a linked patient while that EHR reports no billable-facts capability; it is marked manual, with provenance. | **`OP:mvp`**, `OP:rcc`, `OC:boundary` |
| 22 | 2026-11-14 | **Superseded by ruling 30.** Kept as history: accrual valuation at the billed charge plus an expected contractual adjustment from the payer's observed history (FD-2), trued up at remittance. | **`OP:mvp`**, `OC:boundary` |
| 23 | 2026-11-14 | Full bank account and routing numbers are stored encrypted, visible only to owner and billing-admin roles, masked elsewhere, every view audited, and never in logs, errors, exports, postings or keys. | **`OP:mvp`**, `OC:boundary` |
| 24 | 2026-11-14 | Membership, subscription, retail and other non-medical commerce invoicing are out of scope. Revised by ruling 26: their routing and ownership belong to `define-customer-financial-data-boundaries`, and the accounting contract never creates a second collectible balance for them. | **`OP:mvp`**, `OP:rcc`, `OC:boundary` |
| 25 | 2026-11-14 | **Supersedes ruling 3.** A medical-billing customer may carry its name and billing address or contact in the destination once its BAA gate is satisfied, only in `customer-detail` mode; clinical content and service-revealing reason categories are always excluded. | **`OP:mvp`**, `OP:cfdb`, `OC:boundary` |
| 26 | 2026-11-14 | **Ownership split.** `define-mvp-practice-foundation` owns the posting mechanics of the accounting contract; `define-customer-financial-data-boundaries` owns customer and party roles, routing by offering, obligation ownership and purpose-bound business views. | **`OP:mvp`**, `OP:cfdb`, `OP:rcc`, `OC:boundary` |
| 27 | 2026-11-14 | Confidential-communication instructions stay in the link snapshot; openChart, and anything it drives (for example HealthLinc), must honor them for every patient communication it originates. | **`OP:mvp`**, `OC:boundary` |
| 28 | 2026-11-14 | **Accounting posture.** `summary-journal` is the MVP default and recommended setup; `customer-detail` is a per-destination opt-in, after the BAA gate and before first release. Refines rulings 2, 7 and 25. | **`OP:mvp`**, `OP:cfdb`, `OP:rcc`, `OC:boundary` |
| 29 | 2026-11-14 | **Demographics.** openPractice is the source for administrative demographics and publishes them to a linked EHR; each system owns its own record. | **`OP:mvp`**, `OC:boundary` |
| 30 | 2026-11-14 | **Revenue recognition; supersedes ruling 22.** Net revenue is the constrained expected entitlement, estimated at the date of service and re-estimated at each close; the practice's accountant approves the policy items (D8) before go-live. | **`OP:mvp`**, `OP:readiness`, `OP:rcc`, `OC:boundary` |
| 31 | 2026-11-14 | **Appointment authority; closes R-7.** openPractice is the system of record for the administrative appointment and the EHR owns the encounter; appointment events are a future MINOR version. | **`OP:mvp`**, `OP:rcc`, `OC:boundary`, `OC:acts` |
| 32 | 2026-11-14 | **Framework D1.** One Party with roles; "customer" is a party role only for membership and retail buyers; a medical-billing accounting customer is derived from the guarantor or payer role. | **`OP:mvp`**, `OP:cfdb`, `OP:rcc`, `OC:boundary` |
| 33 | 2026-11-14 | **Framework D7; FD-5 governs.** Automation stays propose-only; where a tier calls for an approval, release decision or attestation, a named human or an opted-in named AI agent with an openWallet ID may sign; tier 4 stays human-only. | **`OP:mvp`**, `OP:cfdb`, `OP:readiness`, `OP:rcc`, `OC:boundary`, `MXF:ops` |
| 34 | 2026-11-14 | openPractice is the gateway and router: an active patient not on payment hold gets access; clinical questions go to openChart, administrative ones to Hermes or staff; openPractice keeps only a routing record, with no clinical content. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `OC:acts`, `OC:rx`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 35 | 2026-11-14 | For now the physician uses openChart, on mobile or workstation; the openPractice app covers administrative work and approvals; a current-phase decision that can be revisited. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `OC:aiwrite`, `OC:acts`, `OC:rx`, `MXE:overlay` |
| 36 | 2026-11-14 | Amends FD-10(b): the HealthLinc integration contract and escalation routing come into the MVP; HealthLinc's own app features stay on HealthLinc's roadmap. | **`OP:actors`**, `OP:gateway` |
| 37 | 2026-11-14 | Autonomy ladder per action type (observe, propose, act with notice, act after a delay); every action type starts at propose and the practice opts each one up; tier 4 stays human-only; an agent never acts with less friction than the action's FD-5 tier requires. | **`OP:actors`**, `OP:gateway`, `OP:readiness`, `OP:rcc`, `MXF:ops` |
| 38 | 2026-11-14 | Amends FD-1: the agent-operated practice is the primary frame, so screens are built for exceptions and approvals; the 2–4 staff mode remains for when agents are off. | **`OP:actors`**, `OP:gateway`, `MXF:ops` |
| 39 | 2026-11-14 | There is no master patient identity across HealthLinc, openPractice and openChart; HealthLinc links to each backend through that backend's own link contract. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:conn` |
| 40 | 2026-11-14 | Packaging: two new changes; the current four are ratified separately; HealthLinc, openChart and MedxFactory/Hermes each need a companion change, listed and not drafted there. | **`OP:gateway`**, `OP:actors`, `MXF:ops` |
| 41 | 2026-11-14 | A payment hold blocks non-urgent clinical messaging and new non-urgent scheduling only, never urgent escalations, billing contact or own records; placing or lifting it is logged and tiered, and the patient is told; the medical director approves the policy, which needs counsel. | **`OP:gateway`**, `OP:actors`, `OP:readiness`, `OP:rcc`, `OC:inbox`, `HL:gateway`, `MXF:conn` |
| 42 | 2026-11-14 | "Active" means the patient's registry record is active and, where the practice sells memberships, the membership is current. | **`OP:gateway`**, `OP:actors`, `HL:gateway` |
| 43 | 2026-11-14 | openChart owns the queue and response clock for routed clinical questions; openPractice gets an acknowledgment for each handoff and alerts the office manager if openChart does not acknowledge or the clock runs out. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:conn` |
| 44 | 2026-11-14 | HealthLinc's AI proposes category and urgency and openPractice's routing rules apply them; staff can re-route a misfiled item but cannot answer outside their lane; a mixed question is split; urgency is never downgraded. Amended by ruling 51. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:ops` |
| 45 | 2026-11-14 | Hermes may reply to a patient without a human only with read-only answers. Such an answer changes nothing and tells the signed-in patient only their own administrative information (appointment time, balance, coverage on file) or practice-approved information (hours, policies). During a conversation the agent can read only that one patient's data; each answer type is switched on by the practice; no clinical content, and anything uncertain goes to staff; the patient can always ask for a human. Anything that changes something stays a proposal or follows the ladder. | **`OP:actors`**, `OP:gateway`, `OP:readiness`, `OC:inbox`, `HL:gateway`, `MXF:ops` |
| 46 | 2026-11-14 | The read-only answer types are in the MVP agent operations contract (1.0), alongside the registry operations; in the MVP, Hermes answers routine administrative questions. | **`OP:actors`**, `OP:gateway`, `OP:rcc`, `HL:gateway`, `MXF:ops` |
| 47 | 2026-11-14 | Draft the openChart companion: `define-clinical-message-inbox` in `opensoft/openChart-spec`. | **`OP:gateway`**, `OP:actors`, `OC:inbox` |
| 48 | 2026-11-14 | Draft the HealthLinc companion: `define-practice-gateway-integration` in `opensoft/HealthLinc`, a private repository cited by repository and change id only. | **`OP:gateway`**, `OP:actors`, `HL:gateway` |
| 49 | 2026-11-14 | Draft the medXfactory companion: `define-care-organization-hermes-openpractice-operations` in `MedxSoft/medXfactory`. | **`OP:gateway`**, `OP:actors`, `MXF:ops` |
| 50 | 2026-11-14 | Cut-offs other than a payment hold get safeguards: routine clinical messages still route if the access check is unavailable; a lapse or deactivation stops messaging only after notice and a grace period; a clinician can keep access open. | **`OP:gateway`**, `OP:actors`, `OP:readiness`, `OC:inbox`, `HL:gateway`, `MXF:conn` |
| 51 | 2026-11-14 | HealthLinc's in-app AI is a coach, not a doctor; clinical AI doctors live in medXfactory. Amends ruling 44: the coach or medXfactory's medical AI proposes category and urgency, with conservative defaults (uncertain category clinical, uncertain urgency urgent, never lowered). | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `OC:aiwrite`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 52 | 2026-11-14 | Non-urgent medXfactory medical-AI consults sit behind the practice's access check, and a payment hold may withhold them with the same safeguards; urgent is never withheld; HealthLinc's coach is never gated by the practice. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 53 | 2026-11-14 | The medical AI's exchanges with a patient are recorded in the chart through medXfactory's own governed path (MedxEHR and openChart), never through openPractice. Refined by ruling 70. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `OC:aiwrite`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 54 | 2026-11-14 | HealthLinc's coach gives general information only; anything individualized is handed to medXfactory's medical AI or to the doctor; the not-medical-advice statement is required in addition. | **`OP:gateway`**, `OP:actors`, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 55 | 2026-11-14 | *private: topic only* — the medical-AI connection change | `HL:gateway`, `MXF:conn` |
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
| 67 | 2026-11-15 | The ratified texts stand, among them openChart intake's bar on any prescription, diagnosis or order created by an intake, API response or extension event; limited and full practice stay specified but off, revisited only through a future governed amendment after advice-only has run and counsel's review is in. † *Partly superseded by ruling 80: "It supersedes, for additions to the chart, the first sentence of ruling 67 as the private companion change records it. The rest of ruling 67 stands: limited and full practice stay specified and off."* | **`OC:aiwrite`**, `OC:inbox`, `OC:acts`, `OC:rx`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 68 | 2026-11-15 | Hermes's administrative patient messages are separated from clinical ones in medXfactory's local policy; they follow ruling 45's guardrails with no clinical review; consent and data-sharing conditions still apply. | **`OP:gateway`**, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 69 | 2026-11-15 | openChart owns one neutral conversation-retrieval contract, `openchart-conversation-retrieval/1.0`, which HealthLinc and medXfactory implement. | **`OC:inbox`**, `OP:gateway`, `OC:aiwrite`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 70 | 2026-11-15 | The medical AI's records reach openChart through MedxEHR, which relays them with receipts; medXfactory does not write to openChart directly. Refines ruling 53. | **`OC:aiwrite`**, `OP:gateway`, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn`, `MXE:overlay` |
| 71 | 2026-11-15 | When a patient writes straight to the doctor with no AI triage, HealthLinc asks a short red-flag checklist: any red flag proposes `urgent` with emergency guidance, none `routine`, a skipped checklist `priority`; the patient can raise urgency but never lower it. | **`OP:gateway`**, `OC:inbox`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 72 | 2026-11-15 | The clinical-acts foundation is drafted now, as `define-clinical-acts-foundation`; it accepts only `clinician` as author type and provides the signing and co-signature model that Tier 2 builds on. † | **`OC:aiwrite`**, `OC:acts`, `MXE:overlay` |
| 73 | 2026-11-15 | The foundation's scope is encounters, clinical notes, diagnoses, procedures, orders, prescriptions, and authorship, signing and amendment; left for later changes: results ingestion, care plans and treatment-plan records, immunizations, clinical decision support, and electronic transmission of prescriptions to pharmacies. † | **`OC:acts`**, `OC:aiwrite`, `OC:rx` |
| 74 | 2026-11-15 | The prescription record is specified in the foundation, but the prescription feature is not buildable until allergy and interaction checking is specified; no prescription can be signed where that gate is not met. | **`OC:acts`**, `OC:aiwrite`, `OC:rx` |
| 75 | 2026-11-15 | "Draft the allergy and interaction checking change too." | **`OC:rx`**, `OC:acts` |
| 76 | 2026-11-15 | When the knowledge source is unavailable at signing, a clinician may sign only with a recorded statement that checks were unavailable and a manual review was done; the prescription is flagged and re-checked; a non-clinician author, including any future AI author, can never use that path. | **`OC:rx`**, `OC:aiwrite`, `OC:acts` |
| 77 | 2026-11-15 | The first checks are allergy, drug–drug interaction and duplicate therapy, with drug–supplement interaction only where the source declares support; dose range, drug–condition, pregnancy and lactation, renal and hepatic, paediatric and geriatric rules come in later changes. | **`OC:rx`**, `OC:acts` |
| 78 | 2026-11-15 | openChart bundles no drug knowledge; each practice connects a licensed or verified source through the adapter contract, and no prescription can be signed without one. | **`OC:rx`**, `OC:acts` |
| 79 | 2026-11-15 | *private: topic only* — scope of an existing requirement | `MXF:conn`, `MXE:overlay` |
| 80 | 2026-11-15 | Additions to a patient's chart. "The rule in one line: anyone or anything the chart can identify may add to it, the chart records exactly where each addition came from, and only a decision by someone authorized to make it counts as a decision." The approved wording for public repositories is under [Ruling 80](#ruling-80-additions-to-a-patients-chart) below; the full wording is recorded in the private companion changes. | Being encoded in `OC:acts`, `OC:aiwrite`, `MXF:conn` and `MXE:overlay` |
| 81 | 2026-11-15 | "'Next steps' means how to get care and general self-care information: see your doctor, book a visit, go to urgent care. It never means a treatment instruction for the patient's own condition." | **`OC:aiwrite`**, `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 82 | 2026-11-15 | A verified source that is stale or down uses the outage path, with the medical director alerted; signing stops after a staleness limit set by policy; a practice that never had a verified source cannot sign. | **`OC:rx`**, `OC:acts` |
| 83 | 2026-11-15 | Prescriptions for controlled substances, or with an unknown classification, are refused in the prescribing-checks change; controlled-substance prescribing goes into a later change alongside electronic prescribing. | **`OC:rx`**, `OC:acts` |
| 84 | 2026-11-15 | Read openChart's ratified bar on an "API response" creating a prescription, diagnosis or order narrowly: it bars automatic creation from intake, and an alert to the clinician is not a treatment recommendation; add a small clarifying amendment to the ratified text. | **`OC:acts`**, `OC:inbox`, `OC:aiwrite`, `OC:rx` |
| 85 | 2026-11-15 | Allow the clinical-acts foundation the same ordered multi-feature exception that openPractice was given in ruling 1. | **`OC:acts`**, `OC:inbox` |
| 86 | 2026-11-15 | Before a HealthLinc conversation is escalated, the patient confirms which turns are shared; the escalated question is always shared; for urgent and priority items the turns that triggered the escalation are always shared and the patient is told. | **`OC:inbox`**, `HL:gateway` |
| 87 | 2026-11-15 | openChart publishes only confirmed diagnoses to openPractice as billable facts; if a published diagnosis is later refuted, a retraction is sent. | **`OP:mvp (ruling-93 follow-up)`**, `OP:rcc`, `OC:boundary`, `OC:acts` |
| 88 | 2026-11-15 | After a patient link is revoked or superseded, retractions of facts already published under it are still delivered, as pointers only; no new facts and no reads are served. | **`OP:mvp (ruling-93 follow-up)`**, `OC:boundary`, `OC:acts` |
| 89 | 2026-11-15 | Add a retraction reason code to the clinical-facts contract now, while 1.0 is still unratified, in a small openPractice-spec follow-up. | **`OP:mvp (ruling-93 follow-up)`**, `OP:rcc`, `OC:boundary`, `OC:acts` |
| 90 | 2026-11-15 | Keep quarantining every consult that fails an authorization check, mismatches included: "Whatever the patient was told should be in front of a clinician, whichever check failed." | **`OC:aiwrite`**, `OC:inbox`, `MXF:conn`, `MXE:overlay` |
| 91 | 2026-11-15 | As revised: "medXfactory keeps it and resends; MedxEHR stores nothing", because MedxEHR must store no conversation text. | **`OC:aiwrite`**, `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 92 | 2026-11-15 | Inbox authority lives in the clinical-acts foundation's single grant model, set by the medical director; clinical roles and authority become their own small first feature. | **`OC:acts`**, `OC:inbox`, `OC:aiwrite` |
| 93 | 2026-11-15 | One draft follow-up PR to the merged openPractice drafts, carrying the agent operations Hermes lacks and the consent check, a verifiable entitlement, a clinician-acknowledged status, the representative's relationship and authority, the retraction reason code, and an erratum to the gateway change's companion list. | **`OP:actors (ruling-93 follow-up)`**, `OP:mvp (ruling-93 follow-up)`, `OP:gateway (ruling-93 follow-up)`, `OC:boundary`, `OC:inbox`, `OC:acts`, `HL:gateway`, `MXF:ops`, `MXF:conn` |
| 94 | 2026-11-15 | *private: topic only* — a medical-AI consult safeguard | `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 95 | 2026-11-15 | The chart's grant is the root of the medical AI's authority; medXfactory's configuration can only narrow it, and a pause in the chart stops non-urgent answering at once. | **`OC:aiwrite`**, `OC:inbox`, `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 96 | 2026-11-15 | MedxEHR registers the patient's chart link when the patient enrols for medical-AI consults; no link, no non-urgent consult. | **`OC:aiwrite`**, `HL:gateway`, `MXF:conn`, `MXE:overlay` |
| 97 | 2026-11-15 | Hermes needs no per-patient consent before a model provider processes administrative data: the provider agreement is enough for practice operations, with counsel to confirm; consent applies to AI answering the patient directly. | **`OP:actors (ruling-93 follow-up)`**, `OP:gateway (ruling-93 follow-up)`, `OP:readiness`, `HL:gateway`, `MXF:ops` |
| 98 | 2026-11-15 | The first medical director is confirmed by a second named person; a one-administrator practice records licence verification against the board's public record. *See ruling 146.* | **`OC:acts`**, `OP:readiness`, `OC:inbox`, `MXF:conn` |
| 99 | 2026-11-15 | May Hermes read the administrative question it is answering? "Yes. It cannot answer what it cannot read. Clinical text and documents stay refused." | **`OP:actors (critique corrections)`**, `MXF:ops` |
| 100 | 2026-11-15 | A request mode below the rung set for an agent: "Not in version 1. Lower the rung in openPractice instead." | **`OP:actors (critique corrections)`**, `OP:rcc`, `MXF:ops` |
| 101 | 2026-11-15 | Alert when an urgent item has no clinician acknowledgment in time: "Yes: alert the office manager. It changes no clinical path." | **`OP:gateway (critique corrections)`**, `OC:inbox` |
| 102 | 2026-11-15 | What "counsel to confirm" means in ruling 97: "Design on your answer now, and make counsel's confirmation a go-live condition. Today one draft blocks all Hermes patient-data work until counsel confirms, and two others do not." | **`OP:actors (critique corrections)`**, `OP:gateway (critique corrections)`, `OP:readiness`, `HL:gateway`, `MXF:ops` |
| 103 | 2026-11-15 | One money format for every contract: "Decimal text plus a currency code on the wire, as the accounting contract already does. Whole cents in storage." | **`OP:mvp (critique corrections)`**, `OP:rcc` |
| 104 | 2026-11-15 | What model providers may keep: "Nothing: zero data retention, verified the way the BAA is. Deletion with a receipt applies to the agent operator only." *Qualified by ruling 133.* | **`OP:actors (critique corrections)`**, `OP:gateway (critique corrections)`, `OP:readiness`, `MXF:ops` |
| 105 | 2026-11-15 | Mobile approver sign-in: "Face, fingerprint or passkey for both steps, never a typed password. Opening skips the prompt if you verified in the last 60 seconds. Every sign-off still needs its own." *Qualified by rulings 134 and 135.* | **`OP:gateway (critique corrections)`** |
| 106 | 2026-11-15 | A mixed question found late: "Keep deleting its text from openPractice. Make the administrative part come back as the same question, and keep a reminder on the lane so it is never lost." | **`OP:gateway (critique corrections)`** |
| 107 | 2026-11-15 | One person in several roles: "Allowed, with each sign-off recording the role used. A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change." *See rulings 139 to 141, 144 and 149.* | **`OP:readiness`**, `OP:mvp (critique corrections)`, `OP:gateway (critique corrections)`, `OP:rcc` |
| 108 | 2026-11-15 | Framing Decisions v2: "I draft a v2 for your review that folds in rulings 1–98 and adds an index of every ruling. Version 1 stays as history." | no table yet. Carried out by this index and the v2 draft beside it. |
| 109 | 2026-11-15 | Promote to canonical specs before building? "No. Ratify first, which is yours to do, then archive each change after its feature lands." | **`OP:readiness`**, `OP:rcc` |
| 110 | 2026-11-15 | Write the revenue-cycle core next and pause perimeter work: "Yes. Its scope and decisions will follow as their own list." | `OP:readiness`, `OP:rcc`. `OP:readiness` quotes only "Yes."; the statement gives the whole accepted answer. |
| 111 | 2026-11-15 | Add the critique file to the spec repo: "Yes, in the corrections PR." | no table yet |
| 112 | 2026-11-15 | Question: "One change or several?" Accepted: "One change, with its own ordered multi-feature hand-off, as you allowed in ruling 1." | **`OP:rcc`** |
| 113 | 2026-11-15 | Question: "Which payers in the MVP?" Accepted: "Self-pay first, then Medicare Part B as primary. Commercial and secondary claims later; the data shape allows both from the start." | **`OP:rcc`** |
| 114 | 2026-11-15 | Question: "Which slice is built first?" Accepted: "The self-pay visit. It fixes every data shape and needs no payer, clearinghouse or processor." | **`OP:rcc`** |
| 115 | 2026-11-15 | Question: "Does openPractice book appointments in the MVP?" Accepted: "Yes, minimally: book, reschedule, cancel, no-show and walk-in. No reminders, waitlist or self-scheduling." | **`OP:rcc`** |
| 116 | 2026-11-15 | Question: "Who supplies billing codes? (framework D6)" Accepted: "The EHR supplies coded facts and a named human biller confirms them. Staff enter codes when there are none. openPractice never infers codes." | **`OP:rcc`** |
| 117 | 2026-11-15 | Question: "Deployment shape? (framework D4)" Accepted: "One Frappe app with a module per area. Adapters are separate packages." | **`OP:rcc`** |
| 118 | 2026-11-15 | Question: "How are claims submitted at first?" Accepted: "Through a simulated payer, plus a manual path that records a claim sent outside the system. The live clearinghouse is a later feature." | **`OP:rcc`** |
| 119 | 2026-11-15 | Question: "Do claim batches need an explicit release?" Accepted: "Yes, by a named signer, keeping the escape near a filing deadline." | **`OP:rcc`** |
| 120 | 2026-11-15 | Question: "May the system post exactly matched remittance lines by itself?" Accepted: "Yes: post and notify, undoable. Anything not exact is a proposal for a human." | **`OP:rcc`** |
| 121 | 2026-11-15 | Question: "How much Good Faith Estimate?" Accepted: "Single-provider estimates for scheduled self-pay care: issue, replace, deliver with proof, retain, watch the variance, handle a dispute." | **`OP:rcc`** |
| 122 | 2026-11-15 | Question: "How much of statements and payments?" Accepted: "Record desk payments, receipts, allocations and credits. Refunds stay human-only. Printable statements. Processor and pay link later." | **`OP:rcc`** |
| 123 | 2026-11-15 | Question: "How much of the revenue estimator?" Accepted: "The full estimate record now, with a simple first method your accountant approves before accounting goes live." | **`OP:rcc`** |
| 124 | 2026-11-15 | Question: "How is the money ledger shaped?" Accepted: "Patient balances belong to the guarantor account. Every entry moves money between two fixed buckets, so it always balances." | **`OP:rcc`** |
| 125 | 2026-11-15 | Question: "How are reason codes organized?" Accepted: "A fixed list we ship, on two axes: kind of money movement and service category. Add a service-category field to the accounting contract if the gap is confirmed." | **`OP:rcc`** |
| 126 | 2026-11-15 | Question: "Where do prices and payer expectations come from?" Accepted: "One practice price list and one self-pay discount. Payer expectations come from the estimator, not loaded fee schedules." | **`OP:rcc`** |
| 127 | 2026-11-15 | Question: "Eligibility and prior authorization?" Accepted: "Recorded by hand as evidence. Electronic checks arrive with the clearinghouse." | **`OP:rcc`** |
| 128 | 2026-11-15 | Question: "Hold claims when a provider is not enrolled with the payer?" Accepted: "Yes, minimally: hold, never reject, and release in one action once enrolled." | **`OP:rcc`** |
| 129 | 2026-11-15 | Question: "What makes a write-off "material"?" Accepted: "Each practice sets an amount at setup, confirmed by its accountant. Below it is do-and-notify; everything else is human-only." | **`OP:rcc`** |
| 130 | 2026-11-15 | Question: "Cash drawer and deposit slip?" Accepted: "Deposit slip in the self-pay slice. Drawer count and close sheet in a later change." | **`OP:rcc`** |
| 131 | 2026-11-15 | Question: "Balances carried over from an old system?" Accepted: "Define the record type now. Build the import before the first practice that needs it." | **`OP:rcc`** |
| 132 | 2026-11-15 | Question: "What may agents do?" Accepted: "Everything starts at "propose", per ruling 37. A practice may opt up as far as:<ul><li>**Act with notice:** booking, rescheduling, drafting charges, allocating payments, building claims.</li><li>**Act after a delay:** sending statements.</li><li>**Propose only:** cancelling, finalizing charges, issuing estimates, releasing claims, write-offs, refunds, estimator versions.</li></ul>" | **`OP:rcc`** |
| 133 | 2026-11-15 | Question: "Ruling 104 says model providers keep nothing. May a provider hold a short-lived in-memory cache that is never written to disk or logs?" Accepted: "Yes, with a short stated limit. Providers' zero-retention programs generally work this way, and nothing is stored or readable by a person. Counsel should confirm." | **`OP:actors (critique corrections)`**, `OP:gateway (critique corrections)`, `OP:readiness` |
| 134 | 2026-11-15 | Question: "Ruling 105 allows a passkey. Does one that syncs across a person's devices through their Apple or Google account count?" Accepted: "Yes, but only on a device the person has enrolled in openPractice." | **`OP:gateway (critique corrections)`** |
| 135 | 2026-11-15 | Question: "When face or fingerprint is unavailable, may the phone's own unlock code stand in?" Accepted: "Yes. It never leaves the phone, and the ruling's concern was typed account passwords. Without it, a person with no working biometric cannot use the mobile approver at all." | **`OP:gateway (critique corrections)`** |
| 136 | 2026-11-15 | Question: "How do staff sign in at the workstation? No rule sets it, and anything a phone can approve can also be done there." Accepted: "Two steps for everyone: a passkey, or a password plus a second step. Each sign-off needs its own fresh check, as on the phone." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 137 | 2026-11-15 | Question: "How is an approval phone enrolled?" Accepted: "A colleague with their own enrolled phone vouches: a first phone works at once, a replacement waits 24 hours and can be cancelled. With no colleague, codes go to two contact details on file, at least one on file for 7 days, with the same wait." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 138 | 2026-11-15 | Question: "Ruling 133 says counsel should confirm provider caching. Is that a go-live condition?" Accepted: "Yes, as in ruling 102: no patient data goes to a caching model path until counsel's confirmation is recorded." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 139 | 2026-11-15 | Question: "Who can give the accountant sign-off?" Accepted: "Someone engaged from outside the practice who is neither an owner nor an employee. An employed accountant cannot, in version 1." | To be encoded in `OP:readiness` (`opensoft/openPractice-spec#10`) |
| 140 | 2026-11-15 | Question: "Must that accountant be a licensed CPA?" Accepted: "Yes. The licence is checked against the state board's public record, and the accountant attests in their own name. Without an independent record, an owner could clear themselves." | To be encoded in `OP:readiness` (`opensoft/openPractice-spec#10`) |
| 141 | 2026-11-15 | Question: "The roster has no place for an outside accountant, so nobody can give those sign-offs today. Add one?" Accepted: "Yes: an outside-accountant entry that can read the books and approve accounting policy, estimator versions and the write-off threshold, and nothing else. Counsel needs no sign-in. Added to the corrections PR." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 142 | 2026-11-15 | Question: "About a dozen rules say "an authorized person" records or verifies an agreement. Which role is that?" Accepted: "The practice manager, unless the rule names another role. Each record keeps the role used." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 143 | 2026-11-15 | Question: "In a one-clinician practice an unanswered urgent question has nobody to escalate to. Must the practice name a covering clinician?" Accepted: "Not in version 1. Record when an escalation reached no second person, warn on the readiness checklist, and let the medical director name cover in their policy." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 144 | 2026-11-15 | Question: "One rule says a proposal is never decided by its proposer; another lets a sole staff member act on their own record with a notice to the accountant. Which wins?" Accepted: "The second, only where the practice has nobody else. It records both roles and notifies the outside accountant. Without an outside accountant on the roster, the act waits." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 145 | 2026-11-15 | Question: "Nothing records who owns the practice, so "not an owner" cannot be checked. Record it?" Accepted: "Yes, as dated links from the practice to each owner. Added to the corrections PR." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 146 | 2026-11-15 | Question: "Ruling 98's licence check applies when openChart names the medical director. Should assigning that role in openPractice carry the same check?" Accepted: "Yes, the same check. The role sets escalation policy and signs health-related answer types there." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 147 | 2026-11-15 | Question: "Who writes the starter policies before they ship?" Accepted: "We draft them. Counsel, an accountant and a physician review them before release, and each practice's own authority still has to adopt each one." | To be encoded in `OP:readiness` (`opensoft/openPractice-spec#10`) |
| 148 | 2026-11-15 | Question: "Three tightenings the readiness draft proposes: an agent's sponsor does not alone sign that agent's daily digest; a sponsor granting their own agent more scope notifies the other managers; one person gives two sign-offs only when nobody else can." Accepted: "Accept all three. Each applies only where a second eligible person exists. Added to the corrections PR." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 149 | 2026-11-15 | Question: "Should a named counsel be checked against the state bar's public record and matched against the practice's staff and owners, as the accountant is?" Accepted: "Yes. Ruling 107 says counsel sign-offs cannot be self-approved, and without a check an owner who is a lawyer could name themselves." | To be encoded in `OP:readiness` (`opensoft/openPractice-spec#10`) |
| 150 | 2026-11-15 | Question: "When two records turn out to be the same person, link them or merge them?" Accepted: "Link them in version 1. A wrong merge of two different people is very hard to undo, and a link is reversible. A full merge, with its undo, would be its own later change." | Being encoded in the critique corrections (`opensoft/openPractice-spec#8`) |
| 151 | 2026-11-15 | The owner's addition to clause 12 of ruling 80: "for 12, and any medical decision we need the practitioner to sign off. so we need a logged in user that we use their openWallet and openSig to sign and validate that entry. the openWallet will have the verification of license and the openSig will attest that it is from that wallet." See [Ruling 80](#ruling-80-additions-to-a-patients-chart) below. | Being encoded in `OC:acts`, `OC:aiwrite`, `MXF:conn` and `MXE:overlay` |
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

**Counts.** 151 rows. Three are *private: topic only* (55, 79 and 94). None is open. Nineteen are in no table yet: 108 and 111; 136 to 138, 141 to 146, 148 and 150, being encoded in the critique corrections; 139, 140, 147 and 149, to be encoded in `OP:readiness`; and 80 and 151, being encoded in the companion changes.

## Ruling 80: additions to a patient's chart

Source: **Brett Heap**, 2026-11-15. He asked for his statement on decision 80 to be reorganized and made more complete, and approved the result, exactly as it was put to him, with the words "approve 80. accept your recs for 136-150". Clauses marked *(added)* were added in that reorganization; everything else restates his statement. The marks are part of what he approved.

This is the wording approved for public repositories. Two sentences of the full wording describe a rule of a private repository; here each is replaced by a bracketed note, and everything else is identical. **The full wording is recorded in the private companion changes** (`MXF:conn` and `MXE:overlay`), which were encoding it on 2026-11-15. Ruling 80 is also being encoded in `OC:acts` and `OC:aiwrite`; no table records it yet.

**The rule in one line:** anyone or anything the chart can identify may add to it, the chart records exactly where each addition came from, and only a decision by someone authorized to make it counts as a decision.

**A. Who may add**
1. Any human or system may add to a patient's chart: a clinician, other staff, the patient, an AI, a device or another system. [One sentence of the approved wording stands here. It names an earlier rule of a private companion repository, which this ruling replaces in part, and is recorded in full in that repository's change.]
2. Nothing is added unless the chart can identify what is adding it. An addition it cannot identify is refused, and nothing is stored. *(added)*

**B. How each is identified**

| Who or what | Identified by |
|---|---|
| A clinician or other staff member | Their own named account in the chart *(added)* |
| The patient or their representative | The verified link they come through *(added)* |
| An AI | Its openWallet ID, plus the model and version that produced the content *(model and version added)* |
| A device | Its make, model and serial number, or a medical asset or device ID the chart can resolve to them |
| Any other system: an app, an integration, a relay | Its registered system identity and who operates it *(added)* |

3. Every system is registered with the practice before it can add, and the practice can suspend any system or device at once. *(added)*

**C. What every addition records**

4. **When:** the time the chart received it, and the time the information itself arose where that differs, such as when a reading was taken.
5. **Where the information came from:** the patient's own words, a device reading, an AI consult, a clinician's observation, another organization's record or an earlier chart entry, with a reference to it.
6. **What added it:** the human or system that wrote it into the chart.
7. **On whose behalf:** when a system adds something for another system or a human, the chart records both, and every hand it passed through.
8. This record is permanent and visible to anyone who can read the chart. Nothing in it is edited or deleted; a correction is a new dated addition that points to the old one. *(added)*

**D. Information and decisions**

9. Every addition is either information or a decision. *(added)*
   - Information is an observation, a reading, a statement, a message, a summary or a recommendation.
   - A decision changes the patient's care or what the chart holds as established: a diagnosis, an order, a prescription, a referral, a treatment plan or a sign-off.
   - The chart keeps the list of what counts as a decision.
10. Information needs only the identification above.
11. A decision counts as a decision only when its decision maker held the practice authority for that kind of decision at that moment. The chart records the authority each decision rested on.
    - For a human, that is their licence and what the medical director has granted them.
    - For an AI, it is the mode and scope the medical director has approved for it.
    - A device or a plain integration holds none.
12. When a system adds a decision on someone's behalf, authority is judged on the decision maker, never on the system that carried it. The decision counts as theirs only when the chart can confirm they made it, by their own sign-off or an approval of theirs it can check. Otherwise it is entered as the carrying system's recommendation. *(added)*

**E. A decision made without authority**

13. It is still entered in the chart, in full, as a recommendation and not a decision. Wherever it is shown, it is clearly marked "not an authorized decision" and says which authority was missing.
14. It has no effect. Nothing is ordered, prescribed, sent, billed or treated as established because of it. *(added)*
15. Whoever or whatever submitted it is told at once that it was entered as a recommendation. *(added)*
16. It goes in front of a clinician who holds the authority. They can adopt it, which creates a new decision of their own that points back to it, or decline it. Either way the recommendation stays in the chart as written. *(added)*
17. This applies to humans and systems alike: a staff member who enters an order they are not authorized to give is treated the same way as an AI.

**F. What does not change**

18. Missing authority is never a reason to refuse an addition. These still are: it cannot be identified, the patient cannot be matched, the content is malformed or test data, or the practice has suspended the writer. *(added)*
19. An AI holds no decision authority today. Advice only is the only mode that can be switched on, and limited and full practice stay specified and off (ruling 67). So everything an AI adds now is information or a recommendation.
20. An AI never manages an emergency, and the urgent hand-off to a human stays as it is.
21. Consent, the patient's link, the medical director's pause and narrowing, and clinician review of what an AI told a patient all still apply.
22. [Clause 22 of the approved wording stands here. It keeps two other parts of that private rule as they are, does not bear on the chart, and is recorded in full in the private companion change.]

**What it overrides.** "It supersedes, for additions to the chart, the first sentence of ruling 67 as the private companion change records it. The rest of ruling 67 stands: limited and full practice stay specified and off."

### Ruling 151: the owner's addition to clause 12

Source: **Brett Heap**, 2026-11-15, after "approve 80". His words, verbatim and not reorganized:

> "for 12, and any medical decision we need the practitioner to sign off. so we need a logged in user that we use their openWallet and openSig to sign and validate that entry. the openWallet will have the verification of license and the openSig will attest that it is from that wallet."

As the record of ruling 80 notes, where ruling 151 and a clause of ruling 80 differ, ruling 151 is the later ruling and governs. Ruling 151 names `opensoft/openWallet` and `opensoft/openSig`, both public repositories. It is being encoded with ruling 80.

## Relationships

Navigation over the "Owner rulings" tables of the changes listed above, which remain the source of truth. Companion to [Framing Decisions v2](openpractice-framing-decisions-v2.md), a draft for the owner's review, and to version 1, [Framing Decisions](openpractice-framing-decisions.md), which stays unchanged as history. Written under ruling 108.
