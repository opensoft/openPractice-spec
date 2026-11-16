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

**Contact points (design call D-33, PROPOSED, made for change `define-patient-gateway-and-escalation` DC-60 under its ruling 137).** A human roster entry MAY record contact points. Each is an email address or a telephone number, recorded for security notices and one-time codes only, never used for any other message, and no PHI is ever sent to it. Each contact point SHALL record when it was added and when the person confirmed it with a fresh check made with one of their own credentials (AM-07); it counts toward MA-04's 7-day age only from that confirmation, so a contact point someone else recorded on the person's roster entry never counts until the person confirms it (design call D-33, PROPOSED). Every addition, change or removal of a contact point SHALL be audited (PR-10) and SHALL be notified, with no PHI, to every contact point recorded before the change, including the one replaced or removed, and to the person's enrolled mobile approver devices (`define-patient-gateway-and-escalation` MA-04). A contact point removed or replaced is kept for 30 days for one purpose only: to receive MA-04's security notices. A contact point removed with the marking `stolen` or `compromised` receives no further notice from the moment of the marking, and the marking is notified to every other contact point and every enrolled mobile approver device of the person. Applying either marking, to a contact point or to a device (`define-patient-gateway-and-escalation` MA-06), SHALL need a fresh check under AM-07 bound to that marking, and SHALL be notified with no PHI to every other holder of the practice manager or office manager role. The person applies a marking to their own device or contact point with a fresh check made with one of their credentials. In the same act as using their recovery code (MA-04), the code standing as the check, a person may revoke and mark their own devices, including a device they no longer hold that still counts as a credential, but never a contact point; the enrolment then proceeds as a recovery, with its 24-hour wait and notices (design call D-33, PROPOSED). A second named person who holds the practice manager or office manager role, never the person themselves, MAY mark another person's device or contact point, with their own fresh check, only for a person with no credential left (design call D-33, PROPOSED). A manager who removed a colleague's phone or passkey cannot approve the replacement (ruling 198). This change reads removing, how long that bar lasts and whom it covers as follows, each a detail of design call D-33 (PROPOSED): a holder of either role who revoked any of a person's passkeys, security keys or devices, or marked any of their devices or contact points, SHALL NOT confirm that person's new credential under MA-04, does not count as that person's second person there, so that MA-04's recovery-code and contact-point paths open for them, and does not count as the second named person who blocks a last-resort recovery for them (AM-07), until the person again holds a credential obtained by a path that does not need that holder, namely their recovery code, codes sent to contact points they confirmed, the confirmation of a holder the bar does not cover, or the last-resort recovery, or makes a fresh check with a credential that neither the barred holder nor any holder the bar covers, as the next sentence states, confirmed or touched, meaning one whose enrolment none of them confirmed, for which none of them issued the invitation, and which none of them ever revoked or marked; each later revocation or marking by that holder starts the bar again. While the bar holds, it covers in the same way every holder of either role whose roster entry the barred holder created, to whom the barred holder gave the practice manager or office manager role, or for whom the barred holder issued the invitation for their first passkey or security key or confirmed their first device, and every holder whom a holder so covered made in one of those ways in turn: such a holder is not another holder for that person, does not confirm for them, does not count as their second person, and does not block a last-resort recovery for them (review finding P8-9). Any other holder of either role may still confirm. The bar cannot deadlock: it closes only the confirmation of the holders it covers, and while it holds they block nothing, so every other path stays open to the person, and it ends as soon as one of them gives the person a credential (design call D-33, PROPOSED). When any roster entry gains the practice manager recipe or the controller or office manager recipe, openPractice SHALL notify every existing holder of either recipe, with no PHI, on their enrolled mobile approver devices and contact points (design call D-33, PROPOSED). Creating a human roster entry whose party, or whose person's legal name, matches that of an existing or ended human roster entry SHALL be notified, with no PHI, to that entry's contact points still on file (design call D-33, PROPOSED).

#### Scenario: A remote VA signs in
- **WHEN** a remote VA signs in
- **THEN** the session carries actor type `remote_va` and the VA's own roster entry

#### Scenario: A staff member with two linked party records
- **WHEN** a staff member's roster entry references party P1, and party P2, linked to P1 as the same person, holds a patient record with a balance
- **THEN** the roster entry cannot act as that patient, and a write-off on that balance is the staff member's own record under `define-mvp-practice-foundation` PR-15

