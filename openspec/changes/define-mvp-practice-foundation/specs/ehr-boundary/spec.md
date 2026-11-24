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

### Requirement: EB-03 Each EHR contract version has one canonical schema identified by digest
The canonical message schemas SHALL be these two files in this change's `contracts/` directory:
- `openpractice-ehr-patient-link-1.0.schema.json`
- `openpractice-ehr-clinical-facts-1.0.schema.json`

Each SHALL be identified by the SHA-256 digest of its bytes. Each peer SHALL select the canonical schema by the exact admitted contract name and wire version (EB-10) and validate the complete message against that schema before applying or retaining its content. A digest mismatch, an unavailable schema, or a field outside the selected schema SHALL be a validation failure. Both owning changes SHALL record matching schema digests before either side uses a version. When this change is archived, the files move to the spec repository's `contracts/` directory with their digests intact.

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

The snapshot's confidential-communication instructions SHALL stay in the snapshot, and the receiving EHR SHALL honor them for every patient communication it originates, including communications from any system it drives (ruling 27; openChart PL-16). Where the record's party belongs to a same-person group (PR-15), the snapshot's restrictions SHALL include every restriction that crosses to that party, as restriction flags only and never another party's channels. After a link of the group ends, they SHALL keep including each restriction that crossed to the party until the end-of-link review decides the party's own, and openPractice SHALL publish the next version when a link of the group is recorded and when an end-of-link review changes them (ruling 150; design call F-6, PROPOSED). A restriction that leaves the EHR no permitted channel for a communication means that the EHR sends nothing by that route. This is a companion expectation for `opensoft/openChart-spec` change `define-openpractice-boundary` (its PL-16), and it binds the EHR only when that change's owner adopts it (design call F-6, PROPOSED). The message MUST NOT include guarantor, coverage, financial, deactivation-reason or internal-note content. Coverage is excluded from contract version 1.0 and may be added only as a later MINOR version.

#### Scenario: A patient's address changes
- **WHEN** staff change a linked patient's home address
- **THEN** openPractice publishes the next demographic version, with `address` in the changed fields and the full snapshot

#### Scenario: A restriction crosses a same-person link
- **WHEN** a record carrying `no_home_mail` is retired as a duplicate of a record linked to the EHR, and their parties stay linked
- **THEN** the linked record's snapshot carries `no_home_mail` but not the other party's alternate address, so the EHR mails nothing to the home address

#### Scenario: A link ends before its review
- **WHEN** a same-person link is ended while a linked record's snapshot carries a crossing `no_home_mail`
- **THEN** the snapshot keeps the flag until the end-of-link review decides the party's own restrictions, and the next version is published when the review changes them

#### Scenario: A crossing restriction leaves the EHR no channel
- **WHEN** the snapshot carries a crossing `no_home_mail` and the record has no alternate address of its own
- **THEN** the snapshot carries no other party's address, and under the companion expectation the EHR sends nothing by mail

#### Scenario: A patient is deactivated
- **WHEN** a linked patient is deactivated in openPractice
- **THEN** the snapshot carries status `inactive` without the reason, and openPractice does not deactivate the EHR's record

### Requirement: EB-07 Publication is reliable, idempotent and version-ordered per link
openPractice SHALL write each `demographics.updated` message to an outbox in the same transaction as the change that caused it, and deliver it asynchronously. Every retry SHALL reuse the original `message_id`. The sender SHALL check the link at dispatch and on retry. Once a terminal transition is recorded, undelivered demographics SHALL be suppressed and no further demographic dispatch authorized for that link. The receiver SHALL compare `demographic_version` with the last version applied on that `link_id`, and SHALL acknowledge with exactly one result:
- `applied` when the version is greater than the last applied version;
- `duplicate` when the `message_id` was already applied, or when the same version arrives with an identical `snapshot` under a new `message_id`, whatever its provenance, because a republish carries a different `provenance.reason` (design call F-24, PROPOSED);
- `stale` when the version is lower, in which case nothing changes;
- `rejected` when the same version arrives with a different `snapshot`, on an admission or validation error under EB-03 and EB-10, or when the link is revoked, superseded or unknown;
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

