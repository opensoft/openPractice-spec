## ADDED Requirements

### Requirement: EB-01 Each system owns its own patient record and the records are linked
openPractice SHALL own the administrative patient record, and a connected EHR SHALL own its clinical patient record. The two records SHALL be linked only by cross-references that each side stores.
- openPractice SHALL store the EHR patient reference as the triple (EHR issuer, namespace, value). For openChart, the namespace is `openchart` and the value is the openChart patient identifier.
- The EHR SHALL store the openPractice reference as the triple (openPractice issuer, `openpractice`, openPractice patient identifier). In openChart this triple is an `OC Patient External Identifier` row.

Neither system SHALL replace, merge, deactivate or delete the other system's record.

#### Scenario: A link is established with openChart
- **WHEN** an openPractice patient is linked to an openChart patient
- **THEN** openPractice stores (openChart issuer, `openchart`, openChart patient identifier), openChart stores (openPractice issuer, `openpractice`, openPractice patient identifier), and each system's own identifier is unchanged

### Requirement: EB-02 Every deployment has a configured issuer and references compare as triples
Each openPractice deployment, and each EHR deployment that links to openPractice, SHALL have a configured, stable issuer identifier. The identifier SHALL be unique across deployments and never derived from a host path. Every patient reference exchanged under the EHR contracts SHALL be the triple (issuer, namespace, value), and SHALL be compared as a triple, never by value alone.

#### Scenario: Two openChart sites reuse a local identifier
- **WHEN** two openChart deployments each have a patient `OC-PAT-00001` and both are linked to one openPractice deployment
- **THEN** openPractice holds two distinct links, distinguished by their openChart issuers, and never treats them as the same patient

### Requirement: EB-03 Each EHR contract has one canonical schema identified by digest
The canonical message schemas SHALL be these two files in this change's `contracts/` directory:
- `openpractice-ehr-patient-link-1.0.schema.json`
- `openpractice-ehr-clinical-facts-1.0.schema.json`

Each SHALL be identified by the SHA-256 digest of its bytes. Both sides SHALL validate every message against the schema with the recorded digest. Any schema change SHALL produce a new digest, recorded in both this change and the companion openChart change before either side implements it. When this change is archived, the files move to the spec repository's `contracts/` directory, and their digests do not change.

#### Scenario: A message violates the schema
- **WHEN** a receiver gets a message that fails validation against the canonical schema
- **THEN** it answers `rejected` with reason `schema_invalid` and changes nothing

### Requirement: EB-04 Links are created explicitly; linking to an existing patient needs explicit human confirmation
openPractice SHALL create an EHR link only through an explicit `link.request` under contract `openpractice-ehr-patient-link/1.0`. Each request SHALL carry a `link_id` that openPractice mints and that identifies the link for its whole life. A request runs in one of two modes:
- `create_new`: the EHR creates a new clinical patient from the demographic snapshot;
- `link_existing`: the link goes to an EHR patient a user selected.

A `link_existing` request SHALL require an explicit confirmation at governance tier 2, made after both records' demographics are compared side by side. The signer SHALL be an authorized named human or, where `link_existing` confirmation is opted in, a named AI agent identified by an openWallet ID (BAB-17). The confirmation SHALL be recorded with the signer's identity. That explicit confirmation is the approval. The system MUST NOT create a link from automatic demographic matching.

#### Scenario: Staff link to an existing EHR patient
- **WHEN** a user selects an existing EHR patient for an openPractice patient
- **THEN** both records' names, dates of birth and administrative sex are shown side by side, and the request is sent only after the user's explicit confirmation

#### Scenario: Demographics match but nobody confirms
- **WHEN** an openPractice patient's name and date of birth exactly match an EHR patient and no user confirms
- **THEN** no link is created

