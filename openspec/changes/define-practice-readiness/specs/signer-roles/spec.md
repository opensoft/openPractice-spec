## ADDED Requirements

### Requirement: SR-01 One person may hold several roles
A named human MAY hold several roles at once (ruling 107). A role is a role recipe held through a roster entry (AM-02), an actor type (AM-01), or a capacity that a requirement names as a signer or an owner of work, such as the medical director, the practice manager, the controller or office manager, the sponsoring human of an agent (AM-04), the on-call clinician (ER-13), the practice's accountant, which is the outside-accountant roster entry (ruling 141), counsel, or the integration operator (EB-15) (design call RD-9). Holding several roles SHALL give the authority of each role and nothing more: no authority SHALL be inferred from a combination of roles (design call RD-17, PROPOSED). The practice's accountant and counsel are the exceptions that SR-04 states. An AI agent SHALL keep only the agent recipe (AM-04), and this requirement gives an agent no other role.

#### Scenario: A physician-owner holds several recipes
- **WHEN** one named physician holds the medical director recipe, the practice manager recipe and the controller or office manager recipe
- **THEN** that person may give each sign-off any of those roles allows, and none that no held role allows

#### Scenario: A combination of roles does not create a new authority
- **WHEN** a person who holds the front desk and biller recipes tries to approve a hold-policy version
- **THEN** the approval is refused, because neither role is the medical director that PH-03 names

#### Scenario: An agent is offered a second role
- **WHEN** someone tries to record an agent as the sponsoring human of another agent, or as the medical director
- **THEN** the change is refused, and the agent keeps only the agent recipe (AM-04)

### Requirement: SR-02 Every sign-off records the role under which it was given
Every sign-off that a requirement gives to a named human or a named AI agent in openPractice SHALL record, in addition to what BAB-17 requires, the one role under which it was given (ruling 107). A sign-off here is an approval, release decision, attestation, confirmation or signature recorded under BAB-17, or a record that a requirement has an authorized person make in openPractice, such as the verification of an agreement (AI-14, AH-02, VA-01, PG-15). It does not include a record that another system makes and openPractice only stores, such as HealthLinc's identity verification for a link (PG-05).

The signer SHALL state that role at signing, and the sign-off SHALL be refused when the stated role is not one the signer holds at that moment, or when the governing requirement names the roles allowed for that sign-off and the stated role is not among them (design call RD-18, PROPOSED). Where a requirement says that "an authorized person" records or verifies something and names no other role, that person is the practice manager, and the record keeps the role used (ruling 142). One act SHALL count for one role only: where an item needs sign-offs in two roles, a person who may give both SHALL give two sign-offs, each recording its role (design call RD-10). For an agent, the role is the agent recipe, recorded with the agent's openWallet ID (BAB-17).

#### Scenario: The controller signs the day close
- **WHEN** a physician who holds the medical director and the controller or office manager recipes signs the daily agent digest
- **THEN** the signature records the controller or office manager role (AO-10)

#### Scenario: A role the requirement does not allow
- **WHEN** the same physician approves a hold-policy version and states the controller or office manager role
- **THEN** the approval is refused, because PH-03 allows only the medical director

#### Scenario: One act offered for two roles
- **WHEN** an item needs a sign-off in each of two roles, and a person who holds both gives one sign-off and asks that it count for both
- **THEN** only the role stated is recorded, and the sign-off in the other role is still missing

#### Scenario: A role the signer no longer holds
- **WHEN** a person whose medical director recipe ended yesterday tries to approve a routing-clock policy version as medical director
- **THEN** the approval is refused

#### Scenario: An authorized person who is not the practice manager
- **WHEN** a requirement says that an authorized person verifies an agreement and names no other role, and a person who holds only the front desk recipe tries to verify it
- **THEN** the verification is refused, because ruling 142 makes that person the practice manager

#### Scenario: The practice manager verifies an agreement
- **WHEN** the practice manager records and verifies a VA company's BAA (VA-01)
- **THEN** the record is accepted and keeps the practice manager role (ruling 142)

#### Scenario: A record another system makes
- **WHEN** HealthLinc reports the identity verification for a link request (PG-05)
- **THEN** openPractice stores it as PG-05 requires and asks for no role, while the named human who confirms the link at tier 2 records the role used

### Requirement: SR-03 A rule that needs two different people names its one-person alternative
Every rule that needs two different people SHALL name the alternative that applies when the practice has only one eligible person, as ruling 98 does (ruling 107). Until the change that owns such a rule names its alternative, and whenever the alternative it names is unavailable, openPractice SHALL apply the rule as written and SHALL refuse the act when no second eligible person exists (fail closed); it SHALL NOT treat one person as two. A one-person alternative SHALL apply only when the roster, at the moment of the act, holds no second person eligible for that side (design call RD-12). Each use SHALL be recorded as a use of a one-person alternative, with every role the person used; the readiness checklist lists or counts it under `readiness-checklist` RC-06.