- A `link.review_resolved` message with outcome `confirmed` can resume delivery only if the authenticated sender is the current resolver under EB-05, the issuer, subject, `link_id` and `review_id` match the outstanding review, and the link is not terminal. The EHR applies only the exact snapshot the reviewer compared and reports its `applied_version`. A newer snapshot the reviewer has not compared is handled only through normal publication and hold rules.
- On the first valid confirmation, openPractice SHALL publish its current snapshot once with reason `republish_after_review` if `applied_version` is absent or lower than its current demographic version. This rule applies whether or not a held update suspended delivery. An identical snapshot already applied under the same version deduplicates under EB-07, regardless of provenance.
- On `demographics.hold_rejected`, openPractice SHALL keep the link suspended, record the rejection and present it as work. Delivery resumes only after an authorized user either corrects the field or re-asserts the value with reason `reassertion_after_hold_rejected` for a fresh review. A confirmation or replay alone cannot lift this suspension.
- A replay, an obsolete or mismatched review or resolver, and a confirmation on a revoked or superseded link SHALL cause no demographic state change or publication. EB-05's revocation precedence applies to both the resolution and its outbox effects. Commit the resolution and any republication outbox entry together, and suppress undelivered demographic entries under EB-07 if the link becomes terminal before dispatch.

#### Scenario: A newer version arrives while a review is open
- **WHEN** the EHR confirms the exact version 3 snapshot the reviewer compared, while openPractice has since recorded version 4
- **THEN** version 3 is applied by the confirmation, openPractice republishes version 4 once with `republish_after_review`, and the EHR handles it through the normal checks, holding a changed date of birth for review

#### Scenario: Two versions are held and only one is compared
- **WHEN** the reviewer compares a held version 5 and confirms it while a newer held version 6 was not compared
- **THEN** the EHR applies only version 5 in the confirmation and reports `applied_version` 5. Version 6 can only apply through its ordinary publication path and its applicable review checks

#### Scenario: A valid confirmation has no applied version
- **WHEN** a matching valid confirmation omits `applied_version` and publication is otherwise allowed
- **THEN** openPractice republishes its current snapshot once and the EHR deduplicates it if that version and snapshot were already applied

#### Scenario: A late, repeated or terminal confirmation arrives
- **WHEN** a confirmation names a completed or different review, is repeated, or arrives after the link was revoked or superseded
- **THEN** it does not resume delivery or enqueue an additional snapshot, and queued undelivered demographics on a terminal link stay suppressed

#### Scenario: A held date of birth is rejected
- **WHEN** the EHR sends `demographics.hold_rejected` and a subsequent confirmation arrives without an authorized correction or re-assertion
- **THEN** openPractice keeps the link suspended and shows the rejection to staff

### Requirement: EB-09 Correction requests from the EHR are received and resolved, never auto-applied
openPractice SHALL accept `demographics.correction_requested` messages from a linked EHR, proposing a change to legal name or date of birth that originated from EHR staff or an intake submission. It SHALL answer each with `correction.ack` result `received`, and present it as work to authorized staff. It MUST NOT apply a correction request automatically.
- An accepted correction SHALL be made as an ordinary openPractice change. It SHALL be published as a new demographic version referencing the request, and reported with `correction.resolved` outcome `applied`.
- A declined request SHALL be reported with `correction.resolved` outcome `declined` and a coded reason.

