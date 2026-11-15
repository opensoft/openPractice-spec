## ADDED Requirements

### Requirement: SR-01 One person may hold several roles
A named human MAY hold several roles at once (ruling 107). A role is a role recipe held through a roster entry (AM-02), an actor type (AM-01), or a capacity that a requirement names as a signer or an owner of work, such as the medical director, the practice manager, the controller or office manager, the sponsoring human of an agent (AM-04), the on-call clinician (ER-13), the practice's accountant, counsel, or the integration operator (EB-15) (design call RD-9). Holding several roles SHALL give the authority of each role and nothing more: no authority SHALL be inferred from a combination of roles (design call RD-17, PROPOSED). The practice's accountant and counsel are the exceptions that SR-04 states. An AI agent SHALL keep only the agent recipe (AM-04), and this requirement gives an agent no other role.

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

The signer SHALL state that role at signing, and the sign-off SHALL be refused when the stated role is not one the signer holds at that moment, or when the governing requirement names the roles allowed for that sign-off and the stated role is not among them (design call RD-18, PROPOSED). Where the governing requirement names no role, such as a record that "an authorized person" makes (AI-14, AH-02, VA-01, PG-15, PG-16), any role the signer holds SHALL be accepted and recorded. Which role an authorized person is, is pending with the owner as decision 142, not yet ruled. One act SHALL count for one role only: where an item needs sign-offs in two roles, a person who may give both SHALL give two sign-offs, each recording its role (design call RD-10). For an agent, the role is the agent recipe, recorded with the agent's openWallet ID (BAB-17).

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

#### Scenario: An agreement record whose requirement names no role
- **WHEN** the practice manager records and verifies a VA company's BAA (VA-01) and states the practice manager role
- **THEN** the record is accepted and records that role, because VA-01 names no role the record must be given in

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
- every sign-off a requirement gives to the practice's accountant: the D8 approval and every estimator-version approval (AI-12, AI-20, BAB-17, WI-03, MA-07);
- every counsel sign-off a requirement records: counsel's review recorded on each hold-policy version (PH-03) and on the proxy policy (PG-06), the recorded confirmation of ruling 97's answer that ruling 102 makes a go-live condition (`define-actors-agents-and-work-inbox` AH-06), and the recorded confirmation that a model provider's in-memory cache is acceptable, which ruling 133 asks for (`define-actors-agents-and-work-inbox` AH-02).

A sign-off that a later requirement gives to the practice's accountant, or records as counsel's, comes under this requirement. Apart from the engagement record, the accountant's attestation and the licence check that the test below needs for the accountant's sign-offs, which make those sign-offs wait on them (this change's `design.md`, T-9), this requirement adds no record and no precondition anywhere. Where a requirement only describes counsel's part, such as counsel confirming an agreement's form (AH-02) or deciding what agreement is required (PG-15), it records no counsel sign-off, and nothing here makes any function wait on counsel.

Such a sign-off is not self-approved only when all of these hold (design call RD-11, PROPOSED):
- it is given by a named person engaged from outside the practice. The practice's accountant is engaged under the vendor arrangement that `define-customer-financial-data-boundaries` requires for an outsourced accounting actor ("Accountant and biller access are independently bounded"). Counsel is named on the record by the authorized person who records the review or confirmation;
- that person is neither an owner nor an employee of the practice. The roster entry for the accountant's engagement SHALL be created together with the vendor arrangement, and SHALL never be an existing entry relabeled. It may hold the bookkeeper recipe and the actor type that entry needs, which is pending with the owner as decision 141. Holding that entry SHALL NOT make the accountant an employee and SHALL NOT count as another role;
- that person gives no other sign-off on the same item, in any role. For example, the medical director who approves a hold-policy version cannot also be the counsel whose review is recorded on it;
- that person is not an AI agent.

The sign-off SHALL be refused when any of these is not shown. A person who holds a roster entry other than the one created for the engagement SHALL be treated as an employee of the practice. Until the owner decides whether an accountant employed by the practice may ever give the accountant's sign-off, no such accountant SHALL give it (fail closed). For the accountant, "neither an owner nor an employee" SHALL be shown by all of these (design call RD-11, PROPOSED):
- the accountant's own attestation, given in their own name with the engagement. A statement that someone else records for the accountant SHALL NOT count;
- a check of the accountant's licence against the licensing board's public record, the independent record that design call RD-13 names. A sign-off whose licence cannot be checked there SHALL be refused (fail closed);
- a match of the licensee's name, as the board's public record gives it, against the parties behind the practice's roster entries, other than the accountant's own engagement entry, and against its recorded owners. Any match SHALL refuse the sign-off.

Who may give the accountant's sign-off is pending with the owner as decisions 139 (only someone engaged from outside the practice who is neither an owner nor an employee) and 140 (a licensed CPA whose licence is checked against the state board's public record), neither yet ruled. This requirement picks no side beyond refusing what it cannot show. Counsel needs no roster entry and no actor type, because an authorized person records counsel's review or confirmation by reference. The counsel named SHALL NOT be a person who holds a roster entry in the practice or is recorded as its owner; how a recorded name is matched to the parties behind roster entries (`define-mvp-practice-foundation` PR-15) is decided in planning.

No one-person alternative SHALL apply to these sign-offs. A practice whose own people cannot give them can have them given only by an accountant or counsel engaged from outside the practice, and the checklist shows that need (RC-06).

#### Scenario: A physician-owner tries to act as the practice's accountant
- **WHEN** the physician who owns the practice tries to approve the revenue-recognition policy as the practice's accountant
- **THEN** the approval is refused, AI-20's go-live gate stays closed, and the checklist shows that an accountant engaged from outside the practice must sign it

#### Scenario: An in-house bookkeeper tries to approve an estimator version
- **WHEN** an employee who holds only the bookkeeper recipe, with no outside engagement recorded, tries to approve a new estimator version as the practice's accountant
- **THEN** the approval is refused, because the person is not engaged from outside the practice

#### Scenario: An outside accountant who also keeps the books
- **WHEN** an accountant engaged from outside the practice approves a new estimator version. The engagement roster entry was created with a recorded vendor arrangement and holds only the bookkeeper recipe. The accountant attested in their own name, and their licence checks against the board's public record with no match to any other roster party or recorded owner
- **THEN** the approval is accepted and records the practice's accountant role

#### Scenario: An owner records themselves as the outside accountant
- **WHEN** an owner who is a licensed accountant records a second party under a variant of their name, creates an engagement entry for it, and attests that the person is neither an owner nor an employee
- **THEN** the licensee's name in the board's public record matches the party behind the owner's own roster entry, and the sign-off is refused

#### Scenario: A bookkeeper's entry is relabeled
- **WHEN** someone tries to mark an employed bookkeeper's existing roster entry as the accountant's engagement entry
- **THEN** the change is refused, because an engagement entry is created only together with its vendor arrangement

#### Scenario: The medical director is named as counsel
- **WHEN** a hold-policy version records counsel's review and names, as counsel, the medical director who will approve that version
- **THEN** the counsel review is refused, and the version cannot be approved under PH-03

#### Scenario: A practice manager who is also a lawyer
- **WHEN** a person who holds the practice manager recipe is named as counsel on a proxy-policy version
- **THEN** the counsel review is refused, because that person is an employee of the practice

#### Scenario: An agreement verified without counsel named
- **WHEN** an authorized person in a one-person practice records and verifies the agent operator's BAA (AH-02) and the HealthLinc operator agreement (PG-15), naming no counsel
- **THEN** both records are accepted exactly as those requirements allow, and this requirement adds no counsel sign-off and holds nothing back
