## ADDED Requirements

### Requirement: AM-01 Every actor has a declared type
openPractice SHALL recognize exactly these actor types:
- on-site staff;
- remote virtual assistant (VA);
- nurse;
- clinician;
- medical director;
- controller or office manager;
- AI agent;
- patient or representative, acting only through the HealthLinc gateway (change `define-patient-gateway-and-escalation`);
- system integration.

Staff, VAs, nurses, clinicians, the medical director, the controller or office manager, and AI agents SHALL each be a roster entry. A system integration SHALL be a named integration identity, as PR-10 already allows. A patient or representative SHALL NOT be a roster entry. Each human roster entry SHALL reference exactly one party in the party register of `define-mvp-practice-foundation` PR-15, and an AI agent or a system integration SHALL reference none; a person who is both staff and a patient, guarantor or representative holds one roster entry, for the staff role only, never acts as patient, guarantor or representative through that roster entry, and both reference the same party (spec-critique correction SC-2, PROPOSED). Where the person's parties form a same-person group (`define-mvp-practice-foundation` PR-15), this rule, and every rule that asks whether a roster entry's own party holds a role on a record, treats the whole group as one party. The system SHALL refuse any action from an actor whose type it cannot determine.

**Contact points (design call D-33, PROPOSED, made for change `define-patient-gateway-and-escalation` DC-60).** A human roster entry MAY record contact points. Each is an email address or a telephone number, recorded for security notices and one-time codes only, never used for any other message, and no PHI is ever sent to it. Each contact point SHALL record when it was added. Every addition, change or removal of a contact point SHALL be audited (PR-10) and SHALL be notified, with no PHI, to every contact point recorded before the change, including the one replaced or removed, and to the person's enrolled mobile approver devices (`define-patient-gateway-and-escalation` MA-04). A contact point removed or replaced is kept for 30 days for one purpose only: to receive MA-04's security notices. A contact point removed with the marking `stolen` or `compromised` receives no further notice from the moment of the marking, and the marking is notified to every other contact point and every enrolled mobile approver device of the person. When any roster entry gains the practice manager recipe or the controller or office manager recipe, openPractice SHALL notify every existing holder of either recipe, with no PHI, on their enrolled mobile approver devices and contact points (design call D-33, PROPOSED).

#### Scenario: A remote VA signs in
- **WHEN** a remote VA signs in
- **THEN** the session carries actor type `remote_va` and the VA's own roster entry

#### Scenario: A staff member with two linked party records
- **WHEN** a staff member's roster entry references party P1, and party P2, linked to P1 as the same person, holds a patient record with a balance
- **THEN** the roster entry cannot act as that patient, and a write-off on that balance is the staff member's own record under `define-mvp-practice-foundation` PR-15

#### Scenario: A contact point is replaced
- **WHEN** the practice manager replaces the telephone number recorded as a contact point on a roster entry
- **THEN** the change is audited, and a notice with no PHI goes to every contact point recorded before the change, including the number replaced, and to the person's enrolled mobile approver devices

#### Scenario: A new practice manager is added
- **WHEN** a roster entry is given the practice manager recipe
- **THEN** every existing holder of the practice manager or the controller or office manager recipe is notified on their enrolled devices and contact points, with no PHI

#### Scenario: An unidentified caller
- **WHEN** a request arrives whose actor type cannot be determined
- **THEN** it is refused with a coded reason and nothing changes

### Requirement: AM-02 Role recipes extend the roster's seven
openPractice SHALL ship role recipes that compose but never edit. These are the seven recipes of the roster brainstorm: front desk, clinician, practice manager, biller, external biller, bookkeeper and time-boxed support. To them it adds five: remote VA, nurse, medical director, controller or office manager, and agent. Each recipe SHALL be named and versioned, with a plain-language card stating what it can and cannot do.

The grant set of each new recipe SHALL be fixed in planning and approved by the owner before the recipe ships. Until then, the new recipe SHALL grant nothing. No recipe SHALL grant clinical write authority, and the agent recipe SHALL NOT include any tier-4 sign-off or the D8 approval (BAB-17).