A correction request that arrives through a link openPractice holds in identity review (EB-05), whether or not that link was ever active, SHALL still be received and answered with `correction.ack` result `received`, but it is kept with that review: its work item SHALL show the link's review state, and nothing SHALL be decided on the request while the review is open: it SHALL be neither accepted, applied nor declined until the review resolves the link as `confirmed`. Until then nobody can say whose record the request concerns, so a decline would be a decision on the wrong person's request as easily as an acceptance would, and `correction.resolved` would travel through a link that is not confirmed. The request's age SHALL count from its receipt, and its work item SHALL show that age; the open review SHALL NOT pause the work item's clock (`define-actors-agents-and-work-inbox` WI-06), so an item whose clock expires during the review is raised as any other is, and still nothing is decided on it until the review resolves the link. If the link ends `revoked` or `superseded` before a request through it is decided, whether or not a review is open, and whether a review resolves it as `revoked`, either side revokes or withdraws it, or it is superseded, the request SHALL be closed unapplied, with the reason recorded in openPractice; no `correction.resolved` is sent for it, because the link has ended and contract 1.0 has no decline reason for that case. Otherwise a correction request through an active link is handled as above (design call F-22, PROPOSED).

#### Scenario: openChart staff report a misspelled name
- **WHEN** openChart sends a correction request for a linked patient's legal name
- **THEN** openPractice acknowledges receipt and creates staff work, and the name changes only if staff accept it, after which the new version is published

#### Scenario: A correction arrives through a link in identity review
- **WHEN** openPractice holds a link in identity review because the EHR reference returned to it is already linked to a different openPractice patient, and a correction request for the legal name arrives through that link
- **THEN** openPractice answers `correction.ack` `received`, the work item shows the link's review state and the request's age from receipt, staff can neither accept nor decline the correction, and when the review resolves the link as `revoked`, as that kind of review must, the request is closed unapplied with the reason recorded and no `correction.resolved` is sent

#### Scenario: A correction waits for a review that confirms the link
- **WHEN** a correction request arrives through a link that was active and is now in identity review, and the review later resolves the link as `confirmed`
- **THEN** staff can accept or decline the request only after that resolution, and from then on it is handled as any request through an active link, its age still counted from its receipt

#### Scenario: A correction held on a link that is superseded during the review
- **WHEN** a correction request is held with an open identity review, and the link ends `superseded` before the review resolves
- **THEN** the request is closed unapplied with the reason recorded in openPractice, no `correction.resolved` is sent, and its work item no longer escalates, because nothing is left to decide on it

#### Scenario: The active link of an undecided correction ends
- **WHEN** a correction request arrived through an active link, and before staff decide it the EHR revokes the link as the wrong patient
- **THEN** the request is closed unapplied with the reason recorded in openPractice, staff can no longer accept or decline it, and no `correction.resolved` is sent

#### Scenario: A correction's clock runs while the review is open
- **WHEN** the work item of a correction request held with an open identity review reaches its due time before the review resolves
- **THEN** the item is raised to the owner's cover and to the controller as any other is, the review not having paused its clock, and still no one can accept or decline the request until the review resolves the link

### Requirement: EB-10 Peers admit exact EHR wire versions and schema digests
The `openpractice-ehr-patient-link` and `openpractice-ehr-clinical-facts` contracts SHALL carry a `MAJOR.MINOR` wire version. Version `1.0` is initially the only admitted version of each contract. For every peer, each side SHALL record the enabled contract name, exact version and verified canonical schema digest. Send only a version with a matching digest explicitly admitted by both peers. The version and digest pairing may be configured for the authenticated peer. A matching MAJOR alone gives no admission.

The receiver SHALL authenticate the peer, select the agreed version and digest before applying or retaining the message content, and validate the whole message under EB-03. It SHALL refuse a structurally valid version marker with an unsupported MAJOR as `unsupported_major_version`. A malformed marker, unadmitted MINOR of a supported MAJOR, missing or mismatched digest pairing, or invalid payload SHALL be refused as `schema_invalid`. Refusals SHALL produce no domain effect or retention of rejected clinical content. An open value in an otherwise valid, admitted version, such as EB-15's `retraction_reason`, follows that field's documented fallback and does not admit any new wire version.

An additive change SHALL introduce a new MINOR and a newly pinned schema. A breaking change SHALL introduce a new MAJOR, a newly pinned schema and a documented migration path. Keep the mutually admitted old version available for continuity until both peers explicitly admit a replacement, including the ability to send the older admitted retraction shape where it conveys the same withdrawal safely.

