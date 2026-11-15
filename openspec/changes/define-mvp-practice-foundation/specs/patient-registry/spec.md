## ADDED Requirements

### Requirement: PR-01 openPractice owns the administrative patient record
The system SHALL maintain one administrative patient record per patient of the practice, holding the demographic, contact, confidential-communication, guarantor and coverage facts that registration, scheduling, billing and patient communication require. openPractice SHALL be the source of truth for these administrative facts. The record MUST NOT hold signed clinical facts, encounters, diagnoses, medication lists, results or orders. Every patient registry operation SHALL work with no electronic health record (EHR) linked.

#### Scenario: Practice operates without an EHR
- **WHEN** a practice with no EHR link configured creates, edits, deactivates and bills a patient
- **THEN** every registry operation completes without waiting on or requiring an EHR

#### Scenario: Clinical content is offered to the registry
- **WHEN** a user or integration attempts to store a diagnosis, medication list or clinical note on the administrative patient record
- **THEN** the system rejects the content as outside the administrative record and stores none of it

### Requirement: PR-02 Identity fields keep administrative sex, gender identity and pronouns distinct
The record SHALL carry a legal name made of a family name, one or more given names and an optional suffix, plus a date of birth. A person with a single name SHALL be recordable without an empty placeholder part. A record SHALL NOT be created without a legal name containing at least one name part and a date of birth.

The record SHALL carry **administrative sex** as a required coded attribute with the values `female`, `male`, `other` and `unknown`, aligned with FHIR R4 administrative gender. Administrative sex is used for payer, eligibility and other administrative transactions.

The record SHALL carry **gender identity** and **pronouns** as separate, optional, patient-reported attributes. The system MUST NOT derive gender identity or pronouns from administrative sex, MUST NOT derive administrative sex from gender identity, and MUST NOT send gender identity or pronouns in a payer transaction.

#### Scenario: Gender identity differs from administrative sex
- **WHEN** a patient's administrative sex is `female` and the patient reports a different gender identity and pronouns
- **THEN** the system stores all three independently, payer transactions carry only administrative sex, and staff views show the reported gender identity and pronouns

#### Scenario: Optional attributes are not provided
- **WHEN** a record is created without gender identity or pronouns
- **THEN** those attributes stay empty and nothing is inferred for them

#### Scenario: A single-name patient registers
- **WHEN** staff register a patient who has only one name
- **THEN** the record holds that one name with no placeholder and the record is created

### Requirement: PR-03 Preferred name and preferred language are carried; clinical sex and orientation are not
The record SHALL carry an optional preferred name, shown to staff, and an optional preferred language, recorded as a BCP 47 language tag. The registry SHALL NOT carry sex assigned at birth or sexual orientation. Those are clinical attributes owned by the EHR.

#### Scenario: Patient prefers Spanish
- **WHEN** staff record a patient's preferred language as Spanish
- **THEN** the record stores the BCP 47 tag and patient-facing communication can use it

#### Scenario: Someone tries to record sexual orientation
- **WHEN** a user tries to add sexual orientation to the administrative record
- **THEN** the registry offers no such field and stores nothing

### Requirement: PR-04 Contacts and addresses are typed, preferred and kept as history
The record SHALL carry zero or more telephone numbers, each with a use type and a preferred flag; zero or more email addresses; and zero or more postal addresses, each with a use type of at least `home` or `mailing` and an effective start date. At most one telephone number and at most one postal address of each use type SHALL be marked preferred at a time. A changed or removed contact or address SHALL be kept as dated history and SHALL NOT be overwritten.

#### Scenario: Patient moves
- **WHEN** staff record a new home address effective from a stated date
- **THEN** the new address becomes current from that date and the prior one remains retrievable with its effective period

#### Scenario: A second number is marked preferred
- **WHEN** a user marks a second telephone number of the same use type as preferred
- **THEN** the system clears the preferred flag on the first and records both changes

