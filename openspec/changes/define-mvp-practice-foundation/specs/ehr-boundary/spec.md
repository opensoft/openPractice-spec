## ADDED Requirements

### Requirement: Each system owns its own patient record and the records are linked
openPractice SHALL own the administrative patient record, and a connected EHR SHALL own its own clinical patient record. The two records SHALL be linked only by cross-references that each side stores. openPractice SHALL store the EHR patient reference as an EHR link on the administrative patient, made of the EHR issuer, an identifier namespace and the value. For openChart the namespace SHALL be `openchart` and the value the openChart patient identifier. The EHR SHALL store the openPractice patient reference under the namespace `openpractice`, with the openPractice deployment's issuer identifier and the openPractice patient identifier. In openChart this is an `OC Patient External Identifier` row. Neither system SHALL replace, merge, deactivate or delete the other system's record.

#### Scenario: A link is established with openChart
- **WHEN** an openPractice patient is linked to an openChart patient
- **THEN** openPractice stores the reference with namespace `openchart`, openChart stores the reference with namespace `openpractice`, and each system's own patient identifier is unchanged

#### Scenario: The EHR reference is never used as the openPractice identifier
- **WHEN** an inbound message identifies a patient only by its EHR reference
- **THEN** openPractice resolves it through the stored EHR link and never adopts the EHR identifier as the administrative patient identifier

### Requirement: Links are created explicitly and existing-patient links are human-confirmed
openPractice SHALL create an EHR link only through an explicit link request under contract `openpractice-ehr-patient-link/1.0`, in one of two modes. In `create_new` mode the EHR creates a new clinical patient from the openPractice demographic snapshot. In `link_existing` mode the link goes to an EHR patient a user selected. A `link_existing` request SHALL require an authorized user's explicit confirmation, made after viewing the openPractice and EHR demographics side by side, as a governance tier-4 hard stop. The system MUST NOT create a link from automatic demographic matching.

#### Scenario: Staff link to an existing EHR patient
- **WHEN** a user selects an existing EHR patient for an openPractice patient
- **THEN** the system shows both records' names, dates of birth and administrative sex side by side and sends the link request only after the user's explicit confirmation

#### Scenario: Demographics match but nobody confirms
- **WHEN** an openPractice patient's name and date of birth exactly match an EHR patient and no user confirms a link
- **THEN** no link is created

### Requirement: Linkage conflicts enter an explicit identity-review state
A link SHALL enter the `identity_review` state, never a merge, when any of the following holds:
- the openPractice reference is already active on a different EHR patient;
- the EHR patient already holds an active openPractice reference from the same issuer with a different value;
- the EHR reports that the demographics in a `link_existing` request do not agree with its record;
- either system reports a conflict through the contract.

While a link is in `identity_review`, openPractice SHALL NOT publish demographic updates for it, and a receiver SHALL NOT apply any. Resolution SHALL be recorded with actor, time, outcome and reason. The outcome SHALL be either confirmation of the link or revocation of the link. Resolution MUST NOT merge, overwrite or delete either patient record.

#### Scenario: Two openPractice patients claim one EHR patient
- **WHEN** a link request names an EHR patient that already holds an active openPractice reference with a different value from the same issuer
- **THEN** the new link enters `identity_review`, no demographics flow on it, and neither record is merged

#### Scenario: Review confirms the link
- **WHEN** an authorized reviewer confirms a link held in `identity_review`
- **THEN** the link becomes active, the confirmation is recorded with actor, time and reason, and openPractice publishes the current demographic snapshot

### Requirement: Administrative demographic changes are published through the versioned link contract
openPractice SHALL be the source of administrative demographic changes for linked patients and SHALL publish each change to every active EHR link as a `demographics.updated` message under contract `openpractice-ehr-patient-link/1.0`. Every message SHALL carry:
- an envelope with the contract name, contract version, a unique message identifier, the message type, the issue time, the issuer, and the openPractice and EHR patient references;
- a per-patient demographic version that strictly increases;
- the list of changed fields;
- an `identity_significant` flag that is true when legal name, date of birth or administrative sex changed;
- the administrative status (`active` or `inactive`);
- provenance: the actor as an opaque reference, the source channel and the reason;
- a full current snapshot, compatible with FHIR R4 Patient, of legal and preferred name, date of birth, administrative sex, gender identity, pronouns, telecom and addresses.