### Requirement: EB-05 Linkage conflicts enter identity review with a single resolver
A link SHALL enter identity review, never a merge, when any of the following holds:
- the openPractice reference is already active on a different EHR patient;
- the EHR patient already holds an active `openpractice` reference from the same issuer with a different value;
- the EHR reports that `link_existing` demographics disagree with its record;
- the EHR reports an empty date of birth on its record;
- the EHR holds an update for review;
- openPractice finds that an EHR reference returned to it is already linked to a different openPractice patient.

Each review SHALL have exactly one resolver, the side that raised it. Only that side SHALL send `link.review_resolved`, with outcome `confirmed` or `revoked`. Two kinds of review SHALL NOT resolve as `confirmed`, because each would leave two openPractice patients on one EHR patient: a review the EHR raises because the EHR patient already holds a different active `openpractice` reference from the same issuer, and its mirror, a review openPractice raises because an EHR reference returned to it is already linked to a different openPractice patient. A revocation of the same `link_id` by either side SHALL win over any concurrent or later confirmation. While a review is open, no demographic update SHALL be applied on that link. Resolution MUST NOT merge, overwrite or delete either patient record.

#### Scenario: Two openPractice patients claim one EHR patient
- **WHEN** a link request targets an EHR patient that already holds an active `openpractice` reference with a different value from the same issuer
- **THEN** the new link enters review, the EHR resolves it, it cannot resolve as `confirmed`, and no record is merged

#### Scenario: openPractice finds the returned EHR reference already linked
- **WHEN** a `link.result` returns an EHR reference that is already linked to a different openPractice patient
- **THEN** openPractice raises the review, resolves it itself, cannot resolve it as `confirmed`, and no record is merged

#### Scenario: A revocation crosses a confirmation
- **WHEN** openPractice revokes a link while the EHR is confirming the same link's review
- **THEN** the link ends revoked on both sides

### Requirement: EB-06 Administrative demographic changes are published through the versioned link contract
openPractice SHALL be the source of administrative demographic changes for linked patients. It SHALL publish each change to every active link as a `demographics.updated` message under contract `openpractice-ehr-patient-link/1.0`. Each message SHALL carry:
- the envelope defined by the canonical schema, including `link_id`;
- a `demographic_version` that strictly increases per openPractice patient record;
- the list of changed fields;
- an `identity_significant` flag;
- provenance made of an opaque actor reference, a source, and a reason from the schema's coded list. Free-text reasons MUST NOT be sent;
- a full snapshot: legal and preferred name, date of birth, administrative sex, gender identity, pronouns, preferred language, telecom, addresses, confidential-communication instructions, and administrative status.

The snapshot's confidential-communication instructions SHALL stay in the snapshot, and the receiving EHR SHALL honor them for every patient communication it originates, including communications from any system it drives (ruling 27; openChart PL-16). The message MUST NOT include guarantor, coverage, financial, deactivation-reason or internal-note content. Coverage is excluded from contract version 1.0 and may be added only as a later MINOR version.

#### Scenario: A patient's address changes
- **WHEN** staff change a linked patient's home address
- **THEN** openPractice publishes the next demographic version, with `address` in the changed fields and the full snapshot

#### Scenario: A patient is deactivated
- **WHEN** a linked patient is deactivated in openPractice
- **THEN** the snapshot carries status `inactive` without the reason, and openPractice does not deactivate the EHR's record

### Requirement: EB-07 Publication is reliable, idempotent and version-ordered per link
openPractice SHALL write each `demographics.updated` message to an outbox in the same transaction as the change that caused it, and deliver it asynchronously. Every retry SHALL reuse the original `message_id`. The receiver SHALL compare `demographic_version` with the last version applied on that `link_id`, and SHALL acknowledge with exactly one result:
- `applied` when the version is greater than the last applied version;
- `duplicate` when the `message_id` was already applied, or when the same version arrives with identical content under a new `message_id`;
- `stale` when the version is lower, in which case nothing changes;
- `rejected` when the same version arrives with different content, when the MAJOR version is unsupported, or when the link is revoked, superseded or unknown;
- `identity_review` when the link is in review or the update is held.