### Requirement: PR-05 Confidential-communication instructions are recorded and honored
The record SHALL carry confidential-communication instructions, kept as dated history:
- an optional alternate postal address;
- optional alternate telephone numbers or email addresses;
- restriction flags from this coded list: `no_voicemail`, `no_home_mail`, `no_text_messages`, `no_email` and `alternate_channel_only`.

Every openPractice function that contacts a patient or guarantor, including statements, reminders and messages, SHALL honor the instructions. It MUST NOT send to a channel the instructions restrict.

#### Scenario: Patient asks for statements at an alternate address
- **WHEN** a patient records an alternate mailing address and the flag `no_home_mail`
- **THEN** statements and letters go only to the alternate address, and nothing is mailed to the home address

#### Scenario: No-voicemail flag is set
- **WHEN** an automated reminder would leave a voicemail for a patient flagged `no_voicemail`
- **THEN** the reminder is not left as a voicemail

### Requirement: PR-06 Each patient has exactly one current guarantor
The system SHALL record exactly one current guarantor for each patient, defaulting to the patient (`self`) at creation. A guarantor who is another person SHALL be recorded in the party register with a legal name, contact facts and their relationship to the patient. That person need not be a patient. A guarantor change SHALL take effect from a stated date and SHALL keep the prior assignment as history. Recording a guarantor MUST NOT require a household model. Each guarantor SHALL have an opaque guarantor account number, which keys that guarantor's balances in every accounting destination. The guarantor's name and billing address or contact SHALL reach a destination only in `customer-detail` mode, behind that destination's BAA gate, with the recipient-profile version recorded on the batch (accounting-integration AI-10).

#### Scenario: A parent is guarantor for a minor
- **WHEN** staff record a parent who is not a patient as the guarantor of a minor
- **THEN** the parent exists as a party with name, contacts, relationship and a guarantor account number, and the minor's record references the parent as current guarantor

#### Scenario: Guarantor changes
- **WHEN** a patient's guarantor changes from a parent to self on a stated date
- **THEN** self becomes current from that date, and the parent assignment and its period remain as history

### Requirement: PR-07 Patients carry zero or more prioritized, versioned coverages
The system SHALL record zero or more insurance coverages per patient; zero means the patient is self-pay. Each coverage SHALL identify:
- the payer;
- the member identifier;
- the group number, when one is issued;
- the subscriber: the patient, or another person recorded with legal name, date of birth and administrative sex;
- the patient's relationship to the subscriber;
- an effective start date, an optional effective end date, and a priority.

Among coverages whose effective periods overlap, priorities SHALL be unique and ordered primary, secondary, tertiary. A coverage change SHALL create a new version effective from a stated date. Any version that an eligibility check, estimate, claim or statement used SHALL remain retrievable unchanged. Each coverage change SHALL emit a dated coverage-changed event naming the patient and the affected dates, for downstream estimate, statement and claim features to consume.

#### Scenario: Two overlapping coverages share a priority
- **WHEN** a user saves a second primary coverage that overlaps an existing primary
- **THEN** the save is refused and the conflicting coverage is identified

#### Scenario: Coverage used by a claim is corrected
- **WHEN** a member identifier is corrected after a claim used the earlier version
- **THEN** a new version is created and the claim still references the earlier version unchanged

#### Scenario: Self-pay patient
- **WHEN** a patient with no insurance is registered
- **THEN** the record is complete with zero coverages and no coverage is required

### Requirement: PR-08 Search before create shows exact matches and never scores
Before creating a patient record, the system SHALL show every existing record, deactivated records included, that exactly matches the entered date of birth and the entered legal name parts. The name parts compared are the family name and first given name, or the single name. Creating a new record while an exact match is shown SHALL require a coded reason, which is recorded. The system MUST NOT compute probabilistic or weighted match scores and MUST NOT merge or link records automatically.

#### Scenario: An exact match exists
- **WHEN** staff enter a name and date of birth that exactly match a deactivated record
- **THEN** the system shows the deactivated record and requires a coded reason before creating a new one

#### Scenario: A near match exists
- **WHEN** an existing record differs only by a one-letter spelling difference in the family name
- **THEN** the system computes no similarity score and takes no merge or link action