#### Scenario: A contact point is replaced
- **WHEN** the practice manager replaces the telephone number recorded as a contact point on a roster entry
- **THEN** the change is audited, and a notice with no PHI goes to every contact point recorded before the change, including the number replaced, and to the person's enrolled mobile approver devices

#### Scenario: A manager tries to mark a colleague's contact point
- **WHEN** a practice manager, with their own fresh check, tries to mark as `compromised` a contact point of a person who still holds a passkey
- **THEN** the marking is refused, because a second person may mark another person's device or contact point only for a person with no credential left; the person marks it with a fresh check made with their own passkey

#### Scenario: The manager who removed a credential cannot restore it
- **WHEN** a sole practice manager revokes another person's passkey and phone, marks the phone `stolen` and each of the person's contact points `compromised`, and then confirms a new phone in that person's name
- **THEN** the confirmation is refused, because that manager revoked and marked the person's credentials and the person has obtained no credential by another path since; the person can still enrol with their recovery code, and the manager does not block a last-resort recovery for them

#### Scenario: A manager the barred manager made cannot restore the credential
- **WHEN** a sole practice manager revokes and marks every credential of another person, then creates a roster entry, gives it the office manager role and issues the invitation for its first passkey, and that entry confirms a new phone in the person's name
- **THEN** the confirmation is refused, because the bar covers every holder the barred manager made, and so does a confirmation by a holder that entry makes in turn; that entry does not block a last-resort recovery for the person either, and the person enrols with their recovery code, with codes to two contact points they confirmed at least 7 days ago, neither marked, or through the last-resort recovery

#### Scenario: A fresh check with a credential the barred manager touched does not end the bar
- **WHEN** a practice manager revoked a person's phone, and the person then makes a fresh check with a passkey registered through an invitation that same manager issued
- **THEN** the bar holds, because the barred manager issued that credential's invitation; a fresh check with a passkey the person registered at installation, or through another holder's invitation, ends it

#### Scenario: The bar does not lapse with time
- **WHEN** 60 days after a sole practice manager revoked and marked every credential of another person, who has obtained none since, that manager confirms a new phone in the person's name
- **THEN** the confirmation is still refused, and the manager still does not block a last-resort recovery for that person

#### Scenario: The bar ends when the person recovers another way
- **WHEN** after the office manager revoked a person's lost phone, the person enrols a new phone with their recovery code and it takes effect
- **THEN** the bar ends, and the office manager may confirm that person's later enrolments again, until they next revoke or mark one of the person's credentials

#### Scenario: A contact point recorded by someone else
- **WHEN** a practice manager records two contact points of their own on another person's roster entry, and asks for contact-point codes for that person eight days later
- **THEN** no codes are sent, because the person has not confirmed either contact point with their own fresh check

#### Scenario: A duplicate entry in a colleague's name
- **WHEN** a practice manager creates a roster entry with the legal name of an existing colleague and two contact points of their own
- **THEN** the colleague's contact points on file are notified, with no PHI, that an entry matching them was created

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

**The outside-accountant recipe (ruling 141).** Its card SHALL state that its holder can read the books and approve accounting policy, estimator versions and the write-off threshold, and nothing else (ruling 141). Accounting policy here is the D8 policy of `define-mvp-practice-foundation` AI-20, with the record beside it of who books the bank debits openPractice explains, which the accountant confirms: that confirmation is part of accounting policy (ruling 197), and the record itself is that change's design call F-18 (PROPOSED). What reading the books covers, within `define-customer-financial-data-boundaries` ("Accountant and biller access are independently bounded"), and which write-off threshold it approves, since none of these five changes defines one yet, are fixed with its grant set in planning (design call D-37, PROPOSED); like every new recipe, it grants nothing until then. It SHALL NOT be assigned to an AI agent.

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
Every action, read and proposal SHALL record its actor's roster entry or integration identity, the actor type, and the principal on whose behalf it acted. A principal is, for example, the practice for an agent, a sponsoring human for an agent's proposal, or a patient for a representative. An agent acting at a person's request SHALL record that person as its principal, so that the own-record rule of `define-mvp-practice-foundation` PR-15 sees them (that change's design call F-7, PROPOSED). These records extend the PR-10 audit fields and SHALL follow EB-12: no PHI in logs, errors or diagnostics. An action whose principal cannot be determined SHALL be refused.