openPractice SHALL record every acknowledgement and SHALL show staff any `rejected` or `identity_review` result on the patient record. An undelivered message SHALL become work owned by a Roster entry once the retry policy is exhausted. An EHR outage SHALL NOT block or delay any openPractice registry operation.

#### Scenario: Messages arrive out of order
- **WHEN** the EHR receives version 7 after it has applied version 8 on the same link
- **THEN** it answers `stale` and keeps version 8's values

#### Scenario: A republish after review
- **WHEN** openPractice republishes the current version under a new `message_id`, and the receiver has already applied that version with identical content
- **THEN** the receiver answers `duplicate`

### Requirement: EB-08 A held update suspends publication on its link until it is resolved
When the receiver acknowledges an update as `identity_review` because it is holding that update, openPractice SHALL suspend demographic delivery on that link. It SHALL keep recording changes and versions locally while suspended.
- On `link.review_resolved` with outcome `confirmed`, the receiver will have applied the latest held snapshot. openPractice SHALL resume, publishing the current snapshot if it is newer than the applied version.
- On `demographics.hold_rejected`, openPractice SHALL keep the link suspended, record the rejection, and present it as work. Delivery resumes only after an authorized user either corrects the field, which publishes a new version, or re-asserts the value with reason `reassertion_after_hold_rejected`, which publishes the current snapshot for a fresh review.

#### Scenario: Two versions are held and then confirmed
- **WHEN** the EHR holds version 5, which changes the date of birth, and also receives version 6 before the review ends, and the reviewer then confirms
- **THEN** the EHR applies version 6, openPractice resumes delivery, and no deadlock occurs

#### Scenario: A held date of birth is rejected
- **WHEN** the EHR sends `demographics.hold_rejected` for held versions 5 and 6
- **THEN** openPractice keeps the link suspended and shows the rejection to staff, and delivery resumes only after a correction or an explicit re-assertion

### Requirement: EB-09 Correction requests from the EHR are received and resolved, never auto-applied
openPractice SHALL accept `demographics.correction_requested` messages from a linked EHR, proposing a change to legal name or date of birth that originated from EHR staff or an intake submission. It SHALL answer each with `correction.ack` result `received`, and present it as work to authorized staff. It MUST NOT apply a correction request automatically.
- An accepted correction SHALL be made as an ordinary openPractice change. It SHALL be published as a new demographic version referencing the request, and reported with `correction.resolved` outcome `applied`.
- A declined request SHALL be reported with `correction.resolved` outcome `declined` and a coded reason.

#### Scenario: openChart staff report a misspelled name
- **WHEN** openChart sends a correction request for a linked patient's legal name
- **THEN** openPractice acknowledges receipt and creates staff work, and the name changes only if staff accept it, after which the new version is published

### Requirement: EB-10 The EHR contracts are versioned and compatible within a MAJOR version
The contracts `openpractice-ehr-patient-link` and `openpractice-ehr-clinical-facts` SHALL carry a `MAJOR.MINOR` version, starting at `1.0`. Implementations are compatible exactly when they share the same MAJOR version. Additive changes SHALL increment MINOR. A breaking change SHALL require a new MAJOR version, a new schema digest, and a documented migration path. A receiver SHALL refuse a message whose MAJOR version it does not support, with reason `unsupported_major_version`, rather than partially applying it.

#### Scenario: An unsupported major version arrives
- **WHEN** a receiver that supports only `1.x` receives a `2.0` message
- **THEN** it rejects the message with `unsupported_major_version` and changes nothing

### Requirement: EB-11 No direct database access across the EHR boundary
openPractice and the EHR SHALL exchange links, demographic messages and clinical facts only through the versioned contracts. Neither system SHALL read or write the other's database or tables, even when both run on the same Frappe site.

#### Scenario: Systems share a Frappe site
- **WHEN** openPractice and openChart share a Frappe site
- **THEN** openPractice exchanges data with openChart only through the versioned contracts and never touches openChart tables

