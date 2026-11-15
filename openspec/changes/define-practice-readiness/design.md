## Context

openPractice is an assembly root whose code leg is empty. Constitution v1.0.0 (`.specify/memory/constitution.md` in `opensoft/openPractice`, ratified 2026-08-24) is in force. Its principles are I (HIPAA by design), II (security review), III (test-first), IV (clinical boundary), V (versioned contracts) and VI (portability). The framing decisions FD-1 to FD-11 were ratified on 2026-08-27. FD-5 is the governance primitive: four fixed tiers, approval kept only for tier 4, and "the second human does not exist; approval queues rot within a month."

Five OpenSpec changes are merged on `main` at `0a82fc2`, all `Status: draft` and unratified. Each fails closed at its own gates. The written critique of 2026-11-15, item C.1 ("Fail-Closed Cold-Start Paralysis"), says that together they leave a small practice unable to go live until four authorities have signed nearly a dozen artifacts, with no statement of how those roles map onto one physician-owner. Its recommended action: keep fail-closed runtime enforcement, add one readiness checklist and a policy starter kit, and define the role mapping for one person in several roles.

The owner ruled on that finding on 2026-11-15 (ruling 107, below). This change encodes ruling 107. Where it goes further, the step is a design call marked PROPOSED, or an amendment proposed for another change's owner; the amendments RA-1, RA-3, RA-5 and RA-6 add conditions, notices or records that ruling 107 does not itself order (T-10). **Rulings are not ratification.** This change stays `Status: draft`, and `owner-ratification.md` stays PENDING until the owner completes it.

Requirement IDs `RC-nn` (`readiness-checklist`), `SK-nn` (`policy-starter-kit`) and `SR-nn` (`signer-roles`) prefix every requirement name in the three spec deltas. Requirement IDs of other changes are cited as those changes define them; a requirement of `add-reimbursement-retention-control` or `define-customer-financial-data-boundaries`, which carry no IDs, is cited by its name. Requirements that exist only on an open pull request are cited by ID and described, not quoted.

### Sources

- The five merged changes on `main`, read in full.
- Draft PR opensoft/openPractice-spec#7 (branch `change/follow-up-ruling-93-contract-gaps`, head `e746f58`), read at its head for the three changes it edits.
- The corrections change, draft PR opensoft/openPractice-spec#8 (branch `change/critique-2026-11-15-corrections`, stacked on PR #7). It was first read at `957c745`, and reconciled at `4bce39a` for fix round 1 and at `1cabb94` for fix round 2. It is still moving, so this change cites its requirement IDs and design-call IDs only.
- [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md): FD-1, FD-5 and the 2026-11-14 amendments.
- [`ideation/brainstorm/openpractice-domain-framework.md`](../../../ideation/brainstorm/openpractice-domain-framework.md) §5A.2.9, §5A.4 and D8: the service-code mapping and the accountant's approval items. Proposal status; ruling 30 adopted D8.
- The critique, `ideation/review/openpractice-spec-critique-2026-11-15.md`. Draft PR #8 adds it to the repository under ruling 111; it is not on `main`, so it is not cited by link.
- For ruling 98 only: the public `opensoft/openChart-spec` change `define-clinical-acts-foundation` (a draft, not merged; read at `28d604f`), located through the owner-rulings index on draft PR #9 (`docs/framing-decisions-v2`), which is navigation and not normative. The same index supplies the full accepted words of ruling 110.

## Owner rulings that govern this change

Rows 107, 98, 102, 104, 109 and 110 have the source **Brett Heap, 2026-11-15**: 98 among the decisions 79 to 98 accepted that day except 80, and 102 to 110 among the decisions 99 to 111 accepted that day in answer to the critique ("accept your recs for 99-111. explain 80 again"). Ruling 97 is from the same day and is recorded in PR #7. Ruling 133 is from the same day too, and is quoted with its question from PR #8's design tables. The other rows have the source **Brett Heap, 2026-11-14, authoring session**, and are quoted from the "Owner rulings" table of the change named. **Rulings are not ratification.**