#### Scenario: An agent proposes a correction
- **WHEN** an agent proposes a demographic correction
- **THEN** the audit records the agent's roster entry, actor type `ai_agent`, its openWallet ID reference and the principal

#### Scenario: An agent acts at a staff member's request
- **WHEN** a staff member asks an agent to lift a payment hold
- **THEN** the audit records that staff member as the agent's principal, and a hold on the staff member's own record is refused as their own-record act

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
- record a dated termination of access;
- keep the ended roster entry's party, where it references one (AM-01), its actor type and its end date readable, so that a look-back over ended entries can run; for an entry that references no party, an AI agent's, its actor type and its end date are kept readable (design call D-42, PROPOSED).

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

These details are design calls under ruling 136 (D-36, PROPOSED), except where a bullet cites ruling 161, 184, 191, 205 or 206 for what it rules:
- **The second step** is a verification on a mobile approver device the person has enrolled (`define-patient-gateway-and-escalation` MA-04), or a security key registered to the person's roster entry, and never a one-time code sent by text message or email (ruling 184); a contact point carries security notices and enrolment codes only (AM-01).
- **The fresh check for a sign-off** is a passkey or security-key verification at the workstation, or a verification on the person's enrolled mobile approver device. It answers a single-use challenge that the server issues for that item and decision, valid for 2 minutes, and produces an assertion bound to the item, the decision and the time, which the server verifies and records with the sign-off, as MA-07 requires on the phone. A typed password never counts as a fresh check.
- **A further passkey or security key** is registered only after a sign-in with two steps and a fresh check under this requirement, and a typed password alone never registers one. Each registration is audited, and notified with no PHI to every enrolled device and contact point of the person, as an enrolment is (MA-04).
- **A new roster entry's first passkey or security key** is registered through a single-use invitation, which expires after 24 hours. Issuing the invitation is a sign-off under this requirement: the practice manager gives it with a fresh check bound to that invitation. It is audited, notified with no PHI to every other holder of the practice manager or office manager role, as MA-04 does for a first device, and delivered to the person in person or as a one-time code sent to a contact point recorded on their roster entry (AM-01).
- **Installation.** The first roster entry of a new practice registers its first passkey or security key at installation, and only while the roster holds no human roster entry at all; a practice whose people are all locked out uses the last-resort recovery below, never this. The installation records who installed openPractice, and is audited. Two contact points SHALL be recorded on that roster entry at installation, and confirmed there by the person with a fresh check made with that first credential (AM-01). During initial set-up, the practice's first phone may be enrolled without ruling 137's wait, until the first phone is enrolled or the practice goes live, whichever comes first (ruling 161); the practice goes live at the earliest of the first patient record, the first patient-app feature switched on, the first live accounting connection, or the first agent permission (ruling 206). It is enrolled under MA-04's set-up path, by a holder of the practice manager or office manager role, and the reading of each event and every other detail are MA-04's design call DC-69 (PROPOSED).
- **A recovery code.** A single-use recovery code, kept offline, SHALL be issued to each person with their first credential, a passkey or a security key, since nobody enrols a mobile approver device before holding one (MA-04), and is used as MA-04 states. It is given to that person directly, never to whoever installs openPractice or issues an invitation.
- **Revoking a passkey or security key.** Its holder MAY revoke it with a fresh check made with any of their credentials, and a holder of the practice manager or office manager role MAY revoke it with their own fresh check. Each revocation is audited, and notified with no PHI to every enrolled device and contact point of the person and, when a manager revokes it, to the other holders of those roles.
- **A person with no credential left.** Where the person has no passkey, security key or enrolled device left, a new one takes effect only through the enrolment paths, waits, notices and cancellation that MA-04 sets for a mobile approver device (its design call DC-60): a second named person's confirmation, the person's recovery code, or codes sent to two contact points that qualify by age, each after 24 hours. An enrolment for such a person is a recovery, even if no device was ever enrolled for them (MA-04). A person who has lost their recovery code as well declares it lost in the same act as entering both contact-point codes (MA-04). Until a new credential takes effect, the person has no access to openPractice, at the workstation or on the phone. Where others are on the roster, their work goes on. Where the person is the practice's only person, nobody who can act is reached while they are locked out: an urgent escalation still runs, ER-09 records that it reached no second person, and only the patient's emergency guidance runs without them (change `define-patient-gateway-and-escalation` ER-09).
- **The last-resort recovery** (rulings 191 and 205). Ruled: whoever runs the installation MAY start a last-resort recovery from the server; it is recorded, announced to every contact point and device on file each day for 7 days, and cancellable from any of them; the new sign-in is handed over in person only, with a record of how the person's identity was confirmed; and it trusts whoever runs the server, who already controls the data (ruling 191). When the recovery is started in person, the person MAY name one contact point or device on file that can only object, not cancel; an objection stops the recovery completing by itself after 7 days, and the new sign-in is then issued only after a second in-person identity check (ruling 205). Every detail below beyond those words is D-36 (PROPOSED). Where no other path above can be used, whoever operates the installation MAY start a recovery on the server for an existing roster entry that is active and has not been offboarded, and only where no second named person may confirm under MA-04: that is, where no other human roster entry holds the practice manager or office manager role together with a credential able to make a fresh check now with which MA-04's confirmation can be given, which is an enrolled mobile approver device that is not revoked; a holder of either role whom AM-01's bar covers for that person (ruling 198 rules its main line), the barred holder or a holder that holder made, for as long as that bar holds, does not count. It creates no roster entry, grants no recipe or role and changes no contact point. It is recorded with who started it and audited. A start is made in person when the person is present and the operator records how they confirmed the person's identity. Only one last-resort recovery MAY be open for a roster entry at a time: a start while one is open is refused and audited, except that a start made in person supersedes an open attempt that was not, cancelling it with an audit entry and an announcement. After a cancellation, a new one MAY start only once 30 days have passed, unless it is started in person; an attempt whose invitation expires unredeemed counts as cancelled for this rule. It is announced at once, and again on each day of its 7-day wait, with no PHI, to every contact point and device on file for that entry (ruling 191), which this requirement reads as every contact point recorded on the entry and every enrolled device that is not revoked (D-36, PROPOSED), and also to every other holder of the practice manager or office manager role (D-36, PROPOSED). It takes effect only after 7 days, and any of those recipients MAY cancel it in that time through a link that can only cancel: every contact point and device on file (ruling 191), except one the person names to only object under ruling 205, and the other holders of those roles (D-36, PROPOSED); a cancellation is audited and raises an exception. When the recovery is started in person, the person MAY name one contact point or device on file that can only object, not cancel, and every other contact point and device on file still cancels (rulings 205 and 191). The named channel is recorded with the attempt and keeps receiving every announcement, through a link that can only object, and an objection is audited and announced like a cancellation (D-36, PROPOSED). An objection stops the recovery completing by itself after 7 days, and the new sign-in is then issued only after a second in-person identity check (ruling 205), which the operator records with the objection shown (D-36, PROPOSED). A named channel that never objects leaves the recovery to take effect after 7 days unless another recipient cancels it (D-36, PROPOSED). When it takes effect, the server issues the single-use invitation this requirement defines, delivered only in person, with the operator recording how they confirmed the person's identity (ruling 191), and never as a code to a contact point; the person's old recovery code is retired, and a new one is issued with the new credential.
- **Session lifetime** (ruling 184). A workstation session SHALL end after 15 minutes without activity, the automatic idle log-off that constitution Principle I requires, and in any case 12 hours after sign-in (ruling 184); the person then signs in again with two steps. Offboarding ends every session at once (AM-05).
- **A practice may tighten these** (ruling 184). A practice MAY tighten the second step and the session limits, for example by requiring a security key, or by setting a shorter idle time or session lifetime (ruling 184), and SHALL NOT loosen them, which is how this change reads "may tighten these" (D-36, PROPOSED). The practice manager sets a tightening, as a tier-2 act, audited (D-36, PROPOSED).
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
- **WHEN** a practice's only administrator, with no credential left, has lost the recovery code too, and they confirmed both contact points with their own fresh check more than 7 days ago
- **THEN** they declare the code lost in the same act as entering both contact-point codes; the declaration takes effect only with that enrolment, every contact point and device is notified, and the enrolment takes effect after 24 hours unless cancelled