### Requirement: EB-12 No PHI in logs, errors, diagnostics or rejection reasons
Logs, error messages, diagnostic bundles and every coded reason exchanged under the EHR contracts MUST NOT contain names, dates of birth, contact details, addresses, identifiers' values or clinical content. Rejections and review reasons SHALL use only the coded values of the canonical schema. Diagnostics SHALL reference messages by `message_id` and `link_id` only.

#### Scenario: A message is rejected
- **WHEN** a demographic message fails validation
- **THEN** the error log and the `rejected` acknowledgement carry the `message_id`, `link_id` and a coded reason, and no demographic value

### Requirement: EB-13 Clinical facts for charge capture come from the EHR through a FHIR R4-compatible contract
openPractice SHALL obtain the clinical facts it needs for charge capture only from the linked EHR, under contract `openpractice-ehr-clinical-facts/1.0`, in three forms:
- pointer-only notifications, which carry no clinical content;
- a capability result stating whether the EHR supports billable facts;
- authenticated reads of FHIR R4-compatible resources, limited to linked patients and the billing purpose.

The reads SHALL be limited to:
- Encounter: identifier, status, type, period, location and participating practitioners;
- Condition, encounter-diagnosis category: coding, rank and encounter reference;
- Procedure: coding, quantity where documented, performed time, performer and encounter reference;
- a documentation-final indicator with signer reference and signed time.

Codings SHALL carry a code system and a code, never descriptor text. openPractice MUST NOT request or store note text, medication lists, results or orders through this contract.

#### Scenario: A signed visit becomes billable
- **WHEN** the EHR notifies openPractice that an encounter's documentation is final
- **THEN** openPractice reads the encounter, its diagnoses and procedures through the contract and receives no note text

### Requirement: EB-14 Only final clinician documentation is billable
openPractice SHALL treat clinical facts as billable only when the contract marks the encounter's documentation final and names a clinician signer. Patient-reported statements MUST NOT be treated as diagnoses or procedures. A visit whose documentation is not final SHALL remain visible to staff as waiting on clinical documentation, with a clock counting down to the payer's filing deadline.

#### Scenario: Documentation is unsigned
- **WHEN** a visit occurred but its documentation is not final
- **THEN** openPractice creates no charge from EHR facts and shows the visit as waiting on clinical documentation, with the filing-deadline clock

### Requirement: EB-15 Clinical corrections flow forward
A corrected clinical fact SHALL arrive as a new version referencing the version it supersedes, and a retraction SHALL arrive as an explicit entered-in-error notification. openPractice SHALL record each one as a dated event citing the EHR as its source. It SHALL drive any corrected or voided claim through its own claim workflow, and MUST NOT edit the frozen copy of a claim as submitted.

#### Scenario: A diagnosis is corrected after the claim was sent
- **WHEN** the EHR publishes a corrected diagnosis for a visit whose claim was already submitted
- **THEN** openPractice records a dated event, keeps the submitted claim copy unchanged, and makes a corrected-claim action available

### Requirement: EB-16 openPractice keeps references and submitted copies, not clinical truth
openPractice SHALL store references to EHR clinical facts, and SHALL freeze only the copies that a third party will judge, namely the claim as submitted. The EHR SHALL remain the source of truth for every clinical fact. openPractice MUST NOT present a stored copy as the current clinical record.

#### Scenario: A clinical fact changes after use
- **WHEN** an EHR diagnosis is later corrected
- **THEN** openPractice shows the original as submitted and the correction separately, and overwrites neither

### Requirement: EB-17 Manual charge entry is allowed without EHR facts and is marked manual
openPractice SHALL let authorized staff enter a visit's billing codes manually in three cases:
- the practice has no linked EHR;
- the patient is linked to an EHR whose capability result reports no billable-facts support;
- the patient's link is in identity review and the visit has reached the filing threshold of EB-18.