The message MUST NOT include guarantor, coverage, financial, deactivation-reason or internal-note content.

#### Scenario: A patient's address changes
- **WHEN** staff change a linked patient's home address
- **THEN** openPractice publishes a `demographics.updated` message with the next demographic version, `address` in the changed fields, `identity_significant` false, and the full snapshot

#### Scenario: A patient is deactivated
- **WHEN** a linked patient is deactivated in openPractice
- **THEN** the published snapshot carries administrative status `inactive` without the deactivation reason, and the EHR's clinical record is not deactivated by openPractice

### Requirement: Demographic publication is reliable, idempotent and version-ordered
openPractice SHALL persist each `demographics.updated` message in an outbox in the same transaction as the demographic change that caused it, and SHALL deliver it asynchronously. Every retry SHALL reuse the original message identifier. The receiver SHALL acknowledge each message with exactly one result:
- `applied` when the demographic version is greater than the last applied version;
- `duplicate` when the message identifier was already applied;
- `stale` when the demographic version is lower than the last applied version, in which case nothing changes;
- `rejected` when the same version arrives with different content, when the contract major version is unsupported, or when the link is revoked or unknown;
- `identity_review` when the link is under review, or when the receiver holds the update for identity review (for example, a date-of-birth change held by openChart).

openPractice SHALL record every acknowledgement and SHALL show staff any `rejected` or `identity_review` result on the patient record. A message that is still undelivered after the retry policy is exhausted SHALL become work owned by a Roster entry. An EHR outage SHALL NOT block or delay any openPractice registry operation.

#### Scenario: Messages arrive out of order
- **WHEN** the EHR receives demographic version 7 after it has already applied version 8
- **THEN** it acknowledges version 7 as `stale` and keeps version 8's values

#### Scenario: The EHR is unavailable
- **WHEN** the EHR is unreachable for a day while staff update linked patients
- **THEN** registry updates complete normally, the messages wait in the outbox, and delivery resumes with the original message identifiers when the EHR returns

### Requirement: EHR boundary contracts are versioned and evolve additively
The contracts `openpractice-ehr-patient-link` and `openpractice-ehr-clinical-facts` SHALL carry a `MAJOR.MINOR` version, starting at `1.0`. Additive changes SHALL increment MINOR. Any breaking change SHALL require a new MAJOR version and a documented migration path. A receiver SHALL refuse a message with an unsupported MAJOR version, with an explicit error, rather than partially applying it. Each EHR link SHALL record the contract version in use.

#### Scenario: An unsupported major version arrives
- **WHEN** a receiver that supports only `1.x` receives a `2.0` message
- **THEN** it rejects the message with an explicit unsupported-version error and changes nothing

### Requirement: No direct database access across the EHR boundary
openPractice and the EHR SHALL exchange patient links, demographic updates and clinical facts only through the versioned contracts. Neither system SHALL read or write the other system's database or tables, even when both run on the same Frappe site.

#### Scenario: Systems share a Frappe site
- **WHEN** openPractice and openChart are installed on the same Frappe site
- **THEN** openPractice still exchanges patient and clinical data only through the versioned contracts, and no openPractice code reads or writes openChart tables

### Requirement: Clinical facts for charge capture come from the EHR through a FHIR R4-compatible contract
openPractice SHALL obtain the clinical facts it needs for charge capture only from the linked EHR, under contract `openpractice-ehr-clinical-facts/1.0`. The contract consists of pointer-only notifications that carry no clinical content, plus authenticated reads of FHIR R4-compatible resources limited to linked patients and the billing purpose. The resources SHALL be limited to:
- Encounter: identifier, status, type, period, location and participating practitioners;
- Condition, encounter-diagnosis category: code system, code, rank and encounter reference;
- Procedure: code system, code, quantity where documented, performed time, performer and encounter reference;
- a documentation-final indicator with signer reference and signed time.