#### Scenario: A passkey registered from a stolen synced passkey is revoked
- **WHEN** a person is notified of a passkey they did not register
- **THEN** they revoke it with a fresh check made with another of their credentials, or a practice manager revokes it with their own fresh check, and the revocation is audited and notified

#### Scenario: The last-resort recovery
- **WHEN** a one-person practice's administrator has no credential, no recovery code and a contact point they can no longer reach, and the operator of the installation starts a last-resort recovery for that roster entry
- **THEN** it is announced at once and daily to every contact point and device on file, takes effect only after 7 days unless one of them cancels it, and then yields a single-use invitation delivered only in person, with the identity check recorded, and never as a code to a contact point, which may be in someone else's hands; it creates no entry and grants no role

#### Scenario: The last-resort recovery for an offboarded entry
- **WHEN** an operator tries to start a last-resort recovery for a roster entry that was offboarded
- **THEN** it is refused and the attempt is audited

#### Scenario: A second last-resort recovery while one is open
- **WHEN** the operator starts a last-resort recovery for a roster entry that already has one open
- **THEN** it is refused, and the attempt is audited

#### Scenario: A last-resort recovery restarted after a cancellation
- **WHEN** a last-resort recovery was cancelled 10 days ago, and the operator starts another for the same roster entry on a telephone request
- **THEN** it is refused until 30 days have passed since the cancellation; started with the person present in person and the identity check recorded, it may begin at once