| # | Ruling (accepted words, or the governing sentence of the table row) | Governs here |
|---|---|---|
| 107 | (One person in several roles.) "Allowed, with each sign-off recording the role used. A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change." | This change: SR-01 to SR-04, RC-01 to RC-07, SK-01 to SK-06 |
| 98 | (Confirming the first medical director.) "A second named person confirms; a one-administrator practice records licence verification against the board's public record." | The model one-person alternative (SR-03); P-4 below. No requirement in this repository encodes it; openChart's `define-clinical-acts-foundation` AU-01 does, for its own designation |
| 102 | (What "counsel to confirm" means in ruling 97.) "Design on your answer now, and make counsel's confirmation a go-live condition. Today one draft blocks all Hermes patient-data work until counsel confirms, and two others do not." | RI-47, encoded in PR #8's AH-06 and AH-01; SR-04 applies to the recorded confirmation |
| 104 | (What model providers may keep.) "Nothing: zero data retention, verified the way the BAA is. Deletion with a receipt applies to the agent operator only." | RI-46 and RI-49, encoded in PR #8's AH-02, AH-01 and AM-04; the deletion receipt of ER-12 shows on RI-17, RI-18 and RI-46 |
| 133 | (Ruling 104 says model providers keep nothing. May a provider hold a short-lived in-memory cache that is never written to disk or logs?) "Yes, with a short stated limit. Providers' zero-retention programs generally work this way, and nothing is stored or readable by a person. Counsel should confirm." (Recorded in PR #8.) | RI-46, RI-53; SR-04 applies to counsel's recorded confirmation (D-35) |
| 109 | (Promote to canonical specs before building.) "No. Ratify first, which is yours to do, then archive each change after its feature lands." | `tasks.md` §4; no canonical promotion |
| 110 | (Write the revenue-cycle core next and pause perimeter work.) "Yes. Its scope and decisions will follow as their own list." | T-7: this change adds no contract and proposes no change to a gateway contract |
| 97 | (Recorded in PR #7.) "No; the provider agreement is enough for practice operations, with counsel to confirm. Consent applies to AI answering the patient directly." | The confirmation that ruling 102 makes a go-live condition (RI-47) |
| 6 | "A practice-operated destination (for example self-hosted ERPNext) satisfies the BAA gate with an attestation naming the operator." (`define-mvp-practice-foundation`) | RI-07 |
| 12 | "Tier 4 applies only to hard delete and to enabling `customer-detail` mode, as a conscious FD-5 exception (T-13)." (`define-mvp-practice-foundation`) | RI-08 |
| 30 | "D8: the practice's accountant approves the reporting basis, estimator, classification, materiality and reopening policy before go-live." (`define-mvp-practice-foundation`) | RI-01, RI-02, SR-04, starter template 8 |
| 33 | "Agent signing is opt-in per action type and never on by default. Every sign-off records the signer's identity, including an agent's openWallet ID, with provenance. Tier 4 stays human-only." (`define-mvp-practice-foundation`) | SR-02 extends this record with the role; RI-22 |
| 37 | "Every action type starts at propose, and the practice opts each one up. Tier 4 stays human-only." (`define-actors-agents-and-work-inbox`) | RI-22, SK-02, SK-04, starter template 6 |
| 41 | "The medical director approves the hold policy, and the exact policy needs the practice's counsel." (`define-actors-agents-and-work-inbox`, whose row states the ruling in full) | RI-32, P-3, starter template 1 |
| 45 | "each answer type is switched on by the practice" (`define-actors-agents-and-work-inbox`) | RI-24, RI-25, starter template 9 |
| 50 | "A lapsed membership or a deactivation stops messaging only after the patient is told and a policy-set grace period has passed." (`define-patient-gateway-and-escalation`) | RI-33, starter template 4 |

## Goals / Non-Goals

**Goals**

- One place that shows every go-live gate and policy sign-off the other changes require, with its enforcing requirement, authority, holder, state and consequence.
- A starter template for each policy a gate needs, inert until signed, and signed exactly like a policy written from scratch.
- Ruling 107 encoded: several roles per person, the role on every sign-off, one-person alternatives for two-person rules, and no self-approved accountant or counsel sign-off.

**Non-Goals**

- The text of any policy, template, agreement or consent form, and any legal, accounting or clinical rule.
- Weakening a gate, adding a gate, or changing who signs a gate, beyond ruling 107's role record and self-approval bar. SR-04 adds no record and no precondition outside the sign-offs it names, except the engagement record, attestation and licence check for the accountant's sign-offs, disclosed as T-9.
- Editing another change. Every amendment below is proposed for its owner.
- A new contract, or any change to an existing contract.

## Decisions

### 1. One checklist that reads the gates and never stands in for them

A readiness item is recorded once per practice, counterparty, destination, agent, roster entry or version, and is either a precondition of a function under an existing requirement, or a version that a requirement gives a named authority to approve while a fail-closed default runs meanwhile, such as the routing-clock policy (design call RD-1). A sign-off made once per transaction, such as a batch release or a link confirmation, is a work item in the inbox (WI-07) and is not a row. Items the product owner decides at release, such as the grant sets of the new recipes, are shown read-only in their own section (RD-2).

Each row reads its state from the gate's own record (RC-03), and shows `unavailable` when that record cannot be read (RC-04). The checklist keeps no state that a gate consults, offers no write path except the gate's own action, and enables nothing. Its rows follow the requirements (RC-02): the inventory below is the starting set, a later change that alters a gate alters its row, a ruling that no requirement encodes produces no row, and a consequence found only in a design, task list or open question is never shown.

### 2. A starter kit of inert, versioned templates

The kit holds one template for each policy, profile or plan a readiness gate needs (SK-01): fifteen. A template is never in force (SK-02). A practice copies it into its own draft, and the authority the gate names approves that draft through the gate's own action, with the same record as a policy written from scratch (SK-03). The version records the template it came from as provenance only (RD-6). Nothing is adopted by default or in bulk (SK-04). A published template version never changes, and a newer one never touches a policy in force (SK-05). Templates assert no rule and may show only values a requirement already states (SK-06, RD-7); the autonomy opt-in set shows AO-04's caps, never a suggested rung.

### 3. Roles, and the two-person rules

A role is a recipe, an actor type, or a capacity a requirement names (RD-9). One person may hold several, each sign-off records the one role it was given in, and no authority comes from a combination of roles (SR-01, SR-02, RD-10, RD-17, RD-18). A rule that needs two different people fails closed until its owning change names a one-person alternative, and whenever the alternative it names is unavailable (SR-03). A rule that only loses its meaning when one person signs both sides keeps working, with every role recorded; the checklist lists the case, or counts it where its subject is a patient-side record (SR-03, RC-06, RD-19).

The accountant's sign-offs, and the counsel sign-offs that a requirement records, are never self-approved, and no one-person alternative applies to them (SR-04). Self-approval is tested on the engagement, not the recipe (RD-11): the sign-off is given by a named person engaged from outside the practice who is neither an owner nor an employee, gives no other sign-off on the same item, and is not an agent. For the accountant, the person attests in their own name, their licence is checked against the board's public record, and the licensee's name must match no other roster party or recorded owner; that adds a precondition and a record field, disclosed as T-9. SR-04 reaches only PH-03's and PG-06's recorded counsel reviews, AH-06's recorded confirmation under ruling 102, AH-02's recorded confirmation of a provider's cache under ruling 133, and the accountant's sign-offs. Where a requirement only describes counsel's part (AH-02's sentence on each agreement's form, PG-15, PG-16, and AH-06's sentence on the consent's form), it records no counsel sign-off, and SR-04 adds nothing there.

The one-person alternatives below are chosen in this order of preference (RD-13): another eligible named human, where the roster has one; an independent external record, as ruling 98 uses the board's public record; and, where neither exists, the sign-off given in both roles, each recorded, with the checklist listing or counting it.

## The inventory

Every row is a readiness item under RD-1. "Authority" is in the enforcing requirement's own words, or described where the requirement exists only on an open pull request. "Two people today?" asks whether an existing rule, or ruling 107, needs two different people for the item. "One-person alternative" is PROPOSED unless it is ruling 107's own text or the owning change's. RI numbers are this design's; the checklist uses the enforcing requirement IDs. Fix round 1 retired RI-26, RI-39, RI-42 and RI-44, whose consequences no requirement states (see "Not readiness items"), and added RI-48 to RI-52. Fix round 2 restored RI-35, which ER-09 makes a precondition of every tracked alert, and added RI-53. The inventory holds 49 items.

### A. Accounting go-live (`define-mvp-practice-foundation`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-01 | Revenue-recognition policy: reporting basis, estimator, receivable classification, materiality approach, reopening policy (D8) | AI-20, AI-12, BAB-17 (restated in WI-03, AO-04, MA-07) | The practice's accountant only; never an agent | No destination goes live | Yes, by ruling 107: the accountant may not be self-approved | None (SR-04): an accountant engaged from outside the practice signs |
| RI-02 | Estimator versions: the first, then each new one | AI-12, AI-20, BAB-17 | The practice's accountant only; never an agent | That version produces no estimate; the first is needed for go-live | As RI-01 | As RI-01 |
| RI-03 | Destination verification: line service dates and cash-basis reporting | AI-20 | Not named ("its adapter SHALL verify and record"); a destination without line service dates waits for "the owner" before customer-detail | That destination's go-live | No | — |
| RI-04 | Reason taxonomy defined before any entry posts | BAB-06 (the categories are separately governed) | Not named | No ledger entry posts | No | — |
| RI-05 | Account mapping per destination and posting mode | BAB-06; governance tiers ("Change an account mapping", tier 2) | No sign-off; tier 2 with notification | Release refused for any batch with an unmapped line | No | — |
| RI-06 | Legacy-collections account, for a practice whose deposits mix legacy collections with openPractice money | BAB-08, with BAB-06's mapping rule; Open Question 4 of that change | "The bookkeeper decides" | The legacy share of a mixed deposit has no mapped account, so a batch carrying that line is refused under BAB-06; nothing else waits | No | — |

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
| RI-13 | Vendor arrangements, including BAAs, for outsourced accounting and billing actors | "Accountant and biller access are independently bounded"; SR-04 | Not named | An outsourced accounting or billing actor's access is scoped to its arrangement. Under SR-04 (PROPOSED, RD-11), the practice's accountant signs only under such an arrangement, with the engagement entry created together with it, the accountant's own attestation and a licence check, so RI-01 and RI-02 wait on it (T-9) | No | — |

### D. Retention, lifecycle evidence and reimbursement authorization (`add-reimbursement-retention-control`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-14 | A jurisdiction selected for each record family | "Unselected jurisdiction blocks duration automation" | Not named | Automated retention duration, disposition horizon and elapsed-time disposition | No | — |
| RI-15 | Retention policy profile versions approved, including any exact Medicaid profile | "Retention policy profiles are versioned and sourced"; "Exact Medicaid duration profiles remain disabled until fully qualified and approved" | "Human approval" and a "review owner", neither named | That profile creates no obligation; a Medicaid profile stays disabled | Two roles named, not stated as two people | One person may hold both, each role recorded (SR-02); no amendment |
| RI-16 | A neutral public-envelope profile selected and pinned, optional | "Public envelopes conform to a selected version-pinned neutral profile" | Not named | Public witnessing | No | — |
| RI-48 | The authority and policy inputs of protected reimbursement operations: each actor's or workload's organization role and financial authority, and each payer or program profile | "Protected reimbursement operations are authorized before execution"; "Authorization fails closed" | Not named | Every billing, claim, remittance and payer-disclosure attempt whose required authority or policy input is missing, expired, revoked, unresolved or mismatched is refused before any protected side effect | No | — |

### E. Agents (`define-actors-agents-and-work-inbox`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-17 | Agent operator BAA | AH-02, AH-01 | "An authorized person" records and verifies. AH-02 describes counsel confirming each agreement's form, but records no counsel sign-off for it; the counsel confirmation AH-02 does record, for a provider's cache, is RI-53 | Every agent grant at any rung; all PHI to that path. While an agent operator's deletion confirmation under ER-12 is more than 72 hours late (PR #8, design call DC-57), AH-01 treats it as a lapse, and the row shows `lapsed` with that cause, though the agreement itself stays verified | No | — |
| RI-18 | Model-provider BAA for each provider on the path, with both agreements in a chain | AH-02, AH-01 | As RI-17 | As RI-17, including the ER-12 lapse for every path through that operator | No | — |
| RI-19 | Recipient profiles for the operator and each provider, per action type | AH-03, AH-01 | Not named | Every agent grant; fields outside the profile are withheld | No | — |
| RI-20 | Agent logging and untrusted-content controls recorded | AH-04, AH-05, AH-01 | Not named | Every agent grant | No | — |
| RI-21 | Agent roster entry: name, openWallet ID, active sponsoring human, dates | AM-04; governance tiers (tier 2) | Practice manager; named human only | The agent; without an active sponsor it drops to `observe` | No | — |
| RI-22 | Grants per action type, and rungs above `propose`, which are the BAB-17 agent-signing opt-ins | AO-03, AO-04, BAB-17 | Practice manager, or controller or office manager; named human only; tier 2 | The agent acts on nothing it holds no grant for, and every action type stays at `propose` | Loses meaning when the granter is the sponsor AO-03 notifies (P-8) | RA-3 |
| RI-23 | Rate and blast-radius limit values | AO-08; design call D-9; Open Question 2 of that change | Set in planning; changed by the practice manager or the controller or office manager, tier 2 | AO-08 fails closed while a limit is unset | No | — |

### F. Agent answers to patients (`define-actors-agents-and-work-inbox`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-24 | Read-only answer type and template versions | AH-05 exception 1 (conditions 1, 4 and 5); AO-11 | "A named human"; anything health-adjacent also the medical director; never opted in | That answer type sends nothing; its items go to staff | Two sign-offs for a health-adjacent type (P-6) | RA-4: one person gives both, each in its role, only where no other named human who may approve it is on the roster |
| RI-25 | Answer type switched on, with its cancel window and sampled-audit rate | AH-05 exception 1 (conditions 6, 7 and 10); AO-04; AO-11 | Practice manager, or controller or office manager; named human (AO-03) | The answer type stays at `propose`; the appointment-time and balance types also wait on RI-52 (AO-11) | The audit may be done by the template's own approver (P-10) | Roles recorded; no amendment |

### G. Remote VAs (`define-actors-agents-and-work-inbox`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-27 | VA company BAA | VA-01 | "An authorized person" records and verifies | Every VA's PHI access | No | — |
| RI-28 | VA device, session and location rules | VA-04 | "The practice approves"; exact rules set in planning | VA access, which fails closed | No | — |
| RI-29 | VA roster entries: named individuals under their company, each with an end date | VA-04, VA-05; governance tiers | Practice manager; named human | Access ends on the end date; no shared logins | No | — |

### H. Patient gateway (`define-patient-gateway-and-escalation`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-30 | HealthLinc operator agreement | PG-15 | "An authorized person" records and verifies. PG-15 describes counsel deciding what agreement is required and its form, but records no counsel sign-off | Every gateway message is refused | No | — |
| RI-31 | Proxy policy | PG-06; DC-12 | Medical director approves; counsel's review recorded; never opted in (DC-28) | Every representative link is refused | Yes: the medical director and counsel are two people (P-3) | None for counsel (SR-04) |
| RI-32 | Hold policy | PH-03 | Medical director approves; counsel's review recorded before approval; never opted in | No hold can be placed | Yes, as RI-31 (P-3) | None for counsel (SR-04) |
| RI-33 | Messaging-continuity policy | PG-04; DC-31 | Medical director; never opted in | Protective: a deactivation or a lapse withholds nothing ruling 50 protects | No | — |

### I. Clinical routing (`define-patient-gateway-and-escalation`)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-34 | On-call roster covering every hour, or naming the fallback | ER-13; DC-27 | Not named (practices configure it; the medical director is the fallback recipient) | Clinical routing, and so clinical and urgent intake, refused under PG-10; an administrative continuation accepted under ER-12 still goes to its administrative lane (PR #8) | Loses meaning when the on-call clinician is the medical director (P-9) | A record and a warning only (RA-2) |
| RI-35 | Second channel for tracked alerts | ER-09; DC-29 | Not named; ER-09 has the practice set it in planning | Not stated by ER-09, which requires every tracked alert to go through it as well as through the mobile approver | No | — |
| RI-36 | Routing-clock policy, with the alert-acknowledgment window, the re-send interval and the clinician-acknowledgment alert window | ER-08, ER-09; ER-20 (PR #8); DC-3, DC-29, DC-58 | Medical director; never opted in | Nothing waits: the fail-closed default windows apply, and the row says the default is in use (RD-1, second limb) | No | — |

### J. Relying parties (`define-patient-gateway-and-escalation`, PR #7)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-37 | The relying party's operator agreement | PG-16 (PR #7); DC-47 | An authorized person records and verifies the agreement that PG-16 says counsel requires; no counsel sign-off is recorded | No assertion for that audience, so no non-urgent consult with that relying party | No | — |
| RI-38 | Relying-party registration: issuer, capabilities, key-set location and pin | PG-16 (PR #7); governance tiers (PR #7) | Practice manager, or controller or office manager; named human; tier 2 | As RI-37 | No | — |

### K. Mobile approver (`define-patient-gateway-and-escalation`, as PR #8 amends it)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-50 | Each person's mobile-approver device enrolment, replacement and recovery, with the contact points recorded on their roster entry and, on the one-person path, their single-use recovery code | MA-04 (PR #8); design call DC-60; AM-01's contact points (PR #8; design call D-33) | A verification on another device the person has enrolled, or the confirmation of a second named person who holds the practice manager or office manager role, is never the person, and verifies afresh on their own enrolled device | The person cannot open an item or sign on the mobile approver, and works at the workstation; where no path is available, this holds until one is (MA-04, fail closed) | Yes, for a first device with no other enrolled device: a second named person (DC-60) | DC-60's own, under ruling 107: the issued recovery code, or a one-time code sent to each of two recorded contact points, at least one qualifying by age, with both entered. Either takes effect only after 24 hours, during which any notice recipient can cancel it, and a cancellation raises an exception (P-15) |

### L. Release prerequisites, decided by the product owner and shown read-only (RD-2)

| # | Item | Enforced by | Authority | What stays off until done |
|---|---|---|---|---|
| RI-40 | Grant sets of the five new recipes: remote VA, nurse, medical director, controller or office manager, agent | AM-02; D-1 | "The owner", before the recipe ships | Each recipe grants nothing, so every sign-off that the medical director or the controller or office manager gives waits on it |
| RI-41 | Identity verification mechanism for gateway links | PG-05; gateway Open Question 1 | Decided in planning | No link reaches `linked`, because PG-05 needs a verified identity |
| RI-43 | The assertion's pin form, registration record, key-removal authority and policy values | PG-16 (PR #7); gateway Open Question 18 (PR #7) | Planning; the owner approves | PG-16 records the pin at registration, so no relying party can be registered and no assertion is issued |
| RI-45 | The agent-operations MINOR for drafted replies and the hold action types | AO-11; the drafted-reply form in `define-patient-gateway-and-escalation`; DC-22 | Change A's owner | AO-11 refuses an agent request for an operation the deployed contract lacks, so agents get no drafted reply and no hold action |
| RI-51 | An actor type and a recipe, or a capacity, for the practice's accountant | AM-01, AM-02; AI-12, AI-20, BAB-17; I-1 | The owner of `define-actors-agents-and-work-inbox`, then the owner. Pending owner decision 141, to which PR #8 now points from AI-12, AI-20, BAB-17, WI-03 and MA-07 | AM-01 refuses every action from an actor whose type it cannot determine, so no D8 or estimator approval can be given and no destination goes live (AI-20) |
| RI-52 | The ratified scheduling change, and the patient receivables of `define-mvp-practice-foundation`, that two answer types read | AO-11; design call D-15 | The owner, by ratifying those changes | The appointment-time and balance answer types cannot be switched on |

### M. Agent go-live conditions in the corrections change (draft PR #8, stacked on PR #7, not merged)

| # | Item | Enforced by | Authority | What stays off until done | Two people today? | One-person alternative |
|---|---|---|---|---|---|---|
| RI-46 | Zero data retention at each model provider, for the endpoints and models it covers, verified the way the BAA is (rulings 104 and 133). The record states whether the arrangement allows a short-lived in-memory cache and, if so, the cache's stated limit, which may not exceed the ceiling of design call D-34 | AH-02, AH-01 (PR #8); D-30, D-34 | An authorized person records and verifies it on the BAA pattern | No PHI reaches that provider, endpoint or model, and no agent grant is made on that path. Shows `lapsed` under the ER-12 lapse, as RI-17 | No | — |
| RI-47 | Counsel's confirmation of ruling 97's answer, which ruling 102 makes a go-live condition | AH-06, AH-01 (PR #8); D-31 | An authorized person records it, naming the confirming counsel | No PHI reaches any model provider, and no agent grant is made on any path that uses one | Yes, by ruling 107, for counsel | None for counsel (SR-04) |
| RI-49 | Each agent's declared model path: provider, endpoint and model | AM-04, AH-02 (PR #8); D-32 | The agent operator declares it on the agent's roster record, and declares again before any change | PHI reaches an agent only when a verified BAA and a verified zero-data-retention arrangement both cover that exact provider, endpoint and model on its declared path, and, where that arrangement allows a cache of content, counsel's confirmation of ruling 133 is recorded (RI-53); otherwise none | No | — |
| RI-53 | Counsel's confirmation that a provider's in-memory cache is acceptable, for each path whose zero-data-retention arrangement allows one (ruling 133) | AH-02, AH-01 (PR #8); D-35 | An authorized person records it, naming the confirming counsel, on the pattern ruling 102 set for AH-06 | No PHI reaches a path whose arrangement allows a cache of content; a path whose arrangement allows no cache is not affected | Yes, by ruling 107, for counsel | None for counsel (SR-04) |

### Rulings that no requirement here encodes yet (no rows until one does, RC-02)

| # | Item | Ruling | Expected home | What it would keep off |
|---|---|---|---|---|
| — | Confirmation of the first medical director | 98 | Not in this repository: not on `main`, in PR #7 or in PR #8. Encoded in openChart: `opensoft/openChart-spec` `define-clinical-acts-foundation` AU-01 | openChart's designation of the first medical director. No openPractice function waits on it (Open Question 5) |

### The critique's citations, checked against the requirements

The critique's item C.1 cites fourteen requirement IDs. Each was read on `main`; the inventory follows the requirements, not the critique (RC-02).

| Cited ID | The critique's attribution | What the requirement says | Inventory |
|---|---|---|---|
| BAB-02 | Accountant: D8 policy and estimator | The accounting system is the books of record; it names no sign-off. The accountant-only D8 and estimator approvals are in AI-12, AI-20 and BAB-17 | RI-01, RI-02, through AI-12, AI-20 and BAB-17 |
| AI-06 | Accountant: D8 policy and estimator | A person releases each export batch, which is frozen at release: a per-transaction sign-off, not an accountant gate | Not a row (WI-07) |
| MA-07 | Accountant: D8 policy and estimator | Mobile sign-offs follow FD-5 and BAB-17, with re-authentication at signing; it restates that the D8 and estimator approvals are the accountant's only. PR #8 gives each sign-off its own verification (ruling 105) | Cited on RI-01 |
| PH-03 | Counsel; medical director | The medical director approves; counsel's review is recorded before approval | RI-32 |
| PG-06 | Counsel; medical director | The medical director approves; counsel's review is recorded on the policy | RI-31 |
| PG-15 | Counsel | An authorized person records and verifies the operator agreement; counsel deciding the agreement and its form is described, not recorded | RI-30 |
| AI-14 | Counsel | An authorized person records and verifies a BAA or an operator attestation; counsel is not named | RI-07 |
| AH-02 | Counsel | An authorized person records and verifies the BAAs; counsel confirming each agreement's form is described, not recorded. PR #8 adds verified zero data retention (rulings 104 and 133), the declared model path, and counsel's recorded confirmation of a provider's cache | RI-17, RI-18, RI-46, RI-49, RI-53 |
| VA-01 | Counsel | An authorized person records and verifies the VA company's BAA; counsel is not named | RI-27 |
| ER-08 | Medical director | The medical director's policy sets the windows, and fail-closed defaults apply until it does, so nothing waits on it | RI-36 |
| PG-04 | Medical director | Defines an active patient; the messaging-continuity policy the medical director approves (DC-31) is protective and blocks no go-live | RI-33 |
| ER-13 | Medical director: hour-by-hour on-call cover | Items route at every hour with on-call cover; it names no medical-director sign-off, and the medical director is the fallback recipient | RI-34 |
| AO-10 | Controller or manager | The controller or office manager signs each day's agent digest; a per-day sign-off that blocks nothing | Not a row (WI-07); P-7 |
| BAB-17 | Controller or manager: autonomy opt-ins | The signer rules: an agent signs only where opted in and never at tier 4, and the D8 and estimator approvals are the accountant's only. The opt-ins are granted under AO-03 and AO-04 | RI-01, RI-02, RI-22 |

Beyond the critique: draft PR #7 adds PG-16's relying parties (RI-37, RI-38, RI-43); PR #8 encodes rulings 102, 104 and 133 (RI-46, RI-47, RI-49, RI-53) and the mobile approver's enrolment and recovery (RI-50); and the retention change's reimbursement authorization (RI-48) and AO-11's wait (RI-52) were missing from the first draft.

### Not readiness items

- **Per-transaction sign-offs** (in the work inbox under WI-07, not on the checklist): EB-04, EB-05, EB-08, EB-09, EB-17, AI-06, AI-07, AI-08, PR-12, refunds and material adjustments at tier 4, PG-05 and PG-06 confirmations, PG-07, PG-08, PG-09, PH-04, ER-12's re-routing and its deletion request and receipt (whose lapse shows on RI-17, RI-18 and RI-46), AO-09, the AH-01 suspension lift, AO-10's daily digest, AH-06's per-patient grants, and the retention change's holds and disposition approvals. Each appears in the role analysis below where one person can sign both sides.
- **Technical setup with no human attestation:** the issuer (EB-02), destination credentials (AI-18), the ERPNext version pin, practice hours and staff lanes (ER-11, ER-13), and the integration operator of EB-15 (PR #7), who receives exceptions but signs nothing.
- **Product governance:** each change's own ratification, the mode continuity procedure (AI-09), the scheduling change PH-01 waits for, and per-feature security reviews.
- **Retired in fix round 1, because no requirement states what waits on them (RC-02):**
  - RI-26, the consent's form and wording. AH-06 (PR #7) describes counsel confirming it, but no gate approves it, and the per-patient grants are work items. Starter template 10 went with it.
  - RI-35 was retired here and restored in fix round 2: ER-09 requires every tracked alert to go through the second channel, so it is a precondition under RD-1's first limb, with no consequence stated.
  - RI-39, the risk-analysis entries of VA-02, AH-04 and the connected-ledger requirement. Nothing waits on them (I-7).
  - RI-42, the retention of administrative communication records. PG-10 states none; the earlier consequence came from a gateway open question and `tasks.md`.
  - RI-44, the AI-authorship disclosure. PG-11 already has HealthLinc show the patient when an agent wrote a reply, and no requirement makes anything wait on counsel's view (gateway Open Question 9).

## The starter kit

| # | Template | Serves | Signs the policy made from it | What the template asks for: the items its requirement names, plus prompts from that change's open questions |
|---|---|---|---|---|
| 1 | Hold policy | PH-03 | Medical director, with counsel's review recorded | Placement criteria; disputed balances and open GFE windows (PH-06); membership obligations (PH-07); countdown length; notice wording; lift conditions and review interval; scope across a guarantor's dependents (gateway Open Question 4) |
| 2 | Proxy policy | PG-06 | Medical director, with counsel's review recorded | Relationships covered; authority evidence; scope per relationship, including billing; expiry or review dates; minors' confidentiality (gateway Open Question 2) |
| 3 | Routing-clock policy | ER-08, ER-09, ER-20 | Medical director | Acknowledgment and response windows per urgency, ordered (ER-08 defaults shown as such); alert-acknowledgment window and re-send interval (DC-29 defaults shown as such); the clinician-acknowledgment alert window of ER-20 (PR #8; DC-58's default shown as such) |
| 4 | Messaging-continuity policy | PG-04 | Medical director | Grace period; notice template for `access_grace_started` and `access_restored` |
| 5 | On-call coverage plan | ER-13 | Not named (RI-34) | Every hour covered, or the fallback named; administrative cover |
| 6 | Autonomy opt-in set | AO-03, AO-04, BAB-17 | Practice manager, or controller or office manager, one opt-in at a time (SK-04) | Action types, each with the highest rung AO-04 allows and no suggested rung; every one starts at `propose` |
| 7 | Agent limit values | AO-08 | Practice manager, or controller or office manager | Actions per window and records per action, per action type |
| 8 | Revenue-recognition policy and first estimator approval | AI-12, AI-20 | The practice's accountant only, engaged from outside the practice (SR-04) | The five D8 items as questions, with no option chosen; the framework's §5A.4 items listed as prompts only |
| 9 | Read-only answer types | AH-05 exception 1; AO-11 | A named human; health-adjacent also the medical director | For each of the four families: template reference, allowlisted fields (no identifier for coverage on file), topics, cancel window, sampled-audit rate |
| 10 | Recipient profiles | AI-10, AI-23, AH-03, "Every business output has a scoped recipient profile" | Not named (RI-09, RI-11, RI-19) | The AI-23 shape: recipient, purpose, row scope, provenance, sensitivity, channels, each field's purpose, recipient and authority |
| 11 | Remote VA access rules | VA-04 | "The practice" (RI-28) | Device approval, idle timeout, session and location rules |
| 12 | Outreach policy | "Audience analysis does not authorize outreach" | Not named (RI-12) | Purposes, audience origins, channel authority, the authorizations and exceptions the practice relies on |
| 13 | Offering routes and membership entitlements | BAB-16; "Offerings determine financial routing at line level" | Not named (RI-10) | Route, owner, entity, category, mapping and entitlement per offering |
| 14 | Retention policy profile | "Retention policy profiles are versioned and sourced" | Not named (RI-15) | The profile fields, all empty: no duration, jurisdiction, program, provider type, effective date or source |
| 15 | Account mapping | BAB-06 | No sign-off (tier 2) | Reason category to account, per destination and mode; shipped only once a governed reason taxonomy exists |

The kit holds no agreement text and no consent form (SK-01). The agreements of RI-07, RI-17, RI-18, RI-27, RI-30 and RI-37 belong to their parties and to counsel, and AH-06 leaves the consent's form and wording to counsel.

## Rules that need two people, or lose their meaning when one person signs both sides

Where a use or sign-off below has a patient-side record as its subject (RC-06), the checklist counts it and never names anyone (RC-06, RD-19).

| # | Rule | Enforced by | Why one person is a problem | One-person alternative | Amendment |
|---|---|---|---|---|---|
| P-1 | Accountant sign-offs | AI-12, AI-20, BAB-17, WI-03, MA-07 | Ruling 107: never self-approved | None (SR-04): an accountant engaged from outside the practice | CA-2, conforming |
| P-2 | Counsel sign-offs that a requirement records | PH-03, PG-06; AH-06's confirmation of ruling 97's answer and AH-02's confirmation of a provider's cache under ruling 133 (PR #8) | Ruling 107: never self-approved | None (SR-04): counsel outside the practice | CA-3, CA-4, CA-9 and CA-10, conforming |
| P-3 | Medical director's approval and counsel's review of one policy version | PH-03, PG-06 | Needs two people under ruling 107 | The medical director side may be held with other roles; the counsel side is outside the practice (SR-04, third condition) | CA-3, CA-4 |
| P-4 | Confirming the first medical director | Ruling 98 | Two people, with ruling 98's own alternative | Ruling 98's own: a one-administrator practice records licence verification against the board's public record | None here. Encoded in `opensoft/openChart-spec` `define-clinical-acts-foundation` AU-01 for openChart's designation, not in this repository (Open Question 5) |
| P-5 | The decider is never the proposer | WI-03, ER-14 | The proposer is an agent or a patient, so one human decider suffices. The one-person case is a staff member deciding a proposal they made as a patient through the gateway (PG-08, PG-09). That is an act on their own record, which PR #8's PR-15 governs under its design call F-7, with its own one-person alternative | None proposed here. Which rule wins is pending owner decision 144, to which PR #8 now points from WI-03 and its actors Open Question 12 (I-13, Open Question 16) | None |
| P-6 | A health-adjacent answer type needs a named human's approval and the medical director's sign-off | AH-05 exception 1, condition 5 | Can be read as needing two people | **PROPOSED:** where no other named human who may approve it is on the roster, two sign-offs by one person who holds both roles, each recording its role (SR-02); where another is, the two sign-offs come from two people | RA-4 |
| P-7 | The controller or office manager signs the daily agent digest | AO-10, with AM-04 | When the signer sponsors an agent, the person accountable for the agent reviews its actions | **PROPOSED, beyond ruling 107:** another named human with the practice manager or the controller or office manager recipe who does not sponsor that agent signs that agent's lines; where none exists, the sponsor signs them in both roles, each recorded, and the digest marks them; the checklist lists it (RC-06) | RA-1 |
| P-8 | A grant is notified to the agent's sponsoring human | AO-03 | When the granting person is the sponsor, the notice goes to themselves | **PROPOSED, beyond ruling 107:** the notice also goes to every other holder of the practice manager or the controller or office manager recipe; where none exists, the grant records that the sponsor made it, and the checklist lists it (RC-06) | RA-3 |
| P-9 | An unacknowledged tracked alert escalates to the medical director; with nobody on call, the medical director gets it | ER-09, ER-13; DC-27; ER-20 (PR #8) | ER-09's escalation is not a two-person rule, but in a one-physician practice the on-call clinician is the medical director, so it reaches the same person. ER-20's alert to the office manager, and its cover, can reach that person too | **PROPOSED:** no change to any clinical path. ER-09 already keeps re-sending until someone acknowledges. The escalation is recorded, with the delivery record ER-09 keeps for each alert, as having reached no second person, and the checklist warns (RC-06). Whether a one-clinician practice should have to name a covering clinician in the medical director's ER-08 policy is Open Question 7, pending owner decision 143 | RA-2, a record only |
| P-10 | A sampled human audit reviews read-only answers | AH-05 exception 1, condition 10 | The auditor may be the template's own approver | **PROPOSED:** allowed; both roles recorded (SR-02, SR-03), and the checklist lists it | None |
| P-11 | An absent owner's items pass to a named cover, else to the controller or office manager | WI-02, WI-05; ER-20's alert, covered under WI-05 and, outside the office manager's hours, also sent to the administrative cover (PR #8) | With one person, the cover and the controller are the absent person | **PROPOSED:** the checklist warns (RC-06); clinical items keep ER-13's on-call path and ER-09's urgent fallback | None |
| P-12 | A staff member acts on a record where their own party holds a role: a money action on a balance they owe or guarantee, a change to their own coverage, guarantor assignment or deactivation, or an approval or sign-off on that record | PR-15 and its design call F-7 (PR #8); also PG-05, PG-06, PH-04 and BAB-17 refunds | The signer is on both sides | PR-15 states the rule under ruling 107: another human roster entry that holds the grant, or an agent whose sponsor is not the actor, decides. Its one-person alternative lets the person act, recording the role used and the own-party fact, with a notice to the practice's accountant that stays open until the accountant records the review. While no other roster entry holds the accountant capacity, the alternative is unavailable and the act waits (SR-03). The checklist counts each use, shows the count only to readers authorized to see that PHI, and names no one (RC-06, RD-19) | None while F-7 stands; RA-5 is the fallback if F-7 is rejected. I-14 records that F-7's alternative waits on RI-51 |
| P-13 | A retention profile has a review owner and a human approval | "Retention policy profiles are versioned and sourced" | Two roles, not stated as two people | One person may hold both, each role recorded (SR-02) | None |
| P-14 | An agent's sponsor decides that agent's proposal | WI-03, with AM-03 naming the sponsor as principal | The principal decides a proposal made on their behalf | **PROPOSED reading:** the agent is the proposer, so the sponsor may decide; both roles recorded. PR-15's F-7 separately bars an agent whose sponsor is the actor from deciding an act on the actor's own record | None |
| P-15 | Enrolling a first mobile-approver device needs another enrolled device or a second named person, never the person, who verifies afresh on their own enrolled device; a replacement or recovery confirmed by a second person waits 24 hours and can be cancelled | MA-04 and design call DC-60 (PR #8) | In a one-person practice there is no second named person | DC-60's own, under ruling 107: the person's single-use recovery code, or a one-time code to each of two recorded contact points, at least one qualifying by age. Each path takes effect only after 24 hours, any notice recipient can cancel it, and a cancellation raises an exception. With no path available, there is no mobile access (fail closed). The checklist lists each use (RC-06) | None |

## Required amendments (for each owning change's owner; this change edits none)

Each one-person alternative that needs another change's text takes effect only when that change's owner adopts it. Until then the rule stays as written, and this change only records the roles; the checklist lists or counts the case (SR-03). RA-1, RA-3, RA-5 and RA-6 go beyond ruling 107: they add a condition, a notice or a record that the rule does not have today (T-10). RA-5 is a fallback: PR #8's PR-15 already states its rule for every act RA-5 lists, so RA-5 is needed only if the owner rejects that change's design call F-7. Fix round 1 withdrew the clinical behaviours RA-2 first proposed: escalating to another clinician or a nurse, and sending emergency guidance for `priority` items. Any change to ER-09's escalation behaviour would touch the gateway contract, which ruling 110 pauses.

| # | Change and requirement | Effect | Current sentence | Proposed sentence |
|---|---|---|---|---|
| RA-1 | `define-actors-agents-and-work-inbox` AO-10 | Beyond ruling 107: creates a second-signer condition where none existed. It tightens only where a second eligible person exists, and refuses nothing that blocks operations, because an unsigned digest blocks nothing | "The digest SHALL enter the day close, and the controller or office manager, a named human, SHALL sign it." | "The digest SHALL enter the day close, and the controller or office manager, a named human, SHALL sign it. The lines for an agent whom that signer sponsors SHALL be signed by another named human who holds the practice manager or the controller or office manager recipe and does not sponsor that agent; where the practice has no such person, the signer SHALL sign those lines in both roles, each recorded, and the digest SHALL mark them as reviewed by the agent's own sponsor (`define-practice-readiness` SR-03)." |
| RA-2 | `define-patient-gateway-and-escalation` ER-09 | A record only, kept with the delivery record ER-09 already keeps for each alert, so ER-01's routing record is untouched. It changes no alert, recipient, message, clock, fallback or contract | "openPractice SHALL record each alert's delivery state and the recipient's acknowledgment." | Add after it: "When the medical director is the same person as the recipient of an escalated alert, openPractice SHALL record, with that alert's delivery state, that the escalation reached no second person (`define-practice-readiness` SR-03); the readiness checklist warns of that case under its RC-06." |
| RA-3 | `define-actors-agents-and-work-inbox` AO-03 | Beyond ruling 107: adds a notice and a record | "Granting or widening a scope SHALL be a tier-2 action with notification to the sponsoring human." | "Granting or widening a scope SHALL be a tier-2 action with notification to the sponsoring human; when the granting person is that sponsor, the notification SHALL also go to every other named human who holds the practice manager or the controller or office manager recipe, and where there is none, the grant SHALL record that the agent's own sponsor made it (`define-practice-readiness` SR-03), which the readiness checklist lists under its RC-06." |
| RA-4 | `define-actors-agents-and-work-inbox` AH-05 exception 1, condition 5 | Clarifies, under ruling 107; one person gives both sign-offs only where no second person may | "A named human approves each answer type and each template version. Anything health-adjacent also needs the medical director's sign-off. Neither approval SHALL be opted in for agent signing." | "A named human approves each answer type and each template version. Anything health-adjacent also needs the medical director's sign-off, given as a separate sign-off; only where no other named human who may approve it is on the roster, a medical director who is also the approving named human gives both, each recording its role (`define-practice-readiness` SR-02, SR-03). Neither approval SHALL be opted in for agent signing." |
| RA-5a | `define-patient-gateway-and-escalation` PG-05 | Fallback, only if PR #8's F-7 is rejected. Beyond ruling 107: tightens where a second eligible person exists | The second condition for `linked`: "a named human confirms, at tier 2, the registry record that the practice's invitation or an exact match (PR-08) identifies, after seeing it side by side with the identity claim." | Add after the list of conditions: "That named human SHALL NOT be the person the link is for, or the account holder, while another named human who may confirm it is on the roster; where none is, the confirmation SHALL record both roles and that the confirmer's own party holds a role on the record (`define-practice-readiness` SR-03), and the readiness checklist counts it under its RC-06 without naming anyone." |
| RA-5b | `define-patient-gateway-and-escalation` PG-06 | As RA-5a | "The link also needs the representative's verified identity and a named human's tier-2 confirmation of the authority." | Add after it: "That named human SHALL NOT be the representative or the patient while another named human who may confirm it is on the roster; where none is, the confirmation SHALL record both roles and that the confirmer's own party holds a role on the record (`define-practice-readiness` SR-03), and the readiness checklist counts it under its RC-06 without naming anyone." |
| RA-5c | `define-patient-gateway-and-escalation` PH-04 | As RA-5a | "Every placement and lift SHALL record the policy version, the coded criteria met, the actor and actor type (AM-03), the signer where one signs (BAB-17), the time and the provenance." | Add after it: "A named human SHALL NOT place or lift a hold on a patient whose balance they owe as patient or guarantor while another named human who may do so is on the roster; where none is, the act SHALL record both roles and that the actor's own party holds a role on the record (`define-practice-readiness` SR-03), and the readiness checklist counts it under its RC-06 without naming anyone." |
| RA-5d | `define-mvp-practice-foundation` BAB-17 | As RA-5a | "Tier-4 actions SHALL be signed by a named human only." | Add after it: "A named human SHALL NOT sign a refund leaving the practice to themselves, or to a guarantor account they hold, while another named human who may sign it is on the roster; where none is, the sign-off SHALL record both roles and that the signer's own party holds a role on the record (`define-practice-readiness` SR-03), and the readiness checklist counts it under its RC-06 without naming anyone." |
| RA-6 | `define-mvp-practice-foundation` PR-15 (PR #8) | Beyond ruling 107: adds a record, so that SR-04 can test that an accountant or counsel is not an owner. No requirement records the practice's ownership today. Pending owner decision 145 | PR-15's list of what parties are, where the practice is a party of type organization (described; PR-15 exists only on PR #8) | Add: "The practice's owners SHALL be recorded as dated links from the practice's organization party to each owner's party, with provenance, and audited (PR-10) (`define-practice-readiness` SR-04)." |

## Conforming amendments (recommended; SR-02 and SR-04 already bind)

SR-02 and SR-04 apply to every sign-off they name, and none of the sentences below permits what they forbid, so there is no conflict while these wait. Each makes the owning requirement say the rule where its sign-off is defined. CA-5 to CA-8 became conditional in fix round 1, because the requirements they name record no counsel sign-off (finding A).

| # | Change and requirement | Current sentence | Proposed sentence |
|---|---|---|---|
| CA-1 | `define-mvp-practice-foundation` BAB-17 | "Every sign-off SHALL record the signer's identity, including the openWallet ID for an agent, with the action, the time and the provenance." | "Every sign-off SHALL record the signer's identity, including the openWallet ID for an agent, the role under which it was given (`define-practice-readiness` SR-02), the action, the time and the provenance." |
| CA-2 | `define-mvp-practice-foundation` BAB-17, AI-12 and AI-20 | BAB-17: "The D8 approval and every estimator-version approval SHALL be given by the practice's accountant only, and SHALL NOT be opted in for agent signing (accounting-integration AI-12, AI-20)." AI-12 and AI-20: "The D8 approval and every estimator-version approval SHALL be given by the practice's accountant only, and neither SHALL be opted in for agent signing (BAB-17)." | Append to each: "Neither SHALL be self-approved (`define-practice-readiness` SR-04)." |
| CA-3 | `define-patient-gateway-and-escalation` PH-03 | "Counsel's review SHALL be recorded on each version, by reference, before approval." | "Counsel's review SHALL be recorded on each version, by reference and naming the counsel who gave it, before approval, and SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |
| CA-4 | `define-patient-gateway-and-escalation` PG-06 | "The practice's medical director approves the policy, and counsel's review is recorded on it." | "The practice's medical director approves the policy, and counsel's review is recorded on it, naming the counsel who gave it; that review SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |
| CA-5 | `define-actors-agents-and-work-inbox` AH-02 | AH-02 describes counsel confirming each agreement's form and records no counsel sign-off for it (its recorded confirmation of a provider's cache is CA-10) | **Conditional:** only if the owning change comes to record counsel's confirmation of an agreement's form. Then add to that record: "That confirmation SHALL NOT be self-approved (`define-practice-readiness` SR-04)." Until then, nothing |
| CA-6 | `define-patient-gateway-and-escalation` PG-15 | PG-15 describes counsel deciding what agreement is required and its form, and records no counsel sign-off | **Conditional**, as CA-5, if PG-15 comes to record counsel's decision |
| CA-7 | `define-actors-agents-and-work-inbox` AH-06 (PR #7) | AH-06 describes counsel confirming the consent's form and wording, and records no counsel sign-off | **Conditional**, as CA-5, if AH-06 comes to record that confirmation |
| CA-8 | `define-patient-gateway-and-escalation` PG-16 (PR #7) | PG-16 has an authorized person record and verify the agreement counsel requires, and records no counsel sign-off | **Conditional**, as CA-5, if PG-16 comes to record counsel's decision |
| CA-9 | `define-actors-agents-and-work-inbox` AH-06 (PR #8) | AH-06's paragraph that makes counsel's confirmation of ruling 97's answer a go-live condition, recorded by an authorized person naming the confirming counsel (design call D-31). Described, not quoted, because PR #8 is still moving | Add after that paragraph's recording sentence: "That confirmation SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |
| CA-10 | `define-actors-agents-and-work-inbox` AH-02 (PR #8) | AH-02's paragraph on counsel's confirmation that a provider's in-memory cache is acceptable (ruling 133; design call D-35), recorded by an authorized person naming the confirming counsel. Described, not quoted, because PR #8 is still moving | Add after that paragraph's recording sentence: "That confirmation SHALL NOT be self-approved (`define-practice-readiness` SR-04)." |

## Governance tiers (FD-5) for actions this change adds

| Action | Tier | Signer (BAB-17) | Agent ceiling (AO-04) |
|---|---|---|---|
| Read the checklist | Not an action | — | No agent action type exists in 1.0 |
| Create a practice draft from a template | 1 (do + undo) | No sign-off; a person whose recipe may prepare that policy (planning) | No agent action type exists in 1.0 |
| Approve a version made from a template | The gate's own tier | The gate's own signer, exactly as for a version written from scratch (SK-03) | The gate's own ceiling; never above `propose` for any approval that is never opted in |
| State the role at signing | Part of each sign-off | The signer | — |
| Use a one-person alternative | As the owning requirement, once adopted | As that requirement | As that requirement |

**Deadline escapes (FD-5).** No action this change adds holds anything deadline-bearing. The checklist holds nothing. SR-04's refusals hold the D8 approval, estimator versions, the PH-03 and PG-06 counsel reviews and the counsel confirmations recorded under AH-06 and AH-02, none of which stalls a claim: accounting postings carry no filing deadline (`define-mvp-practice-foundation`, "Deadline escapes"), holds and representative links are refused meanwhile, and the agent paths wait under AH-01 as they already do. SR-03's fail-closed default applies only to rules that already need two people, each of which keeps its own escape.

## Constitution Check (pre-handoff, against v1.0.0)

| Principle or gate | Obligation | Status |
|---|---|---|
| I. HIPAA by design | The checklist holds no PHI: a use whose subject is a patient-side record appears only as a count, and the count and its opaque reference to the audit entries are shown only to readers authorized to see that PHI (RC-06, RC-07). No gate is relaxed (RC-03). The accountant and counsel stay independent (SR-04). | Requirement level |
| II. Security review | The feature touches authorization: who may sign, and in which role. It needs a security review and a risk-analysis update. Nothing autonomous is added; no template is adopted by default (SK-04). | Owed per feature |
| III. Test-first | Tests cover RC-03 (no write path, nothing enabled), the RC-04 states including `unavailable`, the RC-06 count-only rule, SK-02 to SK-05, the SR-02 role refusal and the SR-04 refusals. | Owed per feature |
| IV. Clinical boundary | No clinical content anywhere. Templates assert no clinical standard (SK-06). No clinical path changes (RA-2 is a record only). | Satisfied |
| V. Versioned contracts | No contract is added or changed. The checklist is read through the versioned API (RC-07). | Satisfied |
| VI. Portability | Nothing vendor-specific. | Satisfied |
| AGENTS.md: no autonomous financial or patient-impacting actions by default | No template is adopted by default, no opt-in is taken in bulk, and every action type still starts at `propose` (SK-04). | Satisfied |

## Design calls for owner review

Each item below goes beyond ruling 107. Each is PROPOSED, not ruled, and each is also routed as an open question.

| # | Design call | Where |
|---|---|---|
| RD-1 | **PROPOSED.** The inclusion rule: a readiness item is recorded once per practice, counterparty, destination, agent, roster entry or version, and either makes a function wait under a requirement, or is a version a requirement gives a named authority to approve while a fail-closed default runs meanwhile; per-transaction sign-offs stay in the work inbox. A consequence found only in a design, task list or open question gives no row. | RC-01, RC-02 |
| RD-2 | **PROPOSED.** Release prerequisites the product owner decides are shown read-only in their own section, not as practice rows. | RC-01; section L |
| RD-3 | **PROPOSED.** Six row states, including `unavailable` when the gate's record cannot be read, so a row never shows a stale state; `not_required` comes only from the enforcing requirement's own condition read from configuration, never from a person's mark. | RC-04 |
| RD-4 | **PROPOSED.** Where a requirement names no authority, the row says so and the checklist names none; the gaps go to their owners (I-4). | RC-02 |
| RD-5 | **PROPOSED.** Rows are grouped by the function they gate, so a practice that turns a function off sees which rows it no longer needs. | RC-01 |
| RD-6 | **PROPOSED.** A version made from a template records the template's identifier and version as provenance only. | SK-03 |
| RD-7 | **PROPOSED.** A template may show only values an existing requirement already states, marked as that requirement's; everything else is blank, and the opt-in set shows AO-04's caps, never a suggestion. | SK-01, SK-04, SK-06 |
| RD-8 | **PROPOSED.** A newer template version is shown beside each version started from an earlier one, and is never applied. | SK-05 |
| RD-9 | **PROPOSED.** A role is a recipe, an actor type, or a capacity a requirement names as a signer or an owner of work. SR-04 says when holding the accountant's engagement entry does not count as another role. | SR-01, SR-04 |
| RD-10 | **PROPOSED.** One act counts for one role; a person signing in two roles gives two sign-offs. This matches MA-07 as PR #8 words it under ruling 105, where each mobile sign-off has its own verification. | SR-02 |
| RD-11 | **PROPOSED, under ruling 107.** Self-approval is tested on the engagement, not the recipe. An accountant's or counsel's sign-off is given by a named person engaged from outside the practice: the accountant under the vendor arrangement of `define-customer-financial-data-boundaries` "Accountant and biller access are independently bounded", and counsel named on the record. That person is neither an owner nor an employee, gives no other sign-off on the same item, and is not an agent. For the accountant, "neither an owner nor an employee" is shown by three things: the accountant's own attestation in their own name; a check of their licence against the licensing board's public record, the independent record of RD-13; and a match of the licensee's name against the parties behind every other roster entry and the recorded owners, where any match refuses. The engagement roster entry is created together with the vendor arrangement and is never an existing entry relabeled. It may hold the bookkeeper recipe and the actor type that pending owner decision 141 settles, and holding it is not another role; any other roster entry makes the person an employee. Until the owner answers Open Question 14, an accountant employed by the practice cannot give the accountant's sign-off. Counsel needs no roster entry or actor type. Pending owner decisions 139 and 140 bear on this call; it picks no side beyond refusing what it cannot show. | SR-04 |
| RD-12 | **PROPOSED.** A one-person alternative applies only when the roster holds no second eligible person at the moment of the act; each use is recorded, and the checklist lists or counts it. | SR-03 |
| RD-13 | **PROPOSED.** Order of preference for one-person alternatives: another eligible person, then an independent external record as ruling 98 uses, then the sign-off in both roles, each recorded, with the checklist listing or counting it. RD-11 uses the external-record route for the accountant's licence. | Decision 3; RD-11 |
| RD-14 | **PROPOSED.** The alternatives of P-5 to P-15, the amendments RA-1 to RA-4 and RA-6, and RA-5 as a fallback. | Tables above |
| RD-15 | **PROPOSED.** Handoff: exactly one Speckit feature. Its plan lands SR-02 and SR-04 in the shared sign-off record before any sign-off is recorded in production, and adds each checklist row with the feature that builds its gate. | `tasks.md` §2 |
| RD-16 | **PROPOSED.** Which recipes may read the checklist is decided in planning; it holds no PHI either way, because of RD-19. | RC-07 |
| RD-17 | **PROPOSED.** No authority is inferred from a combination of roles; each role gives its own authority and nothing more. | SR-01 |
| RD-18 | **PROPOSED.** The signer states the role at signing, and the sign-off is refused when the stated role is not held at that moment, or when the governing requirement names the roles allowed and the stated role is not among them. Where the requirement names no role, as for the records "an authorized person" makes, any role the signer holds is accepted and recorded; which role that is, is pending owner decision 142. | SR-02 |
| RD-19 | **PROPOSED.** A one-person use or a both-sides sign-off whose subject is a patient-side record (RC-06) appears on the checklist only as a count for its rule, with an opaque reference that opens the audit entries only under PHI access control; never a name, a role in a patient record or an amount. Because a count can point to the one person who could have acted, the count and its reference are shown only to readers authorized to see that PHI. | RC-06, RC-07; P-12, RA-5 |

## Conflicts and tensions

| # | Tension | Status |
|---|---|---|
| T-1 | **FD-5** says "the second human does not exist; approval queues rot within a month." | **Consistent.** This change adds no approval. Ruling 107's one person in several roles matches FD-5's premise. SR-04 departs from it only for the accountant and counsel, by ruling 107. |
| T-2 | **FD-1** rejects configuration surfaces that ask a practice to author policy it never articulated. | **Conscious exception proposed.** The kit lowers the authoring burden FD-1 warns about, and the checklist replaces a scatter of gates with one view. Both are fixed and shipped; the practice authors no rule. |
| T-3 | **AM-01** declares exactly nine actor types, and none is the practice's accountant, yet the accountant must act in openPractice to sign. | **Recorded.** SR-04 tests the engagement, not the actor type (RD-11). PR #8 gives every human roster entry one party (AM-01 as corrected, PR-15), which lets SR-04 compare a named accountant or counsel with the parties behind roster entries. The accountant's actor type and recipe remain a release prerequisite (RI-51, Open Question 3, pending owner decision 141). Counsel needs none. |
| T-4 | **AH-05 exception 1, condition 5** can be read as needing two different people for a health-adjacent answer type. | **RA-4 proposed**, on ruling 107's words: one person gives both sign-offs only where no other named human may approve. |
| T-5 | SR-02 and SR-04 bind sign-offs defined in other changes before CA-1 to CA-4, CA-9 and CA-10 land, so the rule has two homes for a while. PR #8's PR-15 also states the self-approval bar for own-record acts, and relies on SR-03's fail-closed rule and its record of each use. | **Recorded.** No conflict: no target sentence permits what SR-02 or SR-04 forbids, SR-04 supplies the meaning of "self-approved" (RD-11) that PR-15 leaves open, and SR-03 states the fail-closed rule PR-15 cites. |
| T-6 | Rulings 102 and 104 are encoded only in PR #8, which is unmerged and still moving. | **Deliberate.** RI-46, RI-47 and RI-49 cite that change's requirement IDs; under RC-02 they follow whatever it finally says. |
| T-7 | **Ruling 110** pauses perimeter work. | **Consistent.** This change adds no contract and proposes no change to any contract. Fix round 1 withdrew RA-2's escalation to other clinicians and its emergency guidance for `priority` items, which would have reversed DC-30 and changed the gateway contract's description and digest. Rows that depend on the Revenue Cycle Core (RI-04, RI-05, starter template 15) follow that work when it lands (RC-02). |
| T-8 | **Ruling 109**: no promotion to canonical specs before building. | **Consistent.** `tasks.md` archives this change only after its feature lands. |
| T-9 | SR-04's engagement test makes the accountant's sign-offs wait on a recorded outside engagement: an engagement roster entry created with its vendor arrangement, the accountant's own attestation, and a check of their licence against the board's public record. That is a precondition and a record field that no requirement had before. Until the owner answers Open Question 14, an accountant employed by the practice cannot give the D8 or estimator approval either, which narrows who can meet AI-20's gate. | **Deliberate, PROPOSED (RD-11), and disclosed in the proposal and in SR-04.** Ruling 107 says accountant sign-offs cannot be self-approved. That an employee approving the books they keep is self-approval is this design's reading, not the ruling's words. The test refuses rather than guesses (fail closed). Pending owner decisions 139 and 140 settle it. |
| T-10 | RA-1, RA-3, RA-5 and RA-6 add conditions, notices or records that the rules do not have today, which goes beyond ruling 107. | **Recorded.** Each is PROPOSED for its owner and changes nothing until adopted; SR-03 needs none of them. |

## Inconsistencies found while building the inventory (reported, not fixed)

| # | Finding | Where |
|---|---|---|
| I-1 | The practice's accountant has no actor type in AM-01 and no recipe in AM-02, yet six requirements make that person the only signer of the D8 approval and estimator versions, and AM-01 refuses any action from an actor whose type it cannot determine. As specified, nobody can give the D8 approval, so AI-20 cannot be met (RI-51); PR #8 now says so, pointing at pending owner decision 141. Counsel needs no actor type: an authorized person records counsel's review or confirmation by reference. | AM-01, AM-02; AI-12, AI-20, BAB-17, WI-03, AO-04, MA-07 |
| I-2 | BAB-10 restricts full bank numbers to "the owner and billing-administrator roles", and neither is a recipe in AM-02. | BAB-10, AM-02 |
| I-3 | Agreement and evidence records say "an authorized person" without naming which role is authorized: AI-14, AH-02, VA-01, PG-15 and PG-16. PR #8 adds four more of the same kind: the zero-data-retention record (AH-02), the records of counsel's confirmations under rulings 102 (AH-06) and 133 (AH-02), and the interim deletion request and receipt for ER-12, which an authorized person sends and records (DC-57). Which role that is, is pending owner decision 142; until it is ruled, SR-02 accepts and records any role the signer holds (RD-18). | AI-14, AH-02, VA-01, PG-15, PG-16; AH-02, AH-06 and ER-12 (PR #8) |
| I-4 | Gates whose requirement names no authority: jurisdiction selection and profile approval in the retention change; public-envelope profile selection; the authority and policy inputs of reimbursement operations; recipient profiles (AI-10, AI-23, AH-03, business outputs); approved membership terms and offering routes; the outreach policy; outsourced-firm vendor arrangements; VA session and location rules; the on-call roster and the second alert channel; the reason taxonomy; the destination verification record. AH-05 condition 5's "named human" is tied to no recipe, while switching the same answer type on is (AO-03). | RI-03, RI-04, RI-09 to RI-16, RI-19, RI-20, RI-28, RI-34, RI-35, RI-48; AH-05 |
| I-5 | "The owner" in AI-20 ("until the owner decides how to proceed") could mean the practice owner or the product owner, and so could the owner named in gateway Open Question 10. AI-09 ("until the owner ratifies a continuity procedure") and AM-02 ("approved by the owner before the recipe ships") clearly mean the product owner. | AI-20; gateway Open Question 10 |
| I-6 | PH-03 records counsel's review "before approval"; PG-06 records it "on" the version and says nothing of order. | PH-03, PG-06 |
| I-7 | Security reviews and risk-analysis entries appear only in designs and tasks, or are named in a requirement with nothing waiting on them, so RC-02 can give them no row. The first group covers each customer-detail destination, agent grant, opt-in above `propose`, answer type and VA company. The second covers the risks named in VA-02, AH-04 and the connected-ledger requirement (the retired RI-39). | Foundation proposal "Impact", design Constitution Check, `tasks.md` 2.3; actors design Constitution Check, `tasks.md` 3.1; VA-02, AH-04 |
| I-8 | Every medical-director and controller-or-office-manager sign-off waits on AM-02's grant-set approval for those recipes, which is a release act by the product owner. Until it is made, no practice can approve a hold, proxy, routing-clock or messaging-continuity policy, sign a digest or grant an agent. | AM-02 with PH-03, PG-06, ER-08, PG-04, AO-10, AO-03 |
| I-9 | In a one-physician practice, DC-27's on-call cover can be met by one person who is also the medical director. Then ER-09's escalation, ER-13's fallback and PR #8's ER-20 alert, with its WI-05 cover and its out-of-hours copy to the administrative cover, can all reach the person who did not answer. | ER-09, ER-13, DC-27; ER-20 |
| I-10 | Ruling 98 is not encoded anywhere in this repository: not on `main`, in PR #7 or in PR #8. It is encoded in `opensoft/openChart-spec` `define-clinical-acts-foundation` AU-01 (draft, not merged; read at `28d604f`), for openChart's designation of the first medical director. No openPractice requirement puts the same check on giving a person the medical director recipe, so the checklist has no row for it (RC-02). | AM-02; openChart-spec AU-01 |
| I-11 | The critique's citations differ from the specs (table "The critique's citations, checked against the requirements"). BAB-02 and AI-06 do not concern the accountant (D8 lives in AI-12, AI-20 and BAB-17). ER-13 names no medical-director sign-off for the on-call roster. AO-10's unsigned digest blocks nothing. ER-08 blocks nothing, because defaults apply. PG-04 keeps cut-offs off rather than blocking go-live. AI-14 and VA-01 do not name counsel, and AH-02, for each agreement's form, and PG-15 describe counsel's part without recording it. | Critique item C.1 |
| I-12 | The framework brainstorm's §5A.2.9 says changes to the mapping from openPractice service codes to categories need approval. No requirement on `main` defines that mapping or its approval, which belong to the Revenue Cycle Core, so it has no row. It is a different mapping from the category-to-account mapping, which the foundation's governance tiers make tier 2 with no sign-off (RI-05). | Framework §5A.2.9; foundation governance tiers |
| I-13 | WI-03 says the decider is never the proposer. PR #8's PR-15 (F-7) lets a staff member in a one-person practice act on their own record, which could include deciding a proposal they made as a patient through the gateway. The two do not say which wins. It is pending owner decision 144; PR #8 now records it as its actors Open Question 12 and picks no side. | WI-03; PR-15 (PR #8); PG-08, PG-09 |
| I-14 | PR-15's one-person alternative (PR #8) needs a roster entry other than the actor that holds the practice's accountant capacity. Under I-1 no actor type or recipe supports one, so until RI-51 is settled (pending owner decision 141) the alternative is unavailable in every practice, and own-record acts with no second person wait. | PR-15 (PR #8); AM-01, AM-02 |

## Dependencies

- **On `main` (`0a82fc2`):** the five merged changes. Every requirement ID cited here exists on `main`, except those listed next.
- **Only in draft PR #7** (`change/follow-up-ruling-93-contract-gaps`, head `e746f58`): AH-06's consent and the actors design calls D-22, D-23 and D-27 to D-29 it cites; the integration operator in EB-15; PG-16, design call DC-47 and the gateway governance-tier row for relying-party registration; actors Open Question 9 and gateway Open Question 18; ruling 97. This change should merge after PR #7. If PR #7 changes before it merges, RI-37, RI-38, RI-43, CA-7 and CA-8 follow it.
- **The corrections change, draft PR opensoft/openPractice-spec#8** (`change/critique-2026-11-15-corrections`, stacked on PR #7, reconciled at `1cabb94` for fix round 2, and still moving). It carries:
  - rulings 102, 104 and 133 in AH-01, AH-02 and AH-06, with design calls D-30, D-31, D-34 and D-35, and the declared model path in AM-04 and AH-02 (RI-46, RI-47, RI-49, RI-53, CA-9, CA-10);
  - pointers to pending owner decisions 141 (from AI-12, AI-20, BAB-17, WI-03 and MA-07) and 144 (from WI-03 and its actors Open Question 12) (RI-51, I-1, I-13, I-14);
  - PR-15 and its design call F-7 (P-5, P-12, RA-5, RA-6, I-13, I-14);
  - ER-12's deletion receipt and design call DC-57 (RI-17, RI-18, RI-46, I-3);
  - ER-20 and DC-58, including the alert's WI-05 cover and its out-of-hours copy to the administrative cover (RI-36, starter template 3, P-9, P-11, I-9);
  - MA-04 and DC-60, with the roster contact points of AM-01 and design call D-33 (RI-50, P-15);
  - the exception in PG-10 and ER-13 for an administrative continuation accepted under ER-12 (RI-34);
  - AM-01's party reference (T-3);
  - MA-07's per-sign-off verification (RD-10).

  PR-15 and ER-20 exist only there. This change should merge after it, and follows it if it changes (`tasks.md` 1.2).
- **Rulings 98 to 111** are recorded here only as far as they govern this change; their own homes are the changes that encode them.

## Risks / Trade-offs

- **[The checklist is mistaken for a gate]** → It has no write path and enables nothing (RC-03), and shows `unavailable` rather than a stale state (RC-04); tests prove it.
- **[The checklist drifts from the requirements]** → Rows derive from requirements and follow them (RC-02); a ruling with no requirement has no row, and a consequence from a design or task list is never shown.
- **[The checklist reveals that a staff member is a patient]** → A use whose subject is a patient-side record is only counted, and the count and its opaque reference are shown only to readers authorized to see that PHI (RC-06, RD-19).
- **[A template is treated as approved policy]** → Templates are never read by a gate (SK-02); approval is the gate's own act (SK-03); nothing is adopted by default or in bulk (SK-04).
- **[Templates give legal or clinical comfort they cannot give]** → Each carries a statement that it asserts no rule (SK-06); this change writes no content.
- **[One person signs everything without anyone noticing]** → Every sign-off records its role (SR-02); the checklist shows each person's roles and lists or counts every case where one person signed both sides (RC-06).
- **[A solo practice cannot find an accountant or counsel]** → Ruling 107 accepts that cost for the sign-offs SR-04 names; SR-04 says so plainly, adds no counsel sign-off elsewhere, and the checklist shows the need.
- **[An employed accountant cannot sign]** → Deliberate until the owner answers Open Question 14 (T-9).
- **[An owner clears themselves as the outside accountant]** → The accountant attests in their own name, the licence is checked against the board's public record, the licensee's name must match no other roster party or recorded owner, and the engagement entry is created with its vendor arrangement, never relabeled (RD-11).
- **[Many amendments for other owners]** → Required amendments change behavior only when adopted (SR-03); conforming amendments restate what SR-02 and SR-04 already bind, and CA-5 to CA-8 apply only if their owners come to record counsel's part.

## Migration Plan

1. Validate strictly. Merge after PR #7 and PR #8. The owner completes `owner-ratification.md`.
2. Each owning change's owner dispositions RA-1 to RA-4 and RA-6, the fallback RA-5 if it is needed, and CA-1 to CA-10, of which CA-5 to CA-8 are conditional.
3. One Speckit feature (RD-15): the role record and the self-approval bar first, built into the shared sign-off record; then the checklist and the kit, with each row added as its gate's feature lands.
4. Template content is drafted and reviewed outside this change before the kit ships (Open Question 4).
5. Rollback disables the checklist view or the kit; no gate is affected, because none depends on either.
6. Archive only after the feature lands and `closure-evidence.md` is complete (ruling 109).

## Open Questions

Every question is routed in `tasks.md` §3.4 to the work it blocks. Where the owner has a pending decision on a question, its number is given; none is ruled, and this change encodes none of them.

1. **The inclusion rule and the release-prerequisite section** (RD-1, RD-2, RD-5), including the second limb for policy versions with a fail-closed default, the four rows retired in fix round 1 and RI-35, restored in fix round 2. The owner.
2. **The reading of "self-approved"** (RD-11): the engagement test, the employee rule, and for the accountant the own attestation, the licence check against the board's public record and the name match. Pending owner decisions 139 and 140.
3. **The accountant's actor type and recipe** (RI-51, T-3, I-1): which actor type and recipe the roster entry for an outside accountant's engagement holds. Counsel needs none. Pending owner decision 141.
4. **Who drafts and reviews starter-template content before it ships**, and with which counsel, accountant and medical-director review. Pending owner decision 147.
5. **Ruling 98**: openChart encodes it, for its own designation of the first medical director (`opensoft/openChart-spec` `define-clinical-acts-foundation` AU-01). Whether openPractice's assignment of the medical director recipe should carry the same check is open; it is not proposed here, because it would add a gate. Pending owner decision 146.
6. **RA-1 to RA-4 and RA-6**, RA-5 if the owner rejects PR-15's F-7, and the conditional CA-5 to CA-8: accept, change or reject each. RA-1, RA-3 and RA-4 are pending owner decision 148, and RA-6 owner decision 145. The owner of each change, then the owner.
7. **A covering clinician in a one-clinician practice**: should the medical director's ER-08 policy have to name a covering clinician, so that ER-09's escalation reaches a second person? Any change to ER-09's behaviour would be a gateway-contract change, which ruling 110 pauses. Pending owner decision 143.
8. **The gates whose requirement names no authority** (I-4, RD-4). The owners of those changes.
9. **"The owner" in AI-20 and gateway Open Question 10** (I-5). The owner.
10. **Rulings 102, 104 and 133**: PR #8 words them in AH-01, AH-02 and AH-06, with design calls D-30 to D-32, D-34 and D-35 PROPOSED there. RI-46, RI-47, RI-49 and RI-53 follow whatever the owner rules on them. The owner, in that change.
11. **The handoff shape** (RD-15). The owner.
12. **Who may read the checklist** (RD-16), and which readers count as authorized to see the counts of patient-side uses (RD-19). Planning.
13. **Security reviews and risk-analysis entries that no requirement makes anything wait on** (I-7): whether they should become requirements, so the checklist can show them. The owner.
14. **An accountant employed by the practice**: may such an accountant ever give the accountant's sign-off? Until the owner answers, no (fail closed; RD-11, T-9). Pending owner decision 139.
15. **Recording the practice's ownership** (RA-6): as dated party links under PR-15, so SR-04 can test that an accountant or counsel is not an owner. Pending owner decision 145.
16. **WI-03 and PR-15** (I-13, P-5): does WI-03's "never the proposer" bar a staff member from deciding a proposal they made as a patient, where PR-15's one-person alternative would allow it? Pending owner decision 144; PR #8 records it as its actors Open Question 12.
17. **The remaining design calls**: RD-3, RD-6 to RD-10, RD-12, RD-13 and RD-17 to RD-19. The owner.
18. **Which role "an authorized person" is** (I-3, SR-02, RD-18): until it is ruled, any role the signer holds is accepted and recorded. Pending owner decision 142.
19. **Counsel named under a variant name**: should the counsel named on a recorded review or confirmation be checked against a public record of admission, as RD-11 checks the accountant's licence? Not proposed here. The owner.
