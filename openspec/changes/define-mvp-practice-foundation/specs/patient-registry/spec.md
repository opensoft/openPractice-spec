## ADDED Requirements

### Requirement: openPractice owns the administrative patient record
The system SHALL maintain one administrative patient record per patient of the practice, holding the demographic, contact, guarantor and coverage facts that registration, scheduling, billing and patient communication require. openPractice SHALL be the source of truth for these administrative facts. The record MUST NOT hold signed clinical facts, encounters, diagnoses, medication lists, results or orders. Every patient registry operation SHALL work with no electronic health record (EHR) linked.

#### Scenario: Practice operates without an EHR
- **WHEN** a practice with no EHR link configured creates, edits, deactivates and bills a patient
- **THEN** every registry operation completes without waiting on or requiring an EHR

#### Scenario: Clinical content is offered to the registry
- **WHEN** a user or integration attempts to store a diagnosis, medication list or clinical note on the administrative patient record
- **THEN** the system rejects the content as outside the administrative record and stores none of it

### Requirement: Patient identity fields keep administrative sex, gender identity and pronouns distinct
The record SHALL carry a legal name made of a family name, one or more given names and an optional suffix, an optional preferred name, and a date of birth. A person with a single name SHALL be recordable without an empty placeholder part. A record SHALL NOT be created without a legal name containing at least one name part, and a date of birth. The record SHALL carry **administrative sex** as a required coded attribute with the values `female`, `male`, `other` and `unknown`, aligned with FHIR R4 administrative gender. Administrative sex is the attribute used for payer, eligibility and other administrative transactions. The record SHALL carry **gender identity** and **pronouns** as separate, optional, patient-reported attributes. The system MUST NOT derive gender identity or pronouns from administrative sex, MUST NOT derive administrative sex from gender identity, and MUST NOT send gender identity or pronouns in a payer transaction.

#### Scenario: Gender identity differs from administrative sex
- **WHEN** a patient's administrative sex is recorded as `female` and the patient reports a different gender identity and pronouns
- **THEN** the system stores all three values independently, payer transactions carry only administrative sex, and staff-facing views show the reported gender identity and pronouns

#### Scenario: Optional attributes are not provided
- **WHEN** a patient record is created without gender identity or pronouns
- **THEN** those attributes remain empty and the system infers no value for them

#### Scenario: Required identity fields are missing
- **WHEN** a user attempts to create a record without a date of birth
- **THEN** the system refuses to create the record and names the missing field

### Requirement: Contacts and addresses are typed, preferred and kept as history
The record SHALL carry zero or more telephone numbers, each with a use type and a preferred flag. It SHALL carry zero or more email addresses. It SHALL carry zero or more postal addresses, each with a use type of at least `home` or `mailing` and an effective start date. At most one telephone number and at most one postal address of each use type SHALL be marked preferred at a time. A changed or removed contact or address SHALL be kept as dated history and SHALL NOT be overwritten.

#### Scenario: Patient moves
- **WHEN** staff record a new home address effective from a stated date
- **THEN** the new address becomes current from that date and the prior address remains retrievable with its effective period

#### Scenario: Two preferred mobile numbers are entered
- **WHEN** a user marks a second telephone number of the same use type as preferred
- **THEN** the system clears the preferred flag on the first and records both changes

### Requirement: Each patient has exactly one current guarantor
The system SHALL record exactly one current guarantor for each patient, defaulting to the patient (`self`) at creation. A guarantor who is another person SHALL be recorded in the party register with a legal name, contact facts and the guarantor's relationship to the patient, and that person need not be a patient. A guarantor change SHALL take effect from a stated date and SHALL keep the prior guarantor assignment as history. Recording a guarantor MUST NOT require a household model.

#### Scenario: A parent is guarantor for a minor
- **WHEN** staff record a parent who is not a patient as the guarantor of a minor patient
- **THEN** the parent exists as a party with name, contacts and relationship, and the minor's record references the parent as current guarantor

#### Scenario: Guarantor changes
- **WHEN** a patient's guarantor changes from a parent to self on a stated date
- **THEN** the system makes self the current guarantor from that date and keeps the parent assignment, with its effective period, as history

### Requirement: Patients carry zero or more prioritized, versioned insurance coverages
The system SHALL record zero or more insurance coverages per patient; zero means the patient is self-pay. Each coverage SHALL identify the payer, the member identifier, the group number when one is issued, the subscriber (the patient, or another person recorded with legal name, date of birth and administrative sex), the patient's relationship to the subscriber, an effective start date, an optional effective end date, and a priority. Among coverages whose effective periods overlap, priorities SHALL be unique and ordered primary, secondary, tertiary. A coverage change SHALL create a new coverage version effective from a stated date. Any version that an eligibility check, estimate, claim or statement used SHALL remain retrievable unchanged. Each coverage change SHALL emit a dated coverage-changed event naming the patient and the affected dates, for downstream estimate, statement and claim features to consume.

#### Scenario: Two overlapping coverages share a priority
- **WHEN** a user saves a second coverage as primary while an existing primary coverage overlaps it in time
- **THEN** the system refuses the save and identifies the conflicting coverage

#### Scenario: Coverage used by a claim is later corrected
- **WHEN** a member identifier is corrected after a claim used the earlier coverage version
- **THEN** the system creates a new coverage version and the claim still references the earlier version unchanged