#### Scenario: A taken-over contact point named to only object
- **WHEN** a person present in person, with the identity check recorded, names an email address that someone else has taken over as the one channel that can only object, and the operator starts a last-resort recovery
- **THEN** that address still receives every announcement, but its link can only object; if it objects, the recovery does not complete by itself after 7 days, and the new sign-in is issued only after a second in-person identity check (ruling 205); every other contact point and device on file can still cancel it (ruling 191)

#### Scenario: An impostor names the real person's channel to only object
- **WHEN** an impostor who fooled the operator at an in-person start names the real person's only working contact point as the channel that can only object
- **THEN** that contact point still receives every announcement, the real person objects from it, the recovery does not complete by itself, and the new sign-in is issued only after a second in-person identity check, which the impostor must pass again (ruling 205)

#### Scenario: A contact point not named still cancels
- **WHEN** a person at an in-person start names one device as the channel that can only object, and the day's announcement reaches an email address on file that they did not name
- **THEN** its link cancels the attempt, as every channel on file but the named one may (ruling 191)

#### Scenario: An in-person start supersedes a telephone request
- **WHEN** an attempt started on a telephone request is open, and the person comes in person and the operator records the identity check
- **THEN** the open attempt is cancelled, with an audit entry and an announcement, and the in-person attempt starts

#### Scenario: A last-resort invitation expires unredeemed
- **WHEN** a last-resort attempt's invitation expires without being redeemed
- **THEN** it counts as a cancellation, so a new attempt waits 30 days unless it is started in person

#### Scenario: Two managers are both locked out
- **WHEN** a practice's two managers both have no credential left, and the operator starts a last-resort recovery for one of them
- **THEN** the other does not block it, because they hold no enrolled device with which to confirm; they receive its announcements and may cancel it

#### Scenario: Another manager holds an enrolled phone
- **WHEN** another practice manager holds an enrolled mobile approver device that is not revoked, and the operator tries to start a last-resort recovery for a locked-out person
- **THEN** it is refused, and that manager confirms the person's new credential under MA-04 instead

#### Scenario: An idle workstation
- **WHEN** a signed-in workstation has no activity for 15 minutes
- **THEN** the session ends, and the next action needs a new sign-in with two steps

#### Scenario: A practice tightens the idle time, and tries to loosen the session lifetime
- **WHEN** the practice manager sets the idle time to 10 minutes, and later tries to set the session lifetime to 16 hours
- **THEN** the 10 minutes apply; the 16 hours are refused, because a practice may tighten these limits and never loosen them

#### Scenario: A last-resort recovery is cancelled from a contact point on file
- **WHEN** a last-resort recovery started on a telephone request is open, and the day's announcement reaches an email address on file that the real person still reads
- **THEN** its link cancels the attempt, as any contact point or device on file may, and the cancellation is audited and raises an exception