#### Scenario: An unsupported major version arrives
- **WHEN** a peer sends `2.0` and the receiver supports only MAJOR 1
- **THEN** it rejects the version with `unsupported_major_version` and changes nothing

#### Scenario: An additive version was not admitted for this peer
- **WHEN** a peer admitted only for `1.0` sends `1.1`, with or without any new field
- **THEN** the receiver rejects it as `schema_invalid` and changes nothing

#### Scenario: A peer version lacks matching schema bytes
- **WHEN** the version marker is supported but the authenticated peer has no matching admitted digest or the canonical bytes fail their digest check
- **THEN** neither side uses that pairing, and an incoming message is rejected as `schema_invalid` before domain processing

#### Scenario: A pinned version gains an unrecognized field
- **WHEN** an admitted `1.0` retraction contains an extra envelope or payload field outside its canonical schema
- **THEN** it is rejected as `schema_invalid` without preserving that field or applying the retraction

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
- authenticated reads of FHIR R4-compatible resources, limited to linked patients and the billing purpose. A retraction after a link ends is the one exception to the active-link limit (EB-15, ruling 88).

The reads SHALL be limited to:
- Encounter: identifier, status, type, period, location and participating practitioners;
- Condition, encounter-diagnosis category, for diagnoses whose verification status is `confirmed` only (ruling 87): coding, rank and encounter reference;
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

### Requirement: EB-15 Clinical corrections flow forward, and every retraction carries a reason code that reveals no restricted act
A corrected clinical fact SHALL arrive as a new version referencing the version it supersedes. A retraction SHALL arrive as an explicit `clinical_fact.entered_in_error` notification, the contract's single retraction message, with a coded `retraction_reason` (ruling 89):
- `entered_in_error`: exactly one Condition or Procedure that was itself marked entered in error while its encounter stays published;
- `withdrawn`: every other retraction, with no reason given. That includes a diagnosis refuted or otherwise no longer `confirmed` (ruling 87), one moved to the problem list, a coding the canonical schema cannot hold, a correction delivered as a retraction after its link ended (ruling 88), and every retraction that names an Encounter, whatever its cause, together with each fact retracted with it.

No code varies with the cause of an encounter-level retraction, because a restricted act in the EHR always withdraws the whole encounter. The contract has no code for a restricted, refuted or superseded fact, so nothing in it lets openPractice infer that a restricted act exists (proposed follow-up call F-1). A retraction without a `retraction_reason` means `withdrawn` (F-3).

openPractice SHALL apply a retraction only to facts it received under the same `link_id` and for the same subject that the retraction names. A reference to a fact it received under another link, for another subject, or not at all SHALL change nothing and SHALL raise an integration exception that holds only identifiers. This holds for every retraction, on an active link or an ended one, so a retraction can never withdraw another patient's charges.

openPractice SHALL treat both codes alike. For each retraction it SHALL record a dated event citing the EHR as its source, stop using every referenced fact for any new charge, hold any unfinalized charge built from them as work owned by a Roster entry, and, for a claim already submitted with them, make a corrected-claim or voided-claim action available to staff, who decide which applies (F-4). It SHALL drive any corrected or voided claim through its own claim workflow, and MUST NOT edit the frozen copy of a claim as submitted. For the two defined codes only, it SHALL keep the code as given and show `withdrawn` as "withdrawn by the EHR" with no reason. It MUST NOT infer, record or show a cause the code does not state. An undefined value is never kept: it SHALL appear in no record, log, diagnostic or stored copy of the message, and where openPractice keeps a copy of a received message, `withdrawn` replaces the undefined value before the copy is kept.

`retraction_reason` is an open code: an otherwise valid message in an admitted wire version may carry a schema-valid code outside the two defined values. A code openPractice does not define for that version SHALL be applied as `withdrawn`, never refused or dropped, and the retraction SHALL be recorded exactly as a `withdrawn` one, so nothing marks which retraction carried it (F-2, PROPOSED). Such a message validates against the pinned schema, so openPractice never applies a message that fails it, and EB-03 keeps no exception. Refusing the retraction would leave openPractice billing a fact the EHR has withdrawn, while `withdrawn` has the same billing effect as every other code and reveals nothing. The schema still refuses `entered_in_error` on a retraction that names an Encounter or more than one resource.

