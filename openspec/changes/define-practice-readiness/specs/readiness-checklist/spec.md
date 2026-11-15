## ADDED Requirements

### Requirement: RC-01 One readiness checklist shows every readiness item the specifications require
openPractice SHALL keep one readiness checklist for each practice. It SHALL list every readiness item (design call RD-1). A readiness item is recorded once per practice, counterparty, destination, agent, roster entry or version, not once per transaction. It is a policy version, approval, verification, attestation, agreement record, roster or coverage fact, or configuration fact that either:
- is made a precondition of a function by an existing requirement; or
- is a version that an existing requirement gives a named authority to approve while a fail-closed default runs until it is approved, such as the routing-clock policy of ER-08.

Each row SHALL show:
- what the item is;
- the requirement or requirements that enforce it at runtime, by requirement ID and change;
- the authority that signs it, in the words of the enforcing requirement, and the named people who hold that authority now;
- its state (RC-04);
- what stays off until it is done, and what still runs meanwhile, as the enforcing requirement states them (RC-05);
- the starter template that serves it, if the kit has one (`policy-starter-kit` SK-01).

Rows SHALL be grouped by the function they gate, so a practice sees together everything one function waits on. A sign-off made once per transaction, such as an export-batch release (AI-06) or a link confirmation (EB-04, PG-05), is a work item in the work inbox (`define-actors-agents-and-work-inbox` WI-07) and SHALL NOT be a checklist row. Release prerequisites that the product owner decides, not the practice, such as the grant sets of the new role recipes (AM-02), SHALL appear in a separate section, read-only (design call RD-2).

#### Scenario: A practice prepares its accounting go-live
- **WHEN** the practice manager opens the checklist before any accounting destination has gone live
- **THEN** the accounting group lists the revenue-recognition policy and the first estimator version (AI-20, AI-12), the destination verification record (AI-20), the reason taxonomy and the account mapping (BAB-06), each with its enforcing requirement, its authority, the person who holds that authority, its state and what stays off

#### Scenario: A batch waits for release
- **WHEN** an export batch is assembled and waits for its release decision
- **THEN** it appears as a work item in the work inbox, governed by AI-06 and BAB-17, and not as a checklist row

#### Scenario: A recipe's grant set is not yet approved for release
- **WHEN** the medical director recipe's grant set has not been approved for the release the practice runs (AM-02)
- **THEN** the checklist shows it in the release-prerequisite section, read-only, and shows that every sign-off the medical director gives waits on it

### Requirement: RC-02 Checklist rows follow the requirements, never the other way round
Each row SHALL be derived from the requirement that enforces it. When a change adds, alters or removes a gate, the row SHALL be added, altered or removed in the release that carries that change, and this requirement needs no change for it. The checklist SHALL NOT keep a row that no requirement states, and SHALL NOT add a condition, name an authority, state a consequence or set a deadline that the enforcing requirement does not state. A consequence or authority found only in a design, a task list or an open question SHALL NOT appear on a row. Where the enforcing requirement names no authority, the row SHALL say "authority not named by the requirement" and SHALL NOT supply one (design call RD-4). A ruling that no requirement yet encodes SHALL produce no row; its row appears with the requirement that encodes it. The rows at the time this change is ratified are the inventory in this change's `design.md`, each traced to its source.

#### Scenario: A later change alters a gate
- **WHEN** a ratified change adds an item that every hold-policy version must state (PH-03)
- **THEN** the hold-policy row lists the new item in the release that carries that change, and the checklist adds nothing PH-03 does not say

#### Scenario: A requirement names no authority
- **WHEN** a gate's requirement makes a function wait on an approval but names nobody who gives it, such as the session and location rules "the practice approves" (VA-04)
- **THEN** the row says that the requirement names no authority, and the checklist names none

#### Scenario: A ruling not yet in any requirement
- **WHEN** the owner has ruled that something is a go-live condition, and no requirement in the deployed release encodes that ruling yet
- **THEN** the checklist shows no row for it until a requirement encodes it

#### Scenario: A gate is removed
- **WHEN** a ratified change removes a gate
- **THEN** its row is removed in the release that carries that change, and its history stays in the gate's own records

### Requirement: RC-03 The checklist reads and displays, and never satisfies, replaces or relaxes a gate
The checklist SHALL read each row's state from the gate's own record, such as the AI-14 agreement record or the approved PH-03 policy version, and SHALL keep no state of its own that any gate consults. Every gate SHALL keep its own fail-closed enforcement where it is defined today. Viewing, marking or completing a row SHALL enable nothing: each function is enabled only by its own gate, and a function whose gate is met is not held back by the checklist. The checklist SHALL offer no action that records, approves or verifies a gate's item except by opening that gate's own action, under that action's own tier, signer rules, evidence and audit (`define-actors-agents-and-work-inbox` AO-02). A row shown as `not_required` (RC-04) SHALL NOT relax its gate: when the function is used, the gate still refuses until it is met.