A rule that does not need two people, but loses its meaning when one person signs both sides, such as an agent's sponsoring human signing that agent's daily digest (AO-10), SHALL keep working as its owning requirement states. Each such sign-off SHALL record every role the signer holds that the rule involves; the readiness checklist lists or counts it under RC-06. This change's `design.md` lists every such rule found when this change was drafted, with a PROPOSED one-person alternative for each and the amendment it would need in its owning change; an alternative takes effect only when that change adopts it.

#### Scenario: A two-person rule with no alternative yet
- **WHEN** a rule needs two different people, its owning change names no one-person alternative, and the roster holds only one eligible person
- **THEN** the act is refused as the rule requires, and the checklist shows that a second person is needed

#### Scenario: The named alternative is unavailable
- **WHEN** a rule's owning change names a one-person alternative that needs a fact the practice does not have, such as a notice recipient who is not on the roster
- **THEN** the act is refused as the rule requires, and nothing treats the one person as two

#### Scenario: An alternative exists but a second person is on the roster
- **WHEN** a rule's owning change has adopted a one-person alternative, and a second eligible person is on the roster
- **THEN** the alternative does not apply, and the rule needs the second person

#### Scenario: A sponsor signs its own agent's digest
- **WHEN** the only controller of a practice sponsors an agent and signs the daily digest that lists that agent's actions
- **THEN** the digest is signed as AO-10 states today, the signature records both the controller or office manager role and the sponsor role, and the checklist lists it as a sign-off in which one person signed both sides

### Requirement: SR-04 An accountant's or counsel's sign-off can never be self-approved
These sign-offs SHALL NOT be self-approved (ruling 107):
- every sign-off a requirement gives to the practice's accountant: the D8 approval and every estimator-version approval (AI-12, AI-20, BAB-17, WI-03, MA-07), and the confirmation beside the D8 record of who books the bank debits openPractice explains, which is part of accounting policy, within what ruling 141 lets the outside accountant approve (ruling 197; AI-20 in the corrections change, the record's details being its design call F-18, PROPOSED);
- every counsel sign-off a requirement records: counsel's review recorded on each hold-policy version (PH-03) and on the proxy policy (PG-06), the recorded confirmation of ruling 97's answer that ruling 102 makes a go-live condition (`define-actors-agents-and-work-inbox` AH-06), and the recorded confirmation that a model provider's in-memory cache is acceptable, which ruling 138 makes a go-live condition (`define-actors-agents-and-work-inbox` AH-02).

A sign-off that a later requirement gives to the practice's accountant, or records as counsel's, comes under this requirement. One is the confirmation of the amount at which a write-off becomes material, which ruling 141 calls the write-off threshold and ruling 129 has each practice set and its accountant confirm (`define-revenue-cycle-core` PS-11 and RE-09, a draft change). This requirement adds no record and no precondition anywhere, apart from what rulings 139, 140, 149, 195 and 196 order, and the attestation's confirmation of the owners below (this change's `design.md`, T-9). Where a requirement only describes counsel's part, such as counsel confirming an agreement's form (AH-02) or deciding what agreement is required (PG-15), it records no counsel sign-off, and nothing here makes any function wait on counsel. The reviews of each starter template before a product release (`policy-starter-kit` SK-06) are made for the product, not inside any practice, and are not a practice's accountant or counsel sign-offs, so this requirement's checks against a practice's staff and owners do not apply to them.