openPractice MUST NOT request or store clinical note text, medication lists, results or orders through this contract.

#### Scenario: A signed visit becomes billable
- **WHEN** the EHR notifies openPractice that an encounter's documentation is final
- **THEN** openPractice reads the encounter, its encounter diagnoses and its procedures through the contract and receives no note text

### Requirement: Only final clinician documentation is billable
openPractice SHALL treat clinical facts as billable only when the contract marks the encounter's documentation final and identifies a clinician signer. Patient-reported statements MUST NOT be treated as diagnoses or procedures. A visit whose documentation is not final SHALL wait in a held waiting-on-clinical condition. Its clock SHALL be inherited from the payer's filing window, so that the visit stays visible to staff until its documentation is final.

#### Scenario: Documentation is unsigned
- **WHEN** a visit has occurred but its EHR documentation is not final
- **THEN** openPractice creates no charge from it and shows the visit as waiting on clinical documentation, with the filing-window clock

### Requirement: Clinical corrections flow forward
A corrected clinical fact SHALL arrive under the clinical-facts contract as a new version that references the version it supersedes. A retraction SHALL arrive as an explicit entered-in-error signal. openPractice SHALL record each correction or retraction as a dated event citing the EHR as its source. It SHALL drive any corrected or voided claim through its own claim workflow, and it MUST NOT edit the frozen copy of a claim as submitted.

#### Scenario: A diagnosis is corrected after the claim was sent
- **WHEN** the EHR publishes a corrected encounter diagnosis for a visit whose claim was already submitted
- **THEN** openPractice records the correction as a dated event, keeps the submitted claim copy unchanged, and makes a corrected-claim action available

### Requirement: openPractice keeps references and submitted copies, not clinical truth
openPractice SHALL store references to EHR clinical facts and SHALL freeze only the copies that a third party will judge, namely the claim as submitted. The EHR SHALL remain the source of truth for every clinical fact, and openPractice MUST NOT present a stored copy as the current clinical record.

#### Scenario: Clinical fact changes after use
- **WHEN** an EHR diagnosis is later corrected
- **THEN** openPractice shows the original as submitted and the correction separately, and does not overwrite the submitted copy

### Requirement: Charge entry without an EHR is administrative data
When a practice has no linked EHR, openPractice SHALL let authorized staff enter the billing codes for a visit as administrative charge data. Each entry SHALL carry provenance recording that no EHR link supplied it. The system MUST NOT represent this data as a signed clinical record.

#### Scenario: Practice bills from its own superbill
- **WHEN** a practice with no EHR enters a visit's billing codes from its own paper superbill
- **THEN** the charge records its codes with staff-entry provenance and no clinical-record status

### Requirement: Appointment authority is left undecided
This change SHALL NOT assign authority over appointments to either system. Open risk R-7 remains open until the owner decides it. Contract `openpractice-ehr-patient-link/1.0` and contract `openpractice-ehr-clinical-facts/1.0` MUST NOT carry appointment create, update or cancel operations.

#### Scenario: An implementer proposes appointment sync
- **WHEN** a feature plan proposes synchronizing appointments between openPractice and an EHR under the 1.0 contracts
- **THEN** the proposal is out of scope for this change and requires the owner's decision on appointment authority first

### Requirement: Each system operates without the other
openPractice SHALL operate fully with no EHR linked, and the EHR link contracts SHALL be optional for every openPractice deployment. openPractice MUST NOT require openChart. openChart's own operation without openPractice is governed by openChart's ratified `expanded-patient-intake` requirement and by `opensoft/openChart-spec` change `define-openpractice-boundary`.

#### Scenario: Deployment has no EHR
- **WHEN** an openPractice deployment configures no EHR
- **THEN** registration, scheduling, billing and accounting integration operate, and charge capture uses administrative charge entry