In the third case, a signer under BAB-17, meaning a named human or, where that action type is opted in, a named AI agent identified by an openWallet ID, SHALL first record an attestation of two facts: the patient's identity for the visit was verified, and the visit's clinical documentation is final and signed by a clinician. The attestation SHALL carry provenance: the attesting actor, the time, a coded basis for the identity verification, the `link_id` under review, and the visit. EB-14 still applies, so codes MAY be entered only for documentation the attestation states is final and signed. When the signer does not attest to both facts, the system SHALL refuse the manual entry, keep the visit waiting, and raise work owned by a Roster entry. An attestation SHALL NOT confirm or change the link.

Every manual entry SHALL be marked manual and SHALL carry provenance: the actor, the time, and why EHR facts were unavailable. The system MUST NOT represent it as a signed clinical record. When the linked EHR later reports billable-facts support, new visits SHALL use EHR facts, and existing manual entries SHALL remain marked manual.

#### Scenario: Linked EHR cannot yet supply facts
- **WHEN** a patient is linked to openChart and openChart's capability result reports no billable-facts support
- **THEN** staff may enter the visit's codes manually, and the charge is marked manual, with provenance naming that reason

#### Scenario: Practice has no EHR
- **WHEN** a practice with no EHR enters codes from its own superbill
- **THEN** the charge is marked manual, with staff-entry provenance and no clinical-record status

#### Scenario: Manual entry under identity review, with attestation
- **WHEN** a visit's link is in identity review at the filing threshold, and a staff member attests that the patient's identity was verified and that the documentation is final and signed
- **THEN** the codes may be entered manually, the charge is marked manual, the attestation is recorded with its provenance, and the link stays in review

#### Scenario: The attestation is refused
- **WHEN** a staff member at the filing threshold cannot attest that the documentation is final and signed, or that the patient's identity was verified
- **THEN** the manual entry is refused, no claim is created, the visit stays waiting, and the work is raised to a Roster entry

### Requirement: EB-18 Gates that could stall a claim carry the FD-5 deadline escape
Every gate in this capability SHALL name its deadline escape, as FD-5 requires. If a link held in identity review would stall billing of a visit past the payer's filing threshold, then at that threshold the claim SHALL be released from the identity gate:
- staff are notified;
- staff may enter the charge manually under EB-17's third case, after the attestation it requires;
- the claim enters the outbound claim batch at tier 3, delay plus cancel window.

The link itself MUST NOT be confirmed automatically. Demographic holds and link creation stall no claim, because claims use openPractice's own demographics, so they need no escape.

#### Scenario: Review would cost the filing window
- **WHEN** a visit's facts are unavailable because its link is in identity review, and the filing deadline reaches the threshold
- **THEN** staff are notified, the visit may be charged manually after the EB-17 attestation and claimed at tier 3, and the link stays in review

### Requirement: EB-19 openPractice owns the administrative appointment; the EHR owns the encounter
openPractice SHALL be the system of record for the administrative appointment (ruling 31, closing R-7). The EHR SHALL own the clinical encounter and MAY keep a reference to the openPractice appointment. The 1.0 versions of both EHR contracts MUST NOT carry appointment create, update or cancel operations; appointment events SHALL be added only as a later MINOR version.

#### Scenario: Appointment sync is proposed
- **WHEN** a feature plan proposes exchanging appointment events under the 1.0 contracts
- **THEN** it is out of scope for 1.0, openPractice remains the appointment's system of record, and the exchange waits for a governed MINOR version

#### Scenario: A walk-in visit has no appointment
- **WHEN** the EHR documents an encounter that has no openPractice appointment
- **THEN** the encounter stays the EHR's record, and billing proceeds from the clinical facts without requiring an appointment

### Requirement: EB-20 Each system operates without the other
openPractice SHALL operate fully with no EHR linked, and the EHR contracts SHALL be optional for every deployment. openPractice MUST NOT require openChart. openChart's own operation without openPractice is governed by openChart's ratified `expanded-patient-intake` requirement and by `opensoft/openChart-spec` change `define-openpractice-boundary`.

#### Scenario: Deployment has no EHR
- **WHEN** an openPractice deployment configures no EHR
- **THEN** registration, scheduling, billing and accounting integration operate, and charges are entered manually