### Requirement: PR-09 Registry operations go through the versioned API layer
The system SHALL expose create, read, update, deactivate and reactivate operations for patients, guarantors, contacts, addresses, confidential-communication instructions and coverages through openPractice's versioned API layer. Staff-facing and patient-facing clients, Flutter applications included, MUST NOT write registry data through any other path.

#### Scenario: A client bypasses the API layer
- **WHEN** a client attempts to change a patient record outside the versioned API layer
- **THEN** the change is refused and no registry data is altered

### Requirement: PR-10 Every registry change and every PHI read is audited in a tamper-evident trail
For every creation, change, deactivation, reactivation and hard delete of registry facts, the system SHALL record:
- the actor (a Roster entry or a named integration identity);
- the time and source channel;
- a coded reason where one is required;
- the prior and new values.

The system SHALL record every read access to a patient record, as constitution Principle I requires. Audit records SHALL be kept owner-local, MUST NOT be written to application logs, MUST NOT be editable or deletable by anyone, and SHALL be tamper-evident. The only sanctioned removal is the PHI-value removal of PR-13. That removal SHALL leave verifiable evidence that a redaction occurred, recording by whom, when and why. Verifying the trail SHALL flag every audit entry whose value is missing without a matching redaction event, as well as every alteration.

#### Scenario: Staff correct a date of birth
- **WHEN** a staff member corrects a patient's date of birth
- **THEN** the audit trail records the actor, time, channel, coded reason, and the prior and new values

#### Scenario: Someone tries to alter an audit record
- **WHEN** any user, administrator included, attempts to edit or delete an audit record
- **THEN** the attempt is refused, and any alteration made outside the application is detectable by verifying the trail

#### Scenario: An audit value disappears without a redaction
- **WHEN** verification finds an audit entry whose value is gone and no redaction event records its removal
- **THEN** verification flags the entry as an integrity failure

### Requirement: PR-11 Delete means deactivate, and services already given remain chargeable
A request to delete a patient record SHALL deactivate it from a stated deactivation date. A deactivated record SHALL refuse new activity (visits, services or charges) dated after the deactivation date. Activity dated on or before that date, including services already given, SHALL remain chargeable and billable. The record SHALL remain retrievable with its full history. Its existing threads, claims, balances, coverages, EHR links and audit history SHALL be unchanged. Deactivation SHALL require a coded reason (at least `duplicate`, `no_longer_a_patient`, `deceased` or `other`). Deactivation SHALL be reversible by reactivation, and both acts SHALL be audited.

#### Scenario: A deceased patient's last visit is charged after deactivation
- **WHEN** a patient seen on Monday is deactivated on Wednesday with reason `deceased`, and Monday's visit is charged on Thursday
- **THEN** the charge for Monday's service is accepted and billed, and no activity dated after Wednesday is accepted

#### Scenario: Patient with an open balance is deactivated
- **WHEN** staff deactivate a patient who has an open balance
- **THEN** the balance, its statements and its claims continue unchanged

#### Scenario: Deactivated patient returns
- **WHEN** an authorized user reactivates the record
- **THEN** it accepts new activity again, and both acts remain in the audit trail

### Requirement: PR-12 Hard delete applies only to records created in error with no linked activity
A record created in error with no linked activity SHALL be treated as outside every retention record family. Its hard delete SHALL be a governance tier-4 hard stop: an authorized user confirms, with a coded reason, that the record was created in error. Before deleting, the system SHALL verify that the record has no linked activity. Linked activity SHALL include:
- any appointment or other thread;
- any charge, claim, statement, payment, credit or refund;
- any eligibility check or other coverage use;
- any active or historical EHR link;
- any accounting customer or posting;
- any active legal or audit hold.

When any linked activity exists, the system SHALL refuse the hard delete and offer deactivation instead.

#### Scenario: Record created in error has no activity
- **WHEN** an authorized user confirms the hard delete of a record created minutes earlier by mistake, which has no linked activity
- **THEN** the deletion proceeds as PR-13 defines

