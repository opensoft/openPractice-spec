## ADDED Requirements

### Requirement: AM-01 Every actor has a declared type
openPractice SHALL recognize exactly these actor types:
- on-site staff;
- remote virtual assistant (VA);
- nurse;
- clinician;
- medical director;
- controller or office manager;
- outside accountant (ruling 141);
- AI agent;
- patient or representative, acting only through the HealthLinc gateway (change `define-patient-gateway-and-escalation`);
- system integration.

Staff, VAs, nurses, clinicians, the medical director, the controller or office manager, the outside accountant, and AI agents SHALL each be a roster entry. A system integration SHALL be a named integration identity, as PR-10 already allows. A patient or representative SHALL NOT be a roster entry. Each human roster entry SHALL reference exactly one party in the party register of `define-mvp-practice-foundation` PR-15, and an AI agent or a system integration SHALL reference none; a person who is both staff and a patient, guarantor or representative holds one roster entry, for the staff role only, never acts as patient, guarantor or representative through that roster entry, and both reference the same party (spec-critique correction SC-2, PROPOSED). Where the person's parties form a same-person group (`define-mvp-practice-foundation` PR-15), this rule, and every rule that asks whether a roster entry's own party holds a role on a record, treats the whole group as one party. The system SHALL refuse any action from an actor whose type it cannot determine.

**The outside accountant and counsel (ruling 141).** The practice's accountant, wherever these changes give it an approval or a notice (`define-mvp-practice-foundation` BAB-17, AI-12, AI-20 and PR-15; WI-03; change `define-patient-gateway-and-escalation` MA-07), SHALL be a roster entry of the outside-accountant type. It SHALL hold the outside-accountant recipe of AM-02 and no other, because ruling 141 lets it do nothing else. Who may hold such an entry is set by change `define-practice-readiness` SR-04. Counsel needs no sign-in and SHALL NOT be a roster entry (ruling 141): where a requirement records counsel's review or confirmation, the person that requirement names records it and names counsel.

**Contact points (design call D-33, PROPOSED, made for change `define-patient-gateway-and-escalation` DC-60 under its ruling 137).** A human roster entry MAY record contact points. Each is an email address or a telephone number, recorded for security notices and one-time codes only, never used for any other message, and no PHI is ever sent to it. Each contact point SHALL record when it was added. Every addition, change or removal of a contact point SHALL be audited (PR-10) and SHALL be notified, with no PHI, to every contact point recorded before the change, including the one replaced or removed, and to the person's enrolled mobile approver devices (`define-patient-gateway-and-escalation` MA-04). A contact point removed or replaced is kept for 30 days for one purpose only: to receive MA-04's security notices. A contact point removed with the marking `stolen` or `compromised` receives no further notice from the moment of the marking, and the marking is notified to every other contact point and every enrolled mobile approver device of the person. Applying either marking, to a contact point or to a device (`define-patient-gateway-and-escalation` MA-06), SHALL need a fresh check under AM-07 bound to that marking, and SHALL be notified with no PHI to every other holder of the practice manager or office manager role. A person with no credential left may mark a device, but never a contact point, in the same act as using their recovery code (MA-04), the code standing as the check. Marking a contact point needs a fresh check made with one of the person's credentials, or a second named person who holds the practice manager or office manager role, never the person themselves, with their own fresh check; that second person may also mark a device for a person with no credential left (design call D-33, PROPOSED). When any roster entry gains the practice manager recipe or the controller or office manager recipe, openPractice SHALL notify every existing holder of either recipe, with no PHI, on their enrolled mobile approver devices and contact points (design call D-33, PROPOSED).

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

#### Scenario: An outside accountant is added
- **WHEN** the practice manager adds a roster entry for the practice's outside accountant
- **THEN** it carries actor type outside accountant and the outside-accountant recipe only, and giving it any other recipe is refused

#### Scenario: Counsel's review is recorded
- **WHEN** a requirement records counsel's review of a policy version
- **THEN** the person the requirement names records it and names counsel, and counsel needs no roster entry and no sign-in