#### Scenario: An agreement lapses while every row looked complete
- **WHEN** a customer-detail destination's BAA expires
- **THEN** AI-14 halts customer-detail delivery at once, whether or not anyone has opened the checklist, and the row turns to `lapsed` because it reads the AI-14 record

#### Scenario: Someone tries to mark a row done
- **WHEN** a user tries to mark the hold-policy row as complete without an approved PH-03 version
- **THEN** the checklist offers no such action, the row stays as it was, and every hold placement is still refused by PH-03

#### Scenario: A gate is met before anyone opens the checklist
- **WHEN** the medical director approves a routing-clock policy version through ER-08's own action
- **THEN** handoffs use the approved windows at once, and the routing-clock row shows `in_force` because it reads that version

#### Scenario: A not-required row and a later use of its function
- **WHEN** the legacy-collections row is `not_required` because the practice has no legacy run-off, and a deposit later combines legacy collections
- **THEN** BAB-08 still governs the deposit, and the row turns to required because its requirement's condition now applies

### Requirement: RC-04 Each row has a state read from its gate, with its dates
Each row SHALL have exactly one of these states, read from the gate's own record (design call RD-3):
- `unavailable`: the gate's record cannot be read at this moment. The row SHALL show no earlier state in its place, and the gate itself still decides;
- `not_recorded`: nothing is recorded;
- `in_progress`: a draft policy version, or a record or review that has begun but is not complete, such as counsel's review recorded on a hold-policy version that the medical director has not approved;
- `in_force`: the gate's record is complete and current;
- `lapsed`: the record has expired, or was revoked, withdrawn or suspended, or the gate has suspended what the record enables, such as a lapse under `define-actors-agents-and-work-inbox` AH-01, with the cause shown;
- `not_required`: the enforcing requirement's own condition does not apply to the practice's configuration, such as legacy run-off (BAB-08) for a practice that has none.

A row SHALL be `not_required` only when the enforcing requirement's own condition says so and the practice's configuration shows it; no person SHALL be able to mark a row as not required. Each row SHALL show the effective, expiry or renewal and review dates the gate's record holds, and the date of each change of state. Where a requirement keeps a fail-closed default running meanwhile, such as the default windows of ER-08, the row SHALL say that the default is in use.

#### Scenario: A VA company's BAA expires
- **WHEN** the BAA recorded under VA-01 for a VA company reaches its expiry date
- **THEN** the row shows `lapsed` with the expiry date, and VA-01 has already removed PHI access from that company's VAs

#### Scenario: An agent operator's deletion confirmation is overdue
- **WHEN** an agent operator's deletion confirmation under ER-12 is more than 72 hours late, and AH-01 suspends PHI access on that operator's path
- **THEN** the operator, provider and zero-data-retention rows for that path show `lapsed` with that cause, while each agreement record stays verified

#### Scenario: A routing-clock policy is not yet approved
- **WHEN** no routing-clock policy version is approved
- **THEN** the row shows `not_recorded` and says that the fail-closed default windows of ER-08 are in use

#### Scenario: A gate's record cannot be read
- **WHEN** the checklist cannot read the PH-03 policy record, after showing the hold-policy row as `in_force`
- **THEN** the row shows `unavailable`, not `in_force`, and PH-03 still decides every hold placement from its own record

#### Scenario: Staff try to mark a row not required
- **WHEN** a user tries to mark the proxy-policy row as not required
- **THEN** the change is refused, because only the enforcing requirement's own condition, read from configuration, can make a row not required

### Requirement: RC-05 Each row says what stays off and what still runs
Each row SHALL state, in the words of its enforcing requirement, what the gate keeps off until the item is done and what continues meanwhile. Where the enforcing requirement states that something always runs, such as urgent escalation, billing contact and own-records access (PG-03, PH-02, ER-09), the row SHALL say so.

#### Scenario: No hold policy is approved
- **WHEN** a practice manager reads the hold-policy row before any version is approved
- **THEN** the row says that no payment hold can be placed (PH-03), and that a hold never withholds urgent escalation, billing contact or the patient's own records (PH-02)

#### Scenario: No messaging-continuity policy is approved
- **WHEN** no messaging-continuity policy version is approved
- **THEN** the row says that a deactivation or a lapsed membership withholds neither routine clinical messaging nor medical-AI consults until one is approved (PG-04)