**Undefined codes are reported in aggregate (F-2, PROPOSED).** openPractice SHALL raise one integration exception for each period in which it received at least one undefined code. The exception SHALL hold only the period and the number of such retractions: no message, patient, link, encounter or fact identifier, and not the code's value, so it cannot single out the encounters whose retractions carried the code. It SHALL be routed only to the integration operator, the person the practice names to run its EHR integration, and never to a staff work queue, a patient record or a report. It means the publisher is sending codes outside the contract. The operator checks the publisher's use of the admitted code set. A later MINOR, if applicable, may be adopted only by the explicit paired version and digest admission in EB-10. The period is a policy value, PROPOSED as one calendar day.

**Companion expectation.** The publisher, openChart under `opensoft/openChart-spec` change `define-openpractice-boundary`, SHALL send only the two defined codes until a governed MINOR version defines another, and never a code that states a cause. It adopts this when it re-pins to this contract's digest.

**After a link ends (ruling 88).** openPractice SHALL accept and apply, as pointers only, a retraction of a fact it received under a link that has since ended `revoked` or `superseded`. This is the one exception to the active-link limit of EB-13 and of the contract's `link_id` note. openPractice SHALL request no read on such a link, and none is served.

#### Scenario: A diagnosis is corrected after the claim was sent
- **WHEN** the EHR publishes a corrected diagnosis for a visit whose claim was already submitted
- **THEN** openPractice records a dated event, keeps the submitted claim copy unchanged, and makes a corrected-claim action available

#### Scenario: A published diagnosis is refuted
- **WHEN** the EHR retracts a published diagnosis with `retraction_reason` `withdrawn` after a clinician refuted it
- **THEN** openPractice stops using that diagnosis for any new charge, makes a corrected-claim action available for a claim already sent with it, and records no cause beyond "withdrawn by the EHR"

#### Scenario: A whole encounter is withdrawn
- **WHEN** the EHR retracts an Encounter and every published Condition and Procedure on it, with `retraction_reason` `withdrawn`
- **THEN** openPractice applies the same billing effect to each, offers staff a corrected-claim or voided-claim action for any claim already sent, and draws no conclusion about why the encounter was withdrawn

#### Scenario: One procedure is marked entered in error
- **WHEN** the EHR retracts one published Procedure with `retraction_reason` `entered_in_error`, and its encounter stays published
- **THEN** openPractice stops billing that procedure, and the encounter's other facts stay billable

#### Scenario: An encounter-level retraction names a cause
- **WHEN** a retraction that names an Encounter carries `retraction_reason` `entered_in_error`
- **THEN** it fails the canonical schema, because every encounter-level retraction carries `withdrawn`

#### Scenario: A retraction arrives after the link was revoked
- **WHEN** the EHR delivers a retraction of a fact published under a link that has since been revoked
- **THEN** openPractice accepts it as pointers only, applies it, and requests no read on that link (ruling 88)

#### Scenario: An admitted retraction carries an undefined reason code
- **WHEN** a retraction carries a well-formed reason code that openPractice does not define
- **THEN** the message validates, openPractice applies and records it exactly as a `withdrawn` retraction, bills nothing the EHR withdrew, keeps the code's value in no record, log, diagnostic or stored copy, and counts it only in that period's aggregate exception to the integration operator

#### Scenario: A publisher sends an undefined code only when an encounter is withdrawn
- **WHEN** a faulty publisher sends an undefined code, such as one that states a cause, only on some Encounter retractions
- **THEN** each retraction is recorded as `withdrawn` like every other, the period's exception holds only a count, and no record, log or exception shows which encounters carried the code

#### Scenario: A retraction names another link's fact
- **WHEN** a retraction on one `link_id` references a fact openPractice received under another link or for another subject
- **THEN** that reference changes nothing, and an integration exception with identifiers only is raised

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