#### Scenario: An unidentified caller
- **WHEN** a request arrives whose actor type cannot be determined
- **THEN** it is refused with a coded reason and nothing changes

### Requirement: AM-02 Role recipes extend the roster's seven
openPractice SHALL ship role recipes that compose but never edit. These are the seven recipes of the roster brainstorm: front desk, clinician, practice manager, biller, external biller, bookkeeper and time-boxed support. To them it adds six: remote VA, nurse, medical director, controller or office manager, agent, and outside accountant. Each recipe SHALL be named and versioned, with a plain-language card stating what it can and cannot do.

The grant set of each new recipe SHALL be fixed in planning and approved by the owner before the recipe ships. Until then, the new recipe SHALL grant nothing. No recipe SHALL grant clinical write authority, and the agent recipe SHALL NOT include any tier-4 sign-off or the D8 approval (BAB-17).

**The outside-accountant recipe (ruling 141).** Its card SHALL state that its holder can read the books and approve accounting policy, estimator versions and the write-off threshold, and nothing else (ruling 141). Accounting policy here is the D8 policy of `define-mvp-practice-foundation` AI-20. What reading the books covers, within `define-customer-financial-data-boundaries` ("Accountant and biller access are independently bounded"), and which write-off threshold it approves, since none of these five changes defines one yet, are fixed with its grant set in planning (design call D-37, PROPOSED); like every new recipe, it grants nothing until then. It SHALL NOT be assigned to an AI agent.

**Assigning the medical director recipe (ruling 146).** Assigning the medical director recipe SHALL carry ruling 98's check. Ruling 98's accepted words are: "A second named person confirms; a one-administrator practice records licence verification against the board's public record." The role sets the escalation policy (change `define-patient-gateway-and-escalation` ER-08) and signs health-adjacent answer types (AH-05 exception 1). How the check is made is design call D-38 (PROPOSED): the second named person is a human roster entry other than the person assigned and, where another eligible named human is on the roster, other than the practice manager who made the assignment, who confirms with a fresh check under AM-07; where the roster holds no other named human, the assignment records the person's licence number, the issuing state board, and the date and result of a check against that board's public record, with the recording person and the role used, and a licence that the public record does not show as active refuses the assignment. Until the check is recorded, the assignment is pending and the recipe grants nothing (fail closed), so the routing-clock policy keeps its fail-closed defaults (ER-08) and no health-adjacent answer type is approved.

#### Scenario: A nurse recipe is assigned
- **WHEN** a nurse is added with the nurse recipe after its grant set is approved
- **THEN** the nurse holds exactly that grant set, and the card states what the nurse cannot do

#### Scenario: A recipe without an approved grant set
- **WHEN** a person is given the remote VA recipe before its grant set is approved
- **THEN** the recipe grants no access, and the assignment is shown as pending

#### Scenario: The outside-accountant recipe before its grant set is approved
- **WHEN** an outside accountant's roster entry holds the outside-accountant recipe before its grant set is approved
- **THEN** the recipe grants nothing, no D8 approval can be given, and `define-mvp-practice-foundation` AI-20's go-live gate stays closed

#### Scenario: A medical director confirmed by a second person
- **WHEN** the practice manager gives a clinician the medical director recipe, and another named human on the roster confirms it with a fresh check
- **THEN** the assignment takes effect and records both people and the roles used

#### Scenario: A medical director in a one-administrator practice
- **WHEN** the only named human on the roster gives themselves the medical director recipe
- **THEN** the assignment waits until a licence check against the state board's public record is recorded, and is refused if the record does not show the licence as active

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

### Requirement: AM-07 Every human signs in with two steps, and each sign-off needs its own fresh check
Every human roster entry SHALL sign in to openPractice with two steps: a passkey, or a password plus a second step (ruling 136). A password alone SHALL NOT sign anyone in. Each sign-off at the workstation SHALL need its own fresh check, as on the phone (ruling 136; change `define-patient-gateway-and-escalation` MA-07, ruling 105): neither the sign-in nor a check made for another sign-off SHALL stand in for it, however recent. A sign-off is any approval, release decision, attestation or signature that `define-mvp-practice-foundation` BAB-17, WI-03 or another requirement records with its signer.