### Requirement: RC-06 The checklist shows who holds each authority, and where one person holds several
The checklist SHALL show, for each authority its rows name, the named people who hold it, and SHALL show together every authority one person holds (`signer-roles` SR-01). It SHALL mark:
- each row whose sign-offs were given by one person in more than one role, with the role each sign-off recorded (SR-02);
- each row whose authority is the practice's accountant or a counsel sign-off that SR-04 covers, as needing a person engaged from outside the practice (SR-04), and, while the accountant's attestation is reopened under SR-04, each accountant row as waiting on its renewal;
- each rule that needs two different people, or that loses its meaning when one person signs both sides, where one person now holds both sides, and whether a one-person alternative is in force for it (SR-03);
- each chain that collapses onto one person, as a warning that changes nothing at runtime: wherever ER-09's escalation of a tracked alert can reach nobody but the alert's own recipient, as ER-09 records it (its design call DC-64 in the corrections change): the medical director can be the recipient, as an on-call clinician or as ER-13's recipient for an hour with no on-call clinician rostered, and the routing-clock policy names no covering clinician, or the covering clinician it names is that recipient too, or everyone the escalation would add cannot be reached at all, being offboarded or having no registered mobile approver device, or no medical director assignment is in effect and the only active entry of the clinician or medical director type is the alert's own recipient (ER-08, ER-09, ER-13, and ER-09's design call DC-67 in the corrections change; ruling 143 orders this warning); or an owner of work whose named cover under WI-05 does not exist.

The checklist SHALL also list each use of a one-person alternative and each sign-off in which one person signed both sides of a rule (SR-03), as a list apart from the rows. A use or sign-off whose subject is a patient-side record SHALL appear only as a count for its rule (design call RD-19, PROPOSED). A patient-side record is a patient's record, or a guarantor, subscriber, representative or other role or link that ties a party to a patient. An example is an act on the actor's own balance or coverage under `define-mvp-practice-foundation` PR-15, where the actor's own party includes every party in its same-person group (ruling 150). The count carries an opaque reference that opens the audit entries (PR-10) only to a reader authorized to see that PHI. Because a count can point to the one person who could have acted, the count and its reference SHALL be shown only to a reader authorized to see that PHI, and any other reader sees neither. The checklist SHALL NOT show, for such a use, the person's name, any role the person holds in a patient record, or any amount.

#### Scenario: A physician-owner holds three authorities
- **WHEN** one named physician holds the medical director recipe, the practice manager recipe and the controller or office manager recipe, and is the only clinician on the on-call roster
- **THEN** the checklist shows the three authorities together on that person, and, because the routing-clock policy names no covering clinician, warns that ER-09's escalation to the medical director reaches the same person (ruling 143)

#### Scenario: Nobody is on call for some hours
- **WHEN** the on-call roster names no clinician for the night hours, so ER-13 sends those hours' tracked alerts to the medical director, and the routing-clock policy names no covering clinician
- **THEN** the checklist warns that, in those hours, ER-09's escalation reaches nobody but the alert's own recipient (ruling 143)

#### Scenario: A pending medical director who is the only clinician
- **WHEN** the only clinician on the roster is on call and their medical director assignment is still pending its check
- **THEN** the checklist warns that ER-09's escalation reaches nobody but the alert's own recipient, because the escalation goes to every active entry of the clinician or medical director type and there is no other (ruling 143)

#### Scenario: A reopened attestation
- **WHEN** an owner link is ended after the outside accountant attested
- **THEN** each accountant row shows that the accountant's attestation is reopened and that new accountant sign-offs wait until the accountant confirms the owners again (SR-04)

#### Scenario: The covering clinician is the medical director
- **WHEN** the only clinician on the on-call roster is the medical director, and the routing-clock policy names the medical director as the covering clinician
- **THEN** the checklist warns that ER-09's escalation reaches nobody but the alert's own recipient, as it would with no covering clinician named (ruling 143)

#### Scenario: An accountant row
- **WHEN** a practice manager reads the revenue-recognition row
- **THEN** it says that the practice's accountant signs it, that the accountant must be engaged from outside the practice (SR-04), and who holds that authority now

#### Scenario: A sponsor signs its own agent's digest
- **WHEN** the controller who sponsors an agent signs a daily digest that includes that agent's actions (AO-10)
- **THEN** the sign-off appears in the list of sign-offs in which one person signed both sides, with both roles recorded

#### Scenario: The sole manager writes off her own balance
- **WHEN** the only manager of a practice, who is also a guarantor there, writes off her own balance under PR-15's one-person alternative
- **THEN** the checklist adds one to the count for PR-15's alternative, with an opaque reference to the audit entries, and shows both only to readers authorized to see that PHI; other readers see neither, and no reader sees a name, a role in a patient record or an amount

### Requirement: RC-07 The checklist holds no PHI and is read only through the versioned API
The checklist SHALL hold no PHI. It shows items, requirement IDs, authorities, the names of workforce members and counterparties, states and dates, and, for a use whose subject is a patient-side record, only a count and an opaque reference, shown only to a reader authorized to see that PHI (RC-06). It SHALL be read only through openPractice's versioned API layer, under that layer's authorization (PR-09; `define-actors-agents-and-work-inbox` AO-02); which recipes may read it is decided in planning (design call RD-16). The checklist SHALL NOT copy an agreement document or a policy text into a log, a diagnostic bundle or a notification.

#### Scenario: A diagnostic bundle is made for the checklist
- **WHEN** a checklist error produces a diagnostic bundle
- **THEN** the bundle holds row identifiers, requirement IDs and coded reasons, and no patient data, agreement text or policy text

#### Scenario: A client reads the checklist outside the API
- **WHEN** a client tries to read checklist rows outside the versioned API layer
- **THEN** the request is refused and nothing is returned
