## ADDED Requirements

### Requirement: SR-01 One person may hold several roles
A named human MAY hold several roles at once (ruling 107). A role is a role recipe held through a roster entry (AM-02), an actor type (AM-01), or a capacity that a requirement names as a signer or an owner of work, such as the medical director, the practice manager, the controller or office manager, the sponsoring human of an agent (AM-04), the on-call clinician (ER-13), the practice's accountant, counsel, or the integration operator (EB-15) (design call RD-9). Holding several roles SHALL give the authority of each role and nothing more: no authority SHALL be inferred from a combination of roles. The practice's accountant and counsel are the exceptions that SR-04 states. An AI agent SHALL keep only the agent recipe (AM-04), and this requirement gives an agent no other role.

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
Every approval, release decision, attestation, confirmation, verification record and signature that any change requires SHALL record, in addition to what BAB-17 requires, the one role under which the signer gave it (ruling 107). The signer SHALL state that role at signing. A sign-off SHALL be refused when the stated role is not one the signer holds at that moment, or is not one the governing requirement allows for that sign-off. One act SHALL count for one role only: where an item needs sign-offs in two roles, a person who holds both SHALL give two sign-offs, each recording its role (design call RD-10). For an agent, the role is the agent recipe, recorded with the agent's openWallet ID (BAB-17).

#### Scenario: The controller signs the day close
- **WHEN** a physician who holds the medical director and the controller or office manager recipes signs the daily agent digest
- **THEN** the signature records the controller or office manager role (AO-10)

#### Scenario: A role the requirement does not allow
- **WHEN** the same physician approves a hold-policy version and states the controller or office manager role
- **THEN** the approval is refused, because PH-03 allows only the medical director

#### Scenario: Two roles, one person, two sign-offs
- **WHEN** a medical director who is also the named human approving a health-adjacent read-only answer type approves it (AH-05 exception 1)
- **THEN** two sign-offs are recorded, one as the approving named human and one as the medical director, and one act does not count for both

#### Scenario: A role the signer no longer holds
- **WHEN** a person whose medical director recipe ended yesterday tries to approve a routing-clock policy version as medical director
- **THEN** the approval is refused

### Requirement: SR-03 A rule that needs two different people names its one-person alternative
Every rule that needs two different people SHALL name the alternative that applies when the practice has only one eligible person, as ruling 98 does (ruling 107). Until the change that owns such a rule names its alternative, openPractice SHALL apply the rule as written and SHALL refuse the act when no second eligible person exists (fail closed); it SHALL NOT treat one person as two. A one-person alternative SHALL apply only when the roster, at the moment of the act, holds no second person eligible for that side, and each use SHALL be recorded as such, with every role the person used, and shown on the checklist (`readiness-checklist` RC-06) (design call RD-12).

A rule that does not need two people, but loses its meaning when one person signs both sides, such as an agent's sponsoring human signing that agent's daily digest (AO-10), SHALL keep working as its owning requirement states. Each such sign-off SHALL record every role the signer holds that the rule involves, and the checklist SHALL show it (RC-06). This change's `design.md` lists every such rule found when this change was drafted, with a PROPOSED one-person alternative for each and the amendment it would need in its owning change; an alternative takes effect only when that change adopts it.

#### Scenario: A two-person rule with no alternative yet
- **WHEN** a rule needs two different people, its owning change names no one-person alternative, and the roster holds only one eligible person
- **THEN** the act is refused as the rule requires, and the checklist shows that a second person is needed

#### Scenario: An alternative exists but a second person is on the roster
- **WHEN** a rule's owning change has adopted a one-person alternative, and a second eligible person is on the roster
- **THEN** the alternative does not apply, and the rule needs the second person

#### Scenario: A sponsor signs its own agent's digest
- **WHEN** the only controller of a practice sponsors an agent and signs the daily digest that lists that agent's actions
- **THEN** the digest is signed as AO-10 states today, the signature records both the controller or office manager role and the sponsor role, and the checklist lists it as a sign-off in which one person signed both sides

### Requirement: SR-04 An accountant's or counsel's sign-off can never be self-approved
A sign-off that a requirement gives to the practice's accountant, including the D8 approval and every estimator-version approval (AI-12, AI-20, BAB-17, WI-03, MA-07), or to counsel, including counsel's review, confirmation or decision on a policy or an agreement (PH-03, PG-06, AH-02, AH-06, PG-15, PG-16, and any counsel sign-off a later requirement adds), SHALL NOT be self-approved (ruling 107). Such a sign-off SHALL name the person who gave it. It SHALL be refused when that person (design call RD-11):
- is an owner of the practice;
- holds any other role in the practice, except that the practice's accountant MAY also hold the bookkeeper recipe;
- gives, in another role, another sign-off on the same item, such as the medical director who approves the hold-policy version whose counsel review is being recorded;
- is an AI agent.

No one-person alternative SHALL apply to these sign-offs. A practice whose own people cannot give them SHALL obtain them from an accountant or counsel outside the practice, and the checklist shows that need (RC-06). Where counsel's review is recorded by reference rather than signed in openPractice, the person recording it SHALL name the counsel, and the same test SHALL apply to the counsel named.

#### Scenario: A physician-owner tries to act as the practice's accountant
- **WHEN** the physician who owns the practice tries to approve the revenue-recognition policy as the practice's accountant
- **THEN** the approval is refused, AI-20's go-live gate stays closed, and the checklist shows that an accountant outside the practice must sign it

#### Scenario: The medical director is named as counsel
- **WHEN** a hold-policy version records counsel's review and names, as counsel, the medical director who will approve that version
- **THEN** the counsel review is refused, and the version cannot be approved under PH-03

#### Scenario: An outside accountant who also keeps the books
- **WHEN** an outside accountant who holds only the bookkeeper recipe approves a new estimator version as the practice's accountant
- **THEN** the approval is accepted and records the practice's accountant role

#### Scenario: A practice manager who is also a lawyer
- **WHEN** a person who holds the practice manager recipe is named as counsel on a proxy-policy version
- **THEN** the counsel review is refused, because that person holds another role in the practice