These details are design calls under ruling 136 (D-36, PROPOSED):
- **The second step** is a verification on a mobile approver device the person has enrolled (`define-patient-gateway-and-escalation` MA-04), or a security key registered to the person's roster entry. A one-time code sent by text message or email is not a second step, because a contact point carries security notices and enrolment codes only (AM-01).
- **The fresh check for a sign-off** is a passkey or security-key verification at the workstation, or a verification on the person's enrolled mobile approver device. It answers a single-use challenge that the server issues for that item and decision, valid for 2 minutes, and produces an assertion bound to the item, the decision and the time, which the server verifies and records with the sign-off, as MA-07 requires on the phone. A typed password never counts as a fresh check.
- **A further passkey or security key** is registered only after a sign-in with two steps and a fresh check under this requirement, and a typed password alone never registers one. Each registration is audited, and notified with no PHI to every enrolled device and contact point of the person, as an enrolment is (MA-04).
- **A new roster entry's first passkey or security key** is registered through a single-use invitation, which expires after 24 hours. Issuing the invitation is a sign-off under this requirement: the practice manager gives it with a fresh check bound to that invitation. It is audited, notified with no PHI to every other holder of the practice manager or office manager role, as MA-04 does for a first device, and delivered to the person in person or as a one-time code sent to a contact point recorded on their roster entry (AM-01).
- **Installation.** The first roster entry of a new practice registers its first passkey or security key at installation, and only while the roster holds no human roster entry at all; a practice whose people are all locked out uses the last-resort recovery below, never this. The installation records who installed openPractice, and is audited. Two contact points SHALL be recorded on that roster entry at installation (AM-01).
- **A recovery code.** A single-use recovery code, kept offline, SHALL be issued to each person with their first credential, whether a passkey, a security key or a mobile approver device, and is used as MA-04 states. It is given to that person directly, never to whoever installs openPractice or issues an invitation.
- **Revoking a passkey or security key.** Its holder MAY revoke it with a fresh check made with any of their credentials, and a holder of the practice manager or office manager role MAY revoke it with their own fresh check. Each revocation is audited, and notified with no PHI to every enrolled device and contact point of the person and, when a manager revokes it, to the other holders of those roles.
- **A person with no credential left.** Where the person has no passkey, security key or enrolled device left, a new one takes effect only through the enrolment paths, waits, notices and cancellation that MA-04 sets for a mobile approver device (its design call DC-60): a second named person's confirmation, the person's recovery code, or codes sent to two contact points that qualify by age, each after 24 hours. A person who has lost their recovery code as well declares it lost in the same act as entering both contact-point codes (MA-04). Until a new credential takes effect, the person has no access to openPractice, at the workstation or on the phone. Where others are on the roster, their work goes on. Where the person is the practice's only person, nobody who can act is reached while they are locked out: an urgent escalation still runs, ER-09 records that it reached no second person, and only the patient's emergency guidance runs without them (change `define-patient-gateway-and-escalation` ER-09).
- **The last-resort recovery.** Where no other path above can be used, whoever operates the installation MAY start a recovery on the server for an existing roster entry that is active and has not been offboarded, and only where no second named person may confirm under MA-04. It creates no roster entry, grants no recipe or role and changes no contact point. It is recorded with who started it and audited. It is announced at once, and again on each day of its wait, with no PHI, to every contact point and enrolled device on file for that entry and to every other holder of the practice manager or office manager role. It takes effect only after 7 days, and any of those recipients MAY cancel it in that time through a link that can only cancel; a cancellation is audited and raises an exception. When it takes effect, the server issues the single-use invitation this requirement defines, delivered in person, with the operator recording how they confirmed the person's identity, or as a one-time code to a contact point on file; the person's old recovery code is retired, and a new one is issued with the new credential.
- **Session lifetime.** A workstation session SHALL end after 15 minutes without activity, D-36's value for the automatic idle log-off that constitution Principle I requires, and in any case 12 hours after sign-in; the person then signs in again with two steps. Offboarding ends every session at once (AM-05).
- The mobile approver signs in under MA-04, where the enrolled device and its verification are the two steps.