#### Scenario: A nurse recipe is assigned
- **WHEN** a nurse is added with the nurse recipe after its grant set is approved
- **THEN** the nurse holds exactly that grant set, and the card states what the nurse cannot do

#### Scenario: A recipe without an approved grant set
- **WHEN** a person is given the remote VA recipe before its grant set is approved
- **THEN** the recipe grants no access, and the assignment is shown as pending

### Requirement: AM-03 Every action records the actor, the actor type and on whose behalf
Every action, read and proposal SHALL record its actor's roster entry or integration identity, the actor type, and the principal on whose behalf it acted. A principal is, for example, the practice for an agent, a sponsoring human for an agent's proposal, or a patient for a representative. These records extend the PR-10 audit fields and SHALL follow EB-12: no PHI in logs, errors or diagnostics. An action whose principal cannot be determined SHALL be refused.

#### Scenario: An agent proposes a correction
- **WHEN** an agent proposes a demographic correction
- **THEN** the audit records the agent's roster entry, actor type `ai_agent`, its openWallet ID reference and the principal

#### Scenario: Missing principal
- **WHEN** an integration call omits the principal
- **THEN** it is refused with a coded reason, and nothing is written

### Requirement: AM-04 AI agents are named roster entries with an openWallet ID and a sponsoring human
Each AI agent SHALL be a roster entry with:
- a name;
- an openWallet ID (opensoft/openWallet);
- a sponsoring human who is an active roster entry and accountable for it;
- the agent recipe, and no other recipe;
- start and end dates;
- the model path its operator declares under AH-02: the model provider, endpoint and model (design call D-32, PROPOSED).

An agent SHALL hold only the agent recipe. No other recipe SHALL be assigned to an agent, and an agent's authority SHALL come only from the agent recipe and the grants a named human makes under AO-03. How an openWallet ID is verified SHALL be decided in planning. An agent SHALL own work like any roster entry, but SHALL NOT decide its own proposals (WI-03). When its sponsor is offboarded or inactive, the agent SHALL stop acting, drop to `observe`, and hand its open work to a named human until a new sponsor is recorded. An agent SHALL NOT sponsor another agent.

#### Scenario: An agent is added
- **WHEN** the practice manager adds an agent with a name, an openWallet ID and a sponsoring human
- **THEN** it appears on the roster and can own work items

#### Scenario: The sponsor leaves
- **WHEN** an agent's sponsoring human is offboarded
- **THEN** the agent drops to `observe`, and its open work is reassigned to a named human

#### Scenario: A second recipe for an agent
- **WHEN** someone assigns the practice manager recipe to an agent
- **THEN** the assignment is refused, and the agent keeps only the agent recipe

### Requirement: AM-05 The same-day offboarding switch applies to people, VAs and agents
One offboarding action SHALL apply identically to an on-site person, a remote VA, a VA company and an AI agent. It SHALL:
- revoke every session, token and grant;
- cancel the actor's pending delayed actions;
- reassign its open work to named owners;
- record a dated termination of access.

Offboarding a VA company SHALL offboard every VA it supplies. Offboarding an agent SHALL also revoke every autonomy opt-in held for it.

#### Scenario: A VA is offboarded at noon
- **WHEN** the office manager offboards a remote VA
- **THEN** the VA's sessions end at once, the VA's queue is reassigned to named owners, and a dated termination record exists

#### Scenario: An agent is offboarded mid-delay
- **WHEN** an agent with an action waiting in its delay window is offboarded
- **THEN** the delayed action is cancelled and not executed, and the cancellation is recorded

### Requirement: AM-06 Every owner is a roster entry
Every work item, escalation and gate SHALL have an owner that is a person or agent roster entry. Ownership SHALL NOT rest with a role name, a group mailbox or an integration identity. When no assignment rule applies, the practice's controller or office manager SHALL own the item.

#### Scenario: A new item matches no rule
- **WHEN** a work item is created that no assignment rule covers
- **THEN** the controller or office manager owns it

#### Scenario: An item would be owned by a role
- **WHEN** a configuration tries to assign items to "billing" as a role, with no roster entry
- **THEN** the configuration is refused
