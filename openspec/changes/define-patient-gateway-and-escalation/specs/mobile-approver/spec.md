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
Push notifications from the mobile approver MUST NOT contain PHI. Their text SHALL be generic, and they SHALL carry only an opaque item reference. On the mobile approver, re-authentication SHALL be a user verification by face, fingerprint or passkey, and never a typed password (ruling 105); where neither face nor fingerprint is available, the device's own unlock code may stand in (ruling 135). Every verification SHALL take place on a device the person has enrolled. A deep link SHALL open the item only after such a verification, unless the last verification in that device session was within the last 60 seconds (ruling 105), measured on the server's clock (design call DC-59, PROPOSED). When the session is absent or expired, the deep link SHALL fail closed to the sign-in screen. The app SHALL log the user off automatically when idle (constitution Principle I).

**Enrolment and recovery (ruling 137; design call DC-60, PROPOSED).** Ruling 137 sets the main lines: a colleague with their own enrolled phone vouches, and a first phone then works at once while a replacement waits 24 hours and can be cancelled; with no colleague, codes go to two contact details on file, at least one on file for 7 days, with the same wait. Every detail below beyond those words is design call DC-60 (PROPOSED), and the details marked as stricter go further than the ruling's words. A typed password alone SHALL never enrol a device or recover access. A person's first device is the first device ever enrolled for their roster entry, enrolled while the person holds a passkey or security key that is not revoked. Every later enrolment is a replacement while the person has an enrolled device that is not revoked, and a recovery when they have none. An enrolment for a person who holds no credential now, no passkey, security key or enrolled device that is not revoked, is a recovery, even if no device was ever enrolled for them; a person who has never held a credential enrols no device, because their first credential is a passkey or security key, registered at installation or through change A AM-07's invitation (DC-60, PROPOSED). A recovery follows the rules for a replacement. Each enrolment SHALL use one of these paths:
- **Another enrolled device** (DC-60; a path the ruling does not name): a verification on a device the person has already enrolled. It takes effect at once.
- **During initial set-up** (ruling 161): the practice's first phone may be enrolled at once, without ruling 137's wait, until the first phone is enrolled or the practice goes live, whichever comes first. The details are design call DC-69 (PROPOSED), and fail closed. The window covers one enrolment: the first mobile approver device ever enrolled for any roster entry of the practice, and only a holder of the practice manager or office manager role may use it, so that their phone can then vouch for everyone else. The person enrols their own device, with a fresh check made with their own passkey or security key (change A AM-07); it takes effect at once, is audited (PR-10), and is notified with no PHI to the person's contact points and to every other holder of the practice manager or office manager role. Once a device is enrolled for any roster entry, or the practice goes live, the window closes and never reopens, even if that device is later revoked. For this window the practice goes live at the earliest of: the first patient record, the first patient-app feature switched on, the first live accounting connection, or the first agent permission (ruling 206). This change reads each as its earliest act (DC-69, PROPOSED): the creation of the first patient record in the registry (`define-mvp-practice-foundation` PR-01); the practice manager's recording and verifying of the first HealthLinc operator agreement, the act that enables the gateway capabilities for that deployment (PG-15); the first connection openPractice makes to a real accounting destination, which that destination's verification under `define-mvp-practice-foundation` AI-20 needs, not its later go-live, a contract double or disposable synthetic instance (AI-21) not counting; and the making of the first grant to an agent (change A AO-03), not its start date.
- **A second named person** (ruling 137): the confirmation of a second named person, never the person themselves, given with a fresh verification under this requirement on the confirmer's own enrolled device. The confirmer SHALL hold the practice manager or office manager role, which is stricter than the ruling's "a colleague" (DC-60). A manager who removed a colleague's phone or passkey cannot approve the replacement (ruling 198). This change reads removing, how long that bar lasts and whom it covers as follows (DC-60, PROPOSED): a holder of either role who revoked any of the person's passkeys, security keys or devices, or marked any of the person's devices or contact points `stolen` or `compromised`, SHALL NOT confirm that person's enrolment and does not count as such a second person for that person, until, for each kind of credential that holder removed, a replacement has taken effect by a path that needs neither that holder nor any holder the bar covers, as change A AM-01 states, a fresh check never ending it; while that bar holds, it also covers every holder of either role whom the barred holder made, by creating their roster entry, giving them their role, or issuing the invitation for their first passkey or security key or confirming their first device, and every holder made in turn by one it covers (change A AM-01), so the manager who removes a credential is never the one who restores it, nor restores it through a manager of their own making; any other holder of either role may still confirm (DC-60, PROPOSED). A colleague's first phone also needs that colleague's own passkey (ruling 198), so the confirmer's confirmation SHALL NOT by itself enrol a device in another person's name: a first device SHALL be started by the person themself, with a fresh check made with their own passkey or security key (change A AM-07; who starts it is DC-60, PROPOSED, and whether a security key counts as their own passkey here is put to the owner in Open Question 27, recommended yes). A first device takes effect on the confirmation (ruling 137), and every other holder of the practice manager or office manager role is notified of it, with no PHI. A replacement or a recovery takes effect only 24 hours after the confirmation, and can be cancelled in that time as stated below (ruling 137).
- **Where the practice has no such second person** (ruling 137, and ruling 107's one-person alternative): for a recovery, a single-use recovery code issued to the person with their first credential (change A AM-07) and kept offline, which takes effect only 24 hours after it is used; using it retires it, and a new code is issued (DC-60; a path the ruling does not name). A person who no longer holds an enrolled device that is not revoked, such as a stolen phone, MAY revoke it and mark it `stolen` or `compromised` in the same act as using their recovery code, the code standing as the check; the enrolment then proceeds as a recovery, with its 24-hour wait and notices (DC-60, PROPOSED). For a first device, or for a recovery when the person's recovery code is unusable: a one-time code sent to each of two contact points recorded on the person's roster entry (change `define-actors-agents-and-work-inbox` AM-01), with both codes entered; the enrolment takes effect only 24 hours after the codes are used, and can be cancelled in that time as stated below (ruling 137). For a first device, this 24-hour wait reads ruling 137's "with the same wait" as the replacement's wait; the words could also mean the colleague path's timing, under which a first phone works at once, so for a first device the wait is DC-60's reading (PROPOSED). Both contact points SHALL qualify by age, which is stricter than the ruling's "at least one on file for 7 days" (DC-60). A contact point qualifies by age when at least 7 days have passed since the person confirmed it with a fresh check made with one of their own credentials (change A AM-01), so a contact point someone else recorded on the person's roster entry never qualifies until the person confirms it; this is stricter than the ruling's "on file for 7 days" (DC-60, PROPOSED). While the person holds a usable recovery code, a recovery uses that code and not contact-point codes, which is also stricter than the ruling's words (DC-60). A recovery code is unusable only once the person declares it lost; the declaration retires it, is audited, and is notified like an enrolment. A person with no credential left MAY declare the code lost in the same act as entering both contact-point codes; the declaration takes effect only with that enrolment, is notified to every contact point and device, and the 24-hour wait and cancellation apply (DC-60, PROPOSED).

Every enrolment, replacement and recovery SHALL send a notice with no PHI to every enrolled device of the person that is not revoked and to every contact point recorded on the roster entry, and SHALL be audited (PR-10). For an enrolment that waits 24 hours, the notice SHALL also go to every device of the person revoked, and every contact point removed or replaced, in the last 30 days, except a device revoked or a contact point removed with the marking `stolen` or `compromised`, which receives no further notice from the moment of the marking; the marking is notified to every other contact point and enrolled device of the person; each notice SHALL carry a link that can only cancel the enrolment, and any recipient MAY cancel it within the 24 hours. A cancelled enrolment never takes effect; the cancellation is audited, notified in the same way, and raises an exception with no PHI for the practice manager or office manager.

Where no path is available, because the practice has no such second person, the person has no enrolled device and no usable recovery code, and no contact point qualifies yet, the person SHALL have no mobile approver access until one qualifies, and works at the workstation if they still hold a passkey or security key (fail closed); with no credential left at all, change A AM-07 states what they can do, including its last-resort recovery for a person who can use none of these paths, which ruling 191 allows. Whoever can sign in as the practice manager can edit the roster and its contact points, so these rules rest on the workstation sign-in, which change A AM-07 sets under ruling 136: every human signs in with two steps, so a stolen password alone signs no one in, and each sign-off needs its own fresh check.

**Which verifications count (rulings 134 and 135).** A verification counts when it takes place on a device the person has enrolled and the user check was a face check, a fingerprint check, or, where neither is available, the device's own unlock code (ruling 135). A passkey verification counts on a device the person has enrolled, including a passkey that syncs across the person's devices through their platform account, and never on a device they have not enrolled (ruling 134). Because a passkey does not report which user check the device applied, a passkey verification on an enrolled device counts whichever of those checks the device used. Where the app can choose the check, it SHALL ask for face or fingerprint first (design call DC-59, PROPOSED). A typed account password never counts (ruling 105). A person whose enrolled device can complete none of these checks works at the workstation, and a tracked alert they cannot acknowledge escalates under ER-09.

#### Scenario: An approval is waiting
- **WHEN** a work-inbox item needs a user's approval
- **THEN** the push text is generic, and opening it asks for a face, fingerprint or passkey verification before the item appears

#### Scenario: A second item within a minute
- **WHEN** a user verified 30 seconds ago and opens another deep link
- **THEN** the item opens without a new prompt

#### Scenario: A phished password
- **WHEN** someone who has only a person's password tries to enrol a new phone in that person's name
- **THEN** the enrolment is refused unless it is verified on one of the person's enrolled devices, confirmed by a second named person, or made through a one-person path; the person's enrolled devices and every contact point are notified, and an enrolment that waits 24 hours can be cancelled from any notice; the password alone does not sign them in at the workstation either (change A AM-07)

#### Scenario: Recovery in a one-person practice
- **WHEN** a practice's only administrator loses their phone and uses their recovery code
- **THEN** access returns only after 24 hours; a notice with a link that can only cancel goes to every recorded contact point and to the lost phone, even if it was revoked in the last 30 days; any recipient may cancel within the 24 hours; and the code is retired and a new one issued

#### Scenario: A contact point is swapped
- **WHEN** someone who has taken over a one-person practice's practice-manager sign-in replaces a contact point on that person's roster entry, and requests one-time codes the same day
- **THEN** no codes are sent, because both contact points must qualify by age and the new one has not been confirmed by the person, let alone 7 days ago; the contact point replaced, every other contact point recorded before the change and the person's enrolled devices are notified of the change

#### Scenario: A stolen phone is marked
- **WHEN** a person revokes a stolen phone and marks it `stolen`
- **THEN** the marking needs the person's fresh check under change A AM-07; the phone then receives no further notice, so it cannot cancel the person's replacement, and the marking is notified to every other contact point and enrolled device of the person and to every other holder of the practice manager or office manager role

#### Scenario: A stolen phone that is not yet revoked, in a one-person practice
- **WHEN** the only administrator's phone is stolen and not revoked, and they use their recovery code
- **THEN** in the same act they revoke the phone and mark it `stolen`, the code standing as the check; the enrolment proceeds as a recovery with its 24-hour wait and notices, and the stolen phone receives none

#### Scenario: The manager who revoked a phone cannot vouch for its replacement
- **WHEN** the office manager revoked a person's phone and marked it `stolen`, no replacement has taken effect by another path since, and the office manager then confirms a new phone for that person
- **THEN** the confirmation is refused; another practice manager or office manager whom the office manager did not make may confirm it, and where there is none, the person enrols with their recovery code, or with codes to two contact points they confirmed at least 7 days ago, each taking effect after 24 hours

#### Scenario: Someone in an open session marks a person's devices
- **WHEN** someone in a session left open tries to mark the person's enrolled devices and contact points `stolen` to silence their cancel links
- **THEN** each marking is refused without the person's fresh check bound to it

#### Scenario: A phone is revoked and the code declared lost by someone who took over the sign-in
- **WHEN** someone who has taken over a person's sign-in revokes that person's phone, declares their recovery code lost, and asks for contact-point codes
- **THEN** the revocation and the declaration are notified to every contact point, the revoked phone still receives the 24-hour notice with its cancel link, and nothing is enrolled without both codes

#### Scenario: A made-up roster entry is confirmed as a first device
- **WHEN** someone signed in as the practice manager creates a roster entry and confirms its first device as the second named person, with no verification on the practice manager's own enrolled device
- **THEN** the confirmation is refused; a confirmation that does verify on that device is notified to every other practice manager or office manager

#### Scenario: A new practice-manager entry replaces the real one
- **WHEN** someone signed in as the practice manager creates a practice-manager entry with two contact points of their own and offboards the real practice manager
- **THEN** neither contact point qualifies by age for 7 days, so no one-person enrolment is possible for that entry before then; giving the entry the practice manager recipe is notified to the existing practice manager and office manager holders, and offboarding the real practice manager revokes their devices, which is notified to every contact point on their roster entry

#### Scenario: A new practice with no colleague, after it has gone live
- **WHEN** the only administrator of a practice that has gone live, with no phone enrolled for anyone, asks for contact-point codes to enrol a first phone three days after confirming both contact points with their own fresh check at installation
- **THEN** no codes are sent until 7 days after that confirmation; until then the administrator has no mobile approver access and works at the workstation, signing in under change A AM-07

#### Scenario: The first phone during initial set-up
- **WHEN** the only administrator of a practice installed that day, who holds the practice manager role, with no patient record, no gateway capability enabled, no accounting destination live, no agent granted any scope and no device enrolled for anyone, enrols their own phone with a fresh check made with the passkey registered at installation
- **THEN** the phone takes effect at once, the enrolment is audited, and a notice with no PHI goes to the administrator's contact points and to every other holder of the practice manager or office manager role

#### Scenario: The set-up window does not reopen
- **WHEN** the phone enrolled during set-up is revoked before the practice goes live, and the administrator enrols another phone
- **THEN** the set-up window does not apply again; the new phone is a recovery under the paths above, with their waits

#### Scenario: An employee cannot use the set-up window
- **WHEN** during set-up, with no device enrolled for anyone, a front-desk employee who holds a passkey tries to enrol their own phone under the set-up window
- **THEN** it is refused under that window, because only a holder of the practice manager or office manager role may use it; once the practice manager enrols their phone under the window, they confirm the employee's first phone, which the employee starts and which takes effect at once

#### Scenario: The first patient record closes the set-up window
- **WHEN** a practice still in set-up, with no device enrolled for anyone, creates its first patient record, even a synthetic test patient, and its administrator then tries to enrol a phone at once under the set-up window
- **THEN** the enrolment is refused under that window, because the practice has gone live (ruling 206), and the phone is enrolled only through the other paths, with their waits

#### Scenario: A manager tries to enrol a first phone in a colleague's name
- **WHEN** a sole practice manager confirms a first phone in the name of a colleague who holds a passkey and has never enrolled a device, and the colleague has not started the enrolment
- **THEN** the enrolment is refused, because a first device is started by the person themself with a fresh check made with their own passkey or security key; once the colleague starts it so, the manager's confirmation makes it take effect at once

#### Scenario: A person whose only credential was revoked
- **WHEN** a practice manager revoked the only passkey of a person who never had a phone, and another practice manager confirms a phone in that person's name
- **THEN** it is a recovery, not a first device: it takes effect only after 24 hours, with notices and a cancel link to every contact point on file

#### Scenario: Someone cancels a waiting enrolment
- **WHEN** a recipient of a notice cancels an enrolment that is waiting its 24 hours
- **THEN** the enrolment never takes effect, and the practice manager or office manager owns an exception that holds no PHI

#### Scenario: A second person approves a replacement phone
- **WHEN** a caller says a person lost their phone, and the office manager confirms a new phone for that person
- **THEN** the new phone takes effect only after 24 hours, notices go to the person's other enrolled devices and every contact point, and any recipient may cancel

#### Scenario: No enrolment path is available
- **WHEN** a one-person practice's practice manager has no enrolled device, no usable recovery code and no contact point that qualifies
- **THEN** they get no mobile approver access until a contact point qualifies, and work at the workstation

#### Scenario: No working face or fingerprint check
- **WHEN** face and fingerprint checks are unavailable on a user's enrolled phone, and the phone's unlock code is available
- **THEN** the item opens after the user enters the phone's unlock code on that phone

#### Scenario: A synced passkey on a device that is not enrolled
- **WHEN** a person's passkey, synced through their platform account, is used on a tablet the person has not enrolled
- **THEN** the verification does not count, and the item does not open

#### Scenario: A synced passkey on an enrolled phone
- **WHEN** the person uses the same synced passkey on their enrolled phone
- **THEN** the verification counts, whichever user check the phone applied

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
Each device session SHALL be revocable. The user, the office manager, or same-day offboarding (change A AM-05) SHALL revoke a lost device's sessions at once, and its push registration for everything except MA-04's security notices. A revoked device SHALL be refused on its next request and SHALL receive no further notification, except MA-04's security notices, which carry no PHI, for 30 days after its revocation (design call DC-60, PROPOSED). A device revoked with the marking `stolen` or `compromised` receives no notice at all from that moment, and the marking is notified to every other contact point and enrolled device of the person. Applying the marking SHALL need a fresh check under change A AM-07 bound to that marking, and SHALL be notified with no PHI to every other holder of the practice manager or office manager role; who may apply it for a person with no credential left is change A AM-01's rule (design call DC-60, PROPOSED). Every revocation SHALL be audited, and notified with no PHI to every contact point on the person's roster entry.

#### Scenario: A phone is reported lost
- **WHEN** a user reports a lost phone and the office manager revokes it
- **THEN** the phone's sessions end at once, its push registration ends for everything but MA-04's security notices, the revocation is audited, and every contact point on the person's roster entry is notified

#### Scenario: A stolen phone reported by telephone
- **WHEN** a person telephones the office manager to report that their only phone was stolen
- **THEN** the office manager revokes it; the person, who now has no credential left, may have it marked `stolen` by the office manager with the office manager's own fresh check (change A AM-01); that office manager cannot then confirm the person's new phone, until the person holds a credential obtained another way, so the person enrols one with their recovery code, or with codes to two contact points they confirmed, each after 24 hours, or another manager whom that office manager did not make confirms it

#### Scenario: A revoked device tries again
- **WHEN** a revoked device sends a request
- **THEN** the request is refused and no data is returned

### Requirement: MA-07 Sign-offs on mobile follow the FD-5 tiers and BAB-17, each with its own user verification at signing
A sign-off on the mobile approver SHALL follow the action's FD-5 tier and BAB-17, exactly as on the workstation. Tier-4 actions SHALL be signed by a named human only. The D8 approval and estimator-version approvals SHALL be given by the practice's accountant only, the outside-accountant roster entry of change A AM-01 (ruling 141). Each mobile sign-off SHALL require its own user verification at the moment of signing, by face, fingerprint or passkey, or the device's unlock code where neither face nor fingerprint is available, as MA-04 sets out which verifications count, and never a typed password, and a verification made to open the item SHALL NOT stand in for it, however recent (ruling 105). The sign-off's verification SHALL answer a single-use challenge that the server issues for that item and decision, valid for 2 minutes, and SHALL produce an assertion bound to the item, the decision and the time. The server verifies the assertion, records it with the sign-off, and refuses one that is replayed, expired, or made for another item or decision (design calls DC-59 and DC-62, PROPOSED). The sign-off SHALL record the signer, the time, the device-session reference, that assertion and the provenance. The server SHALL refuse a sign-off that the user's role does not allow.

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