#### Scenario: Coverage ends
- **WHEN** staff record an effective end date on a coverage
- **THEN** the coverage stops applying after that date, its history remains retrievable, and a coverage-changed event is emitted

### Requirement: Search before create shows exact matches and never scores
Before creating a patient record, the system SHALL show every existing record, deactivated records included, whose date of birth and entered legal name parts (the family name and first given name, or the single name) exactly match the entered values. Creating a new record while an exact match is shown SHALL require a stated reason, which is recorded. The system MUST NOT compute probabilistic or weighted match scores and MUST NOT merge or link records automatically.

#### Scenario: An exact match exists
- **WHEN** staff enter a name and date of birth that exactly match a deactivated record
- **THEN** the system shows the deactivated record and requires a stated reason before creating a new one

#### Scenario: A near match exists
- **WHEN** an existing record differs from the entered values only by a one-letter spelling difference in the family name
- **THEN** the system computes no similarity score and takes no merge or link action

### Requirement: Registry operations go through the versioned API layer
The system SHALL expose patient, guarantor, contact, address and coverage create, read, update, deactivate and reactivate operations through openPractice's versioned API layer. Staff-facing and patient-facing clients, Flutter applications included, MUST NOT write registry data through any other path.

#### Scenario: A client bypasses the API layer
- **WHEN** a client attempts to change a patient record outside the versioned API layer
- **THEN** the change is refused and no registry data is altered

### Requirement: Every registry change and every PHI read is attributed and audited
For every creation, change, deactivation, reactivation and hard delete of patient, guarantor, contact, address and coverage facts, the system SHALL record the actor (a Roster entry or a named integration identity), the time, the source channel, the reason where one is required, and the prior and new values. The system SHALL record every read access to a patient record, as constitution Principle I requires for PHI access. Audit records SHALL be kept in the owner's environment, MUST NOT be written to application logs, and MUST NOT be editable by the actors they describe.

#### Scenario: Staff correct a date of birth
- **WHEN** a staff member corrects a patient's date of birth
- **THEN** the audit trail records the staff member, time, channel, prior value and new value

#### Scenario: Staff open a patient record
- **WHEN** a staff member views a patient record
- **THEN** the system records the read access with actor, time and source

### Requirement: Delete means deactivate
A request to delete a patient record SHALL deactivate it. A deactivated record SHALL accept no new appointments, visits or charges. It SHALL remain retrievable with its full history, and its existing threads, claims, balances, coverages, EHR links and audit history SHALL be unchanged. Deactivation SHALL require a coded reason (at least `duplicate`, `no_longer_a_patient`, `deceased` or `other`). Deactivation SHALL be reversible by reactivation, and both acts SHALL be audited.

#### Scenario: Patient with an open balance is deactivated
- **WHEN** staff deactivate a patient who has an open balance
- **THEN** the record accepts no new appointments, and the open balance, its statements and its claims continue unchanged

#### Scenario: Deactivated patient returns
- **WHEN** an authorized user reactivates a deactivated patient
- **THEN** the record accepts new activity again and the deactivation and reactivation both remain in the audit trail

### Requirement: Hard delete is limited to records created in error with no linked activity
The system SHALL permanently remove a patient record's demographic, contact, guarantor and coverage content only when both of the following hold. First, an authorized user confirms, as a governance tier-4 hard stop with a stated reason, that the record was created in error. Second, the system verifies that the record has no linked activity. Linked activity SHALL include any appointment or other thread, charge, claim, statement, payment, credit, refund, eligibility check or other coverage use, active or historical EHR link, accounting customer or posting, and any active legal or audit hold. When any linked activity exists, the system SHALL refuse the hard delete and offer deactivation instead. A completed hard delete SHALL leave a tombstone that holds the record identifier, the actor, the time and the reason, and no other personal content.

#### Scenario: Record created in error has no activity
- **WHEN** an authorized user hard-deletes a record created minutes earlier by mistake, which has no thread, money, coverage use, EHR link or accounting posting
- **THEN** the system removes its personal content and leaves a tombstone with identifier, actor, time and reason

#### Scenario: Record has an EHR link
- **WHEN** an authorized user requests a hard delete of a record that was ever linked to an EHR patient
- **THEN** the system refuses the hard delete and offers deactivation

### Requirement: Hard delete defers to retention and hold controls
While the retention-obligation control from OpenSpec change `add-reimbursement-retention-control` is in force, the system SHALL submit each hard-delete request to that control's disposition gate and SHALL refuse the hard delete unless the gate authorizes deletion for that record's scope. An active legal or audit hold SHALL block a hard delete in every case. The audit records about a hard-deleted record SHALL remain under retention control, and a hard delete MUST NOT remove them.

#### Scenario: Disposition gate cannot resolve applicability
- **WHEN** a hard delete is requested while the disposition gate reports unresolved retention applicability for the record's scope
- **THEN** the system refuses the hard delete and offers deactivation

#### Scenario: Audit trail survives a hard delete
- **WHEN** a hard delete completes
- **THEN** the audit records of the record's creation, reads and changes remain retrievable to authorized reviewers

### Requirement: Registry fixtures and examples are synthetic
Test fixtures, seed data, examples and documentation for the patient registry MUST use synthetic data that is visibly marked as synthetic, and MUST NOT contain real PHI, real member identifiers or real contact details.

#### Scenario: Fixture review
- **WHEN** a reviewer inspects the registry's fixtures and examples
- **THEN** every person, member identifier, address and contact value carries a synthetic marker and none is real
