## ADDED Requirements

### Requirement: MA-01 The mobile approver is a thin client of the same versioned API
The openPractice mobile approver SHALL be a thin client of openPractice's versioned API layer, the same API every client uses (PR-09; constitution Principle V; change `define-actors-agents-and-work-inbox` AO-02) (rulings 35 and 38). Every authorization, tier and signer check SHALL run on the server. The app SHALL hold no business rule that the server does not also enforce. It SHALL show each user only what that user's roles allow (AM-02).

#### Scenario: The app loads the work inbox
- **WHEN** a practice manager opens the mobile approver
- **THEN** it loads the work inbox through the versioned API, filtered by the server to the user's roles

#### Scenario: A modified client skips a check
- **WHEN** a modified client sends an approval that the user's role does not allow
- **THEN** the server refuses it and nothing changes

### Requirement: MA-02 The mobile approver covers approvals, administrative escalations, the day-close signature and alerts
The mobile approver SHALL cover exactly these functions:
- approving, changing or rejecting work-inbox items (change A WI-03);
- working administrative escalations in the user's lane (ER-11);
- the controller's day-close signature of the daily agent digest (AO-10);
- alerts, including the urgent fallback, overdue acknowledgments, expired clocks and hold countdowns, with an acknowledge action for each tracked alert (ER-09).

Its screens SHALL be built for exceptions and approvals (ruling 38). Every other function SHALL stay on the workstation. This scope is a current-phase decision and may be revisited (ruling 35).

#### Scenario: The controller signs the day close on a phone
- **WHEN** the controller opens the daily agent digest on the mobile approver and signs it
- **THEN** the signature is recorded exactly as a workstation signature would be

#### Scenario: A user looks for patient registration
- **WHEN** a user looks for new-patient registration on the mobile approver
- **THEN** the app does not offer it and directs the user to the workstation

### Requirement: MA-03 The physician's clinical work stays in openChart
The physician's clinical work SHALL stay in openChart, on mobile or workstation (ruling 35). The mobile approver MUST NOT show or accept clinical content. A clinical alert SHALL show only the routing id, the urgency, a coded cause, the instruction to open openChart, and an action to acknowledge a tracked alert (ER-09).

#### Scenario: A clinician receives an urgent fallback alert
- **WHEN** the on-call clinician receives an urgent fallback alert on the mobile approver
- **THEN** the alert shows the routing id, the urgency, the cause, "open openChart" and an acknowledge action, and no message content, and the acknowledgment is recorded

#### Scenario: A clinician tries to reply from the approver
- **WHEN** a clinician tries to answer a clinical item from the mobile approver
- **THEN** the app offers no reply path, because clinical replies belong to openChart

### Requirement: MA-04 Notifications carry no PHI, and a deep link opens an item only after a fresh user verification
Push notifications from the mobile approver MUST NOT contain PHI. Their text SHALL be generic, and they SHALL carry only an opaque item reference. On the mobile approver, re-authentication SHALL be a user verification by face, fingerprint or passkey, and never a typed password (ruling 105). Every verification SHALL take place on a device the person has enrolled. A deep link SHALL open the item only after such a verification, unless the last verification in that device session was within the last 60 seconds (ruling 105), measured on the server's clock (design call DC-59, PROPOSED). When the session is absent or expired, the deep link SHALL fail closed to the sign-in screen. The app SHALL log the user off automatically when idle (constitution Principle I).

**Enrolment and recovery (design call DC-60, PROPOSED).** A typed password alone SHALL never enrol a device or recover access. Enrolling a device, or recovering access after losing every enrolled device, SHALL need one of these:
- a verification on another device the person has already enrolled;
- the confirmation of a second named person who holds the practice manager or office manager role, never the person themselves, given in a verified session;
- where the practice has no such second person, ruling 107's one-person alternative: a single-use recovery code issued to the person at their first enrolment and kept offline. It takes effect only 24 hours after it is used, and the notice below lets the person cancel it in that time. Using it retires it, and a new code is issued.

A person's first device is enrolled through the second path. Where the practice has no such second person, it is enrolled with a one-time code sent to a contact point recorded on the person's roster entry before the request, and takes effect after the same 24-hour delay. Every enrolment and recovery SHALL send a notice with no PHI to the person's other enrolled devices and recorded contact points, and SHALL be audited (PR-10).

**What the ruling leaves open.** Whether a passkey that syncs between a person's devices is acceptable, or only one bound to an enrolled device, is Open Question 24. Whether the device's own unlock code or PIN may stand in when a face or fingerprint check is unavailable is Open Question 25. Until the owner answers, a verification counts only when it takes place on an enrolled device and its user check is a face or fingerprint check; a verification whose only check is an unlock code or PIN does not count. A person who cannot complete a face or fingerprint check still receives notifications, cannot open an item or sign on the mobile approver, and works at the workstation; a tracked alert they cannot acknowledge escalates under ER-09.