**The accountant (rulings 139, 140 and 141).** The accountant's sign-off SHALL be given only by someone engaged from outside the practice who is neither an owner nor an employee, and an accountant employed by the practice SHALL NOT give it in version 1 (ruling 139). That accountant SHALL be a licensed CPA whose licence is checked against the state board's public record, and SHALL attest in their own name (ruling 140). The accountant's roster entry is of the outside-accountant type and SHALL hold the outside-accountant recipe and no other (ruling 141; `define-actors-agents-and-work-inbox` AM-01, AM-02). The accountant SHALL be matched against the practice's staff and owners, and a match SHALL refuse the sign-off (ruling 139, read with ruling 149's question, "as the accountant is"). These details are design call RD-11 (PROPOSED), except what a bullet cites ruling 195 for:
- the engagement is the vendor arrangement that `define-customer-financial-data-boundaries` requires for an outsourced accounting actor ("Accountant and biller access are independently bounded"). The accountant's roster entry SHALL be created together with that arrangement, and SHALL never be an existing entry relabeled. Holding it SHALL NOT make the accountant an employee or count as another role;
- someone who used to be an owner or on staff counts as outside the practice only after 24 months, and an owner recorded by mistake and withdrawn does not count against them (ruling 195), for both matches. This requirement reads that as follows (RD-11, PROPOSED): a party counts as an owner or an employee if they hold an owner link under PR-15 or a staff roster entry now, or held one that ended within the last 24 months, counted from the later of its end date and the time the ending was recorded, which is read from the audit entry of the ending (`define-mvp-practice-foundation` PR-10), so that a backdated end date cannot clear the look-back; an ended roster entry's party and end date stay readable for this (`define-actors-agents-and-work-inbox` AM-05, its design call D-42, PROPOSED). An owner recorded by mistake is withdrawn by ending the owner link with the reason `recorded_in_error`, and a link so ended does not count; that ending is decided by another holder of the practice manager or the controller or office manager recipe where one is on the roster, and otherwise under PR-15's one-person alternative (that change's design call F-12, PROPOSED), and it SHALL be audited, notified and SHALL reopen the attestation. A roster entry of the outside-accountant type is not a staff roster entry;
- the attestation is the accountant's own, given in their own name with the engagement, states that they are neither an owner nor an employee, and confirms the practice's owners as recorded under `define-mvp-practice-foundation` PR-15 when it is given. Ending or changing an owner link after the attestation SHALL reopen it, and the accountant's sign-offs SHALL be refused until the accountant confirms the owners again; recording a new owner link, which only restricts, SHALL NOT reopen it. A statement that someone else records for the accountant SHALL NOT count;
- a licence that cannot be checked against the state board's public record, or that the record does not show as active, SHALL refuse the sign-off;
- the match uses the licensee's name as the board's public record gives it, against every party that counts as an owner or an employee (ruling 145), so that ending a link or an entry clears no one for 24 months (ruling 195).

**Counsel (rulings 141 and 149).** Counsel needs no sign-in and is not a roster entry (ruling 141). Where a requirement records counsel's review or confirmation, the person who records it SHALL name counsel, and the named counsel SHALL be checked against the state bar's public record and matched against the practice's staff and owners, as the accountant is, a match refusing the review or confirmation (ruling 149). The refusal on a match is read as part of ruling 149, because it answers a question that says "as the accountant is" and gives self-approval as its reason. These details are design call RD-11 (PROPOSED):
- the record keeps counsel's state bar, admission number, and the date and result of the check;
- an admission that cannot be checked against the bar's public record, or that the record does not show as active, SHALL refuse the review or confirmation;
- the match uses the admitted name as the bar's public record gives it, against every party that counts as an owner or an employee, as for the accountant.

How a name is matched to parties is decided in planning.

**For both.** The sign-off SHALL also be refused when the person gives another sign-off on the same item, in any role, such as the medical director who approves a hold-policy version and the counsel whose review is recorded on it, or when the person is an AI agent. At least one current owner must be on record first: until then, no accountant or counsel sign-off SHALL be accepted (ruling 196). Where the owner cannot be recorded yet because that record is itself waiting for a second person, counsel's own written statement of independence SHALL be recorded instead, and the counsel review or confirmation then proceeds; the accountant's sign-offs still wait for the owner record (ruling 196). These details are design call RD-11 (PROPOSED): a current owner on record is an owner link in force under PR-15; an owner link held for an outside accountant under PR-15's one-person alternative is a record waiting for a second person; and the statement says that counsel is neither an owner nor an employee of the practice, and is recorded by reference with the review or confirmation. No one-person alternative SHALL apply to these sign-offs. A practice whose own people cannot give them can have them given only by an accountant or counsel engaged from outside the practice, and the checklist shows that need (RC-06).

#### Scenario: A physician-owner tries to act as the practice's accountant
- **WHEN** the physician who owns the practice tries to approve the revenue-recognition policy as the practice's accountant
- **THEN** the approval is refused, AI-20's go-live gate stays closed, and the checklist shows that an outside accountant must sign it

#### Scenario: An employed bookkeeper tries to approve an estimator version
- **WHEN** an employee who holds the bookkeeper recipe tries to approve a new estimator version as the practice's accountant
- **THEN** the approval is refused, because an employed accountant cannot give the accountant's sign-off in version 1 (ruling 139)

#### Scenario: An outside accountant approves an estimator version
- **WHEN** an outside accountant approves a new estimator version. The accountant is a licensed CPA whose roster entry was created with a recorded vendor arrangement and holds only the outside-accountant recipe. They attested in their own name, confirming the owners recorded, their licence checks active against the state board's public record, the name matches no other roster party or recorded owner, and the practice's owners are recorded
- **THEN** the approval is accepted and records the practice's accountant role

#### Scenario: An accountant who is not a CPA
- **WHEN** a bookkeeping firm engaged from outside the practice offers a bookkeeper who holds no CPA licence to approve the revenue-recognition policy
- **THEN** the approval is refused, because the accountant must be a licensed CPA (ruling 140)

#### Scenario: An owner records themselves as the outside accountant
- **WHEN** an owner who is a licensed CPA records a second party under a variant of their name, creates an outside-accountant entry for it, and attests that the person is neither an owner nor an employee
- **THEN** the licensee's name in the board's public record matches the owner's recorded owner link, and the sign-off is refused

#### Scenario: A bookkeeper's entry is relabeled
- **WHEN** someone tries to mark an employed bookkeeper's existing roster entry as the accountant's engagement entry
- **THEN** the change is refused, because the accountant's entry is created only together with its vendor arrangement

#### Scenario: No owner is recorded
- **WHEN** an outside accountant who passes every other check approves the revenue-recognition policy, and the practice has no owner link recorded
- **THEN** the approval is refused until at least one current owner is on record (ruling 196)

#### Scenario: The owners change after the attestation
- **WHEN** the practice manager ends one owner link and records another after the outside accountant attested, and the accountant then approves a new estimator version
- **THEN** the approval is refused until the accountant confirms the owners as now recorded (design call RD-11, PROPOSED)

#### Scenario: A new owner link alone
- **WHEN** the practice manager records an owner link for a new partner after the outside accountant attested, ending none
- **THEN** the attestation stays in force, because a new link only restricts; the accountant is notified of the change (design call RD-11, PROPOSED)

#### Scenario: An owner ends their link to clear an accountant
- **WHEN** an owner who is a licensed CPA has their owner link ended, and is then engaged as the practice's outside accountant
- **THEN** the sign-off is refused, because someone who used to be an owner counts as outside only after 24 months (ruling 195); the match reads the ended owner link (design call RD-11, PROPOSED)

#### Scenario: A retired partner serves as the outside accountant
- **WHEN** a former owner whose owner link ended 30 months ago, a licensed CPA, is engaged from outside the practice and passes every other check
- **THEN** the sign-off is accepted, because more than 24 months have passed, as it would be for a former employee whose roster entry ended 30 months ago (ruling 195)

#### Scenario: A former employee within 24 months
- **WHEN** a bookkeeper whose staff roster entry ended 10 months ago is engaged as the outside accountant
- **THEN** the sign-off is refused, because someone who used to be on staff counts as outside only after 24 months (ruling 195); the match reads the ended staff roster entry (design call RD-11, PROPOSED)

#### Scenario: Counsel while the owner's record waits for a second person
- **WHEN** a practice has no owner link in force, and recording its owner's link is itself waiting for a second person, such as an outside accountant the practice has not engaged
- **THEN** counsel's own written statement of independence is recorded instead, and a counsel review on a hold-policy version proceeds without the link, while the accountant's sign-offs wait for it (ruling 196); that an outside accountant is such a second person, and that the statement says counsel is neither an owner nor an employee and is recorded by reference, are design call RD-11 (PROPOSED)

#### Scenario: An attestation that does not confirm the owners
- **WHEN** an outside accountant attests in their own name that they are neither an owner nor an employee, but does not confirm the practice's owners as recorded
- **THEN** the attestation does not count, and the accountant's sign-offs are refused until it does (design call RD-11, PROPOSED)

#### Scenario: A sole owner records themselves, then counsel reviews a hold policy
- **WHEN** the only human on a practice's roster, its owner and practice manager, records the owner link that names their own party, and then records counsel's review on a hold-policy version, naming outside counsel whose admission checks active and whose name matches no roster party or recorded owner
- **THEN** the owner link is recorded at once, with no outside accountant, because the first owner link the practice ever records is never held for a second person or an outside accountant (`define-mvp-practice-foundation` PR-15), and the counsel review is accepted

#### Scenario: The medical director is named as counsel
- **WHEN** a hold-policy version records counsel's review and names, as counsel, the medical director who will approve that version
- **THEN** the counsel review is refused, and the version cannot be approved under PH-03

#### Scenario: A practice manager who is also a lawyer
- **WHEN** a person who holds the practice manager recipe is named as counsel on a proxy-policy version
- **THEN** the counsel review is refused, because the bar's record of that person matches the party behind a roster entry

#### Scenario: An owner who is a lawyer names themselves as counsel
- **WHEN** the practice manager records counsel's confirmation of ruling 97's answer and names, under a variant of their name, an owner who is a lawyer
- **THEN** the admitted name in the state bar's public record matches a recorded owner, and the confirmation is refused (ruling 149)

#### Scenario: An agreement verified without counsel named
- **WHEN** the practice manager of a one-person practice records and verifies the agent operator's BAA (AH-02) and the HealthLinc operator agreement (PG-15), naming no counsel
- **THEN** both records are accepted exactly as those requirements allow, and this requirement adds no counsel sign-off and holds nothing back