#### Scenario: Record has an EHR link
- **WHEN** a hard delete is requested for a record that was ever linked to an EHR patient
- **THEN** the system refuses and offers deactivation

### Requirement: PR-13 Hard delete has an exactly stated removal scope
A completed hard delete SHALL remove two things:
- the record's demographic, contact, confidential-communication, guarantor and coverage content from live data;
- the PHI values held in that record's audit trail.

It SHALL leave a PHI-free tombstone audit event holding the record identifier, the actor, the time and the coded reason. Copies inside backups SHALL age out under the backup retention schedule and are not removed by the hard delete. The system and its documentation MUST NOT describe a hard delete as permanent or complete beyond this stated scope.

#### Scenario: Audit values are removed and the tombstone remains
- **WHEN** a hard delete completes
- **THEN** live data and audit entries for the record hold no PHI values, a tombstone audit event records identifier, actor, time and reason, and verifying the audit trail shows the sanctioned redaction

#### Scenario: A user asks whether the data is gone forever
- **WHEN** a user views the confirmation of a completed hard delete
- **THEN** it states that live data and audit values were removed and that backup copies age out on the backup schedule

### Requirement: PR-14 Registry fixtures and examples are synthetic
Test fixtures, seed data, examples and documentation for the patient registry MUST use synthetic data that is visibly marked as synthetic. They MUST NOT contain real PHI, real member identifiers or real contact details.

#### Scenario: Fixture review
- **WHEN** a reviewer inspects the registry's fixtures and examples
- **THEN** every person, identifier, address and contact value carries a synthetic marker and none is real