#### Scenario: An approval is waiting
- **WHEN** a work-inbox item needs a user's approval
- **THEN** the push text is generic, and opening it asks for a face, fingerprint or passkey verification before the item appears

#### Scenario: A second item within a minute
- **WHEN** a user verified 30 seconds ago and opens another deep link
- **THEN** the item opens without a new prompt

#### Scenario: A phished password
- **WHEN** someone who has only a person's password tries to enrol a new phone in that person's name
- **THEN** the enrolment is refused unless it is verified on one of the person's enrolled devices, confirmed by a second named person, or made through the delayed one-person path, and the person's devices and contact points are notified

#### Scenario: Recovery in a one-person practice
- **WHEN** a practice's only administrator loses their phone and uses their recovery code
- **THEN** access returns only after 24 hours, notices go to their recorded contact points, and the code is retired

#### Scenario: No working face or fingerprint check
- **WHEN** a user's face and fingerprint checks fail and only the phone's unlock code is available
- **THEN** the item does not open on the mobile approver, and the user works it at the workstation

#### Scenario: A typed password is offered to open an item
- **WHEN** a user tries to open an item by typing a password
- **THEN** the app does not accept it, and asks for a face, fingerprint or passkey verification

#### Scenario: The session has expired
- **WHEN** a user opens a deep link after the session expired
- **THEN** the app shows the sign-in screen and no item detail

### Requirement: MA-05 No PHI persists on the device beyond the session
The mobile approver MUST NOT persist PHI on the device beyond the session (design call DC-19). It SHALL keep no offline cache, local database, file or screenshot store of PHI. Session data SHALL be cleared at logoff, at idle timeout and on revocation. The app SHALL work online only. When the server is unreachable, it SHALL say so and act on nothing.

#### Scenario: A user logs off
- **WHEN** a user logs off the mobile approver
- **THEN** no PHI remains in the app's storage on the device

#### Scenario: The server is unreachable
- **WHEN** the device loses its connection while an approval screen is open
- **THEN** the app shows that it is offline, keeps no PHI locally, and records no approval

### Requirement: MA-06 A lost device is revoked through same-day offboarding
Each device session SHALL be revocable. The user, the office manager, or same-day offboarding (change A AM-05) SHALL revoke a lost device's sessions and push registration at once. A revoked device SHALL be refused on its next request and SHALL receive no further notification. Every revocation SHALL be audited.

#### Scenario: A phone is reported lost
- **WHEN** a user reports a lost phone and the office manager revokes it
- **THEN** the phone's sessions and push registration end at once, and the revocation is audited

#### Scenario: A revoked device tries again
- **WHEN** a revoked device sends a request
- **THEN** the request is refused and no data is returned

### Requirement: MA-07 Sign-offs on mobile follow the FD-5 tiers and BAB-17, each with its own user verification at signing
A sign-off on the mobile approver SHALL follow the action's FD-5 tier and BAB-17, exactly as on the workstation. Tier-4 actions SHALL be signed by a named human only. The D8 approval and estimator-version approvals SHALL be given by the practice's accountant only. Each mobile sign-off SHALL require its own user verification at the moment of signing, by face, fingerprint or passkey and never a typed password, and a verification made to open the item SHALL NOT stand in for it, however recent (ruling 105). The sign-off's verification SHALL answer a single-use challenge that the server issues for that item and decision, valid for 2 minutes, and SHALL produce an assertion bound to the item, the decision and the time. The server verifies the assertion, records it with the sign-off, and refuses one that is replayed, expired, or made for another item or decision (design calls DC-59 and DC-62, PROPOSED). The sign-off SHALL record the signer, the time, the device-session reference, that assertion and the provenance. The server SHALL refuse a sign-off that the user's role does not allow.

#### Scenario: A practice manager approves a refund on a phone
- **WHEN** a named human with the authority approves a refund leaving the practice on the mobile approver
- **THEN** they verify with face, fingerprint or passkey for the sign-off itself, and the tier-4 sign-off is recorded with their identity, the time, the device-session reference and the assertion bound to that refund, the approval and the time

#### Scenario: A sign-off assertion is replayed
- **WHEN** an assertion from one approved refund is sent again to sign another item
- **THEN** the server refuses it, because its challenge was used and names another item

#### Scenario: A sign-off right after opening the item
- **WHEN** a user opens an item with a fingerprint check and approves it ten seconds later
- **THEN** the app asks for a new verification for the approval, and the opening verification is not used

#### Scenario: A non-accountant tries to approve an estimator version
- **WHEN** a user who is not the practice's accountant tries to approve an estimator version on the mobile approver
- **THEN** the server refuses the approval