#### Scenario: A stolen password
- **WHEN** someone who has only a person's password tries to sign in at the workstation
- **THEN** the sign-in is refused, because a password alone never signs anyone in

#### Scenario: A passkey
- **WHEN** a person signs in at the workstation with a passkey
- **THEN** the sign-in succeeds with no further step

#### Scenario: A sign-off right after signing in
- **WHEN** a person signs in with a password and a verification on their enrolled phone, and approves a refund a minute later
- **THEN** the approval asks for its own fresh check, bound to that refund and that decision, and the sign-in does not stand in for it

#### Scenario: A code by text message
- **WHEN** a person offers a code sent to their phone by text message as the second step
- **THEN** it is not accepted as a second step

#### Scenario: A made-up roster entry is given a workstation passkey
- **WHEN** someone in a practice manager's session left open creates a roster entry and issues it an invitation with no fresh check bound to that invitation
- **THEN** the invitation is refused; an invitation that is issued with that check is notified to every other holder of the practice manager or office manager role

#### Scenario: A new practice-manager entry is given a passkey from an open session
- **WHEN** someone in a practice manager's session left open creates a practice-manager entry, issues it an invitation and plans to offboard the real practice manager
- **THEN** the invitation is refused without the real practice manager's own fresh check bound to it, so the new entry gets no credential from the open session; giving the entry the practice manager recipe is notified to the existing holders (AM-01)

#### Scenario: A reinstallation over an existing roster
- **WHEN** openPractice is reinstalled or restored, and the roster already holds a human roster entry, with or without a credential
- **THEN** no credential is registered at installation, and the attempt is audited with who made it; a locked-out practice uses the last-resort recovery instead

#### Scenario: A further passkey
- **WHEN** a signed-in person registers a second passkey
- **THEN** it needs a fresh check, is audited, and is notified to every enrolled device and contact point of the person

#### Scenario: A sole administrator loses the only passkey
- **WHEN** a practice's only administrator, with no enrolled phone, loses the passkey registered at installation
- **THEN** the recovery code issued with that passkey lets a new passkey take effect 24 hours later, after notices any recipient may cancel; until then the administrator has no access, and urgent routing runs without them

#### Scenario: A sole administrator has lost the passkey and the recovery code
- **WHEN** a practice's only administrator, with no credential left, has lost the recovery code too, and both contact points have been on file for more than 7 days
- **THEN** they declare the code lost in the same act as entering both contact-point codes; the declaration takes effect only with that enrolment, every contact point and device is notified, and the enrolment takes effect after 24 hours unless cancelled

#### Scenario: A passkey registered from a stolen synced passkey is revoked
- **WHEN** a person is notified of a passkey they did not register
- **THEN** they revoke it with a fresh check made with another of their credentials, or a practice manager revokes it with their own fresh check, and the revocation is audited and notified

#### Scenario: The last-resort recovery
- **WHEN** a one-person practice's administrator has no credential, no recovery code and a contact point they can no longer reach, and the operator of the installation starts a last-resort recovery for that roster entry
- **THEN** it is announced at once and daily to every contact point and device on file, takes effect only after 7 days unless one of them cancels it, and then yields a single-use invitation delivered in person with the identity check recorded, or as a code to a contact point on file; it creates no entry and grants no role

#### Scenario: The last-resort recovery for an offboarded entry
- **WHEN** an operator tries to start a last-resort recovery for a roster entry that was offboarded
- **THEN** it is refused and the attempt is audited

#### Scenario: An idle workstation
- **WHEN** a signed-in workstation has no activity for 15 minutes
- **THEN** the session ends, and the next action needs a new sign-in with two steps
