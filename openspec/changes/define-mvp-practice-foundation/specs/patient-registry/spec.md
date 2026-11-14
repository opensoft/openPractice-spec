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