### Requirement: PR-15 One party per real person or organization; every other identity is a role or a link
openPractice SHALL keep one party record per real person or organization in its party register (ruling 32, adopting framework D1; this requirement's wording is spec-critique correction SC-2, design call F-6, PROPOSED). Everything else SHALL be a role of a party or a link between parties, never a second person record:
- the administrative patient record of PR-01 is the patient role of a party;
- a guarantor who is another person (PR-06), a subscriber who is not the patient (PR-07), a person named as a contact, and a representative (change `define-patient-gateway-and-escalation` PG-06) are roles of, or links to, a party;
- a person who later becomes a patient, such as a guarantor parent, keeps the same party and gains the patient role;
- a payer and the practice are parties of type organization.

Search before create (PR-08) SHALL also show every party that exactly matches and holds another role, so that an existing party gains the patient role instead of a second record. Because a guarantor, contact, representative or customer may be recorded without a date of birth, the search SHALL also show every party whose name parts match exactly and that has no date of birth on record, marked as having none. For a party shown because it holds another role or has no date of birth, the result SHALL show only its name, its date of birth if any, and the kinds of role it holds, never the patients it is linked to, the links themselves or any balance, because customer-role-boundaries restricts the visibility of sensitive links. Giving an existing party the patient role SHALL require the verified matching evidence and attributable authority that customer-role-boundaries requires for a link, recorded with the person who confirmed it; a matching name, or a matching name and date of birth, is not evidence on its own. Each human roster entry SHALL reference exactly one party (change `define-actors-agents-and-work-inbox` AM-01). An AI agent and a named integration identity SHALL NOT be parties. The commerce customer role SHALL follow ruling 32: it applies only to membership and retail buyers, and a medical-billing accounting customer is derived from the guarantor or payer role (change `define-customer-financial-data-boundaries`, customer-role-boundaries). Table and DocType names are set in planning.

Sharing one party SHALL NOT merge what belongs to a role or a link. Communication authority that is specific to a relationship, effective dates, provenance and visibility stay with the role or link they belong to, and authority held through one role grants nothing through another (customer-role-boundaries). Confidential-communication instructions (PR-05) are the exception, because they protect the person: a restriction or alternate channel recorded through any role SHALL apply to every contact openPractice makes to that party, whatever the role, unless the person explicitly records, as a dated PR-05 instruction, that a named role is excepted. A role's own contact details never count as such an exception, and where two instructions conflict, the more restrictive applies. Reading a party that holds the patient role, through its guarantor, staff or any other role, is a read of a patient record and is audited under PR-10. Deactivation (PR-11) and hard delete (PR-12, PR-13) act on the patient role: a party that still holds another role or link keeps it, and its shared identity facts are not removed while any role or link remains. A deactivation with reason `deceased` SHALL raise review work for every other role and link the party holds, and SHALL change none of them automatically. A hard delete of a patient role created in error, on a party that holds another role or link, SHALL remove the patient role and the content only it uses, such as its coverages and its guarantor assignment, and SHALL keep the legal name, contact facts and confidential-communication instructions the other role or link still uses; PR-13's confirmation SHALL say which facts were kept and why. The party is openPractice's own record. It is never shared with, or matched to, an identity in HealthLinc or the EHR, which link to it only through their own link contracts (ruling 39; EB-01; PG-05). Finding that one person has two party records SHALL NOT merge them automatically (PR-08): it raises review work, and a merge is a tier-4 action signed by a named human (BAB-17). A merge SHALL keep one party, and where only one of the two holds a patient role, the kept party is that one. The other party SHALL be closed as merged into the kept party: it keeps its identifier and takes no new role or link, and every role and reference that pointed at it, such as a minor's current guarantor, a subscriber or a roster entry, SHALL move to the kept party with its history; each guarantor account keeps its history, with its balances unmoved. The exception is a second patient role: it is resolved as a PR-11 deactivation with reason `duplicate`, and it stays on the closed party together with its EHR and gateway links, which never move on their own. EB-05 cannot confirm a second openPractice patient on one EHR patient, so before the kept record's `link_existing` request to the same EHR patient is sent, a named human SHALL revoke the duplicate's EHR link with `link.revoked` and reason `duplicate_record`, as part of the merge; the kept record's request is then confirmed under EB-04, and any other conflict it meets enters identity review under EB-05. Facts already received under the revoked link, and the charges and claims built from them, stay with the duplicate's record (PR-11), and a later retraction of them still arrives under that link as pointers only and is applied there (EB-15, ruling 88). Once the kept record's link is active, openPractice SHALL request again under it the facts it could not receive or apply on the duplicate's link: those for services dated after the duplicate's deactivation, which PR-11 refuses there, and those whose notifications fell between the revocation and the new link. Until then staff MAY enter charges manually (EB-17), and nothing is lost (design call F-6, PROPOSED). Linking the patient's HealthLinc account to the kept record is likewise a new `link.request` under PG-05, made after verified identity and a named human's confirmation; once it is linked, a named human revokes the link on the duplicate under PG-07. Until then, the patient's app is served on the old link as a link to an inactive record (PG-04): urgent escalation, billing contact and own-records access stay granted, other capabilities are withheld with reason `registry_inactive`, and non-urgent clinical messaging and medical-AI consults are withheld only after the patient has been told and the approved grace period has passed (ruling 50). The merge is audited under PR-10 with both parties' identifiers (design call F-6, PROPOSED).

**A staff member who is also a patient, guarantor, subscriber or representative.** One person may hold several roles (ruling 107): the roster entry and the other role reference the same party, and every sign-off records the role used. These actions on a record where the actor's own party holds a role SHALL be owned and decided by another human roster entry that holds the grant, or by an agent whose sponsor is not the actor (design call F-7, PROPOSED): a write-off, adjustment, discount, refund or balance transfer on a balance the person owes or guarantees; a change to their own coverage, guarantor assignment or deactivation; and any approval or sign-off on that record. Routine work, such as booking an appointment for oneself or one's child, recording a changed address, or reading the record, is not caught. Ruling 107 asks a rule that needs two people to name a one-person alternative. The alternative F-7 names under ruling 107 is this: where, at the moment of the act, the roster holds no other human roster entry that holds the grant and no agent holding the grant whose sponsor is not the actor, the person may act; the action records the role used and that the actor's own party holds a role on the record; and openPractice sends a notice of the action, with no clinical content, to the practice's accountant, which stays open until the accountant records that it was reviewed. While no roster entry other than the actor holds the practice's accountant capacity, the alternative is unavailable and the act waits, as the fail-closed rule of `define-practice-readiness` SR-03 requires; how an accountant from outside the practice holds a roster entry is open with the owner as decision 141. Which rule wins when a sole staff member decides a proposal they made as a patient, this alternative or the rule of change `define-actors-agents-and-work-inbox` WI-03 that the decider is never the proposer, is open with the owner as decision 144; this requirement picks no side. Each use is recorded as a use of a one-person alternative, as `define-practice-readiness` SR-03 requires. An accountant's or counsel's sign-off SHALL NOT be self-approved (ruling 107).

#### Scenario: A guarantor later becomes a patient
- **WHEN** staff register as a patient a parent who is already recorded as a guarantor, with no date of birth on record
- **THEN** search before create shows the existing party, marked as having no date of birth, staff record verified matching evidence, the party gains the patient role and a date of birth, no second person record is created, and the guarantor history is unchanged

#### Scenario: A name and birth date match the wrong person
- **WHEN** an exact match on name and date of birth is shown, but the evidence does not confirm that it is the same person
- **THEN** the existing party does not gain the patient role, and staff create a new record with a coded reason (PR-08)

#### Scenario: A patient's mail restriction reaches her guarantor role
- **WHEN** a patient records `no_home_mail` and an alternate address, and she is also the guarantor for her child
- **THEN** the child's statement goes to her alternate address, and nothing is mailed to her home address

#### Scenario: A staff member's own balance
- **WHEN** a front-desk staff member who is also a patient of the practice starts a write-off on their own balance, and another human roster entry holds the grant
- **THEN** the write-off is routed to that roster entry to decide, and the staff member's attempt is recorded with the role used

#### Scenario: The staff member sponsors an agent with the grant
- **WHEN** the only other holder of the write-off grant is an agent the staff member sponsors
- **THEN** the agent cannot decide it, and the write-off goes to another human roster entry, or to the one-person alternative if the roster holds none

#### Scenario: No accountant is recorded
- **WHEN** a staff member in a one-person practice starts a refund on their own balance, and no roster entry other than theirs holds the practice's accountant capacity
- **THEN** the one-person alternative is unavailable, and the refund waits until another eligible person or an accountant is on the roster

#### Scenario: A staff parent books their child's appointment
- **WHEN** a staff member books an appointment for their own child
- **THEN** the booking proceeds as routine work and needs no one else

#### Scenario: An AI agent joins the roster
- **WHEN** an AI agent is added to the roster
- **THEN** its roster entry references no party, and no party record is created for it

#### Scenario: One person is found twice
- **WHEN** staff find that a guarantor's party and a patient's party are the same person
- **THEN** review work is raised, and the two are merged only by a named human's tier-4 sign-off; the patient's party is kept, the guarantor party is closed as merged, and every guarantee and account it held moves to the kept party with its history

#### Scenario: A merged duplicate was linked to the EHR
- **WHEN** patient records P1 and P2 are merged, P1 is kept, and P2 is linked to EHR patient E
- **THEN** P2's patient role is deactivated as `duplicate`, a named human revokes P2's EHR link with reason `duplicate_record` before P1's `link_existing` request to E is sent, so E holds no other active `openpractice` reference and the request can be confirmed; facts P2 received stay with P2's charges, and a later retraction of them is applied there; and once P1's link is active, the facts for visits after P2's deactivation are requested again under it, so the merged patient's EHR-fed charges resume

#### Scenario: A patient record is deactivated while its party is a guarantor
- **WHEN** a patient's record is deactivated with reason `no_longer_a_patient`, and the same party is the guarantor of a minor's balance
- **THEN** the patient role is deactivated, and the guarantor role, its contact facts and its balances continue unchanged

#### Scenario: A guarantor dies
- **WHEN** a patient's record is deactivated with reason `deceased`, and the same party is the current guarantor of a minor
- **THEN** review work is raised for the guarantor role and every other role and link the party holds, and none of them changes until a person acts
