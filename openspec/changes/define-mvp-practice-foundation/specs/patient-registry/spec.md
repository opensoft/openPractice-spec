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
A request to delete a patient record SHALL deactivate it from a stated deactivation date. A deactivated record SHALL refuse new activity (visits, services or charges) dated after the deactivation date. Activity dated on or before that date, including services already given, SHALL remain chargeable and billable. The record SHALL remain retrievable with its full history. Its existing threads, claims, balances, coverages, EHR links and audit history SHALL be unchanged. Deactivation SHALL require a coded reason (at least `duplicate`, `no_longer_a_patient`, `deceased` or `other`). Deactivation SHALL be reversible by reactivation, and both acts SHALL be audited. Deactivation with reason `duplicate` SHALL use PR-15's governed operation. A generic lifecycle operation, import, or direct write SHALL refuse that reason; all permitted paths use the same PR-15 checks.

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
- any active legal or audit hold;
- any active or historical same-person link involving the role's party, or any open identity or duplicate review pertaining to the role.

When linked activity exists or the system cannot establish its absence, the system SHALL refuse the hard delete. It MAY offer a separately authorized deactivation. A configured source that fails to answer, returns an indeterminate result, or cannot account for prior links or holds SHALL leave deletion blocked. Hard-delete eligibility and removal SHALL serialize against relevant link, activity and hold writes. Recheck eligibility at commit or use an equivalent authoritative guard. Return success only after patient-role removal, audit redaction and tombstone evidence agree.

#### Scenario: Record created in error has no activity
- **WHEN** an authorized user confirms the hard delete of a record created minutes earlier by mistake, which has no linked activity
- **THEN** the deletion proceeds as PR-13 defines

#### Scenario: Record has an EHR link
- **WHEN** a hard delete is requested for a record that was ever linked to an EHR patient
- **THEN** the system refuses and offers deactivation

#### Scenario: Linked activity races hard deletion
- **WHEN** a qualifying link, activity or hold is added before a pending hard deletion commits
- **THEN** deletion is refused and the patient's role and audit values remain, with the refusal attributable in the audit trail

### Requirement: PR-13 Hard delete has an exactly stated removal scope
A completed hard delete SHALL remove the erroneous patient role, data used only by it, and its audit PHI values. If another party role or link independently uses an identity fact, contact or communication instruction, the system SHALL retain that fact under the other role's permissions and provenance. Shared facts SHALL NOT be exposed by the deleted role or reconstructed in its audit trail. The role's guarantor assignment and coverage content are patient-role data. Independently required audit records for the other role keep their own access and retention rules.

It SHALL leave a PHI-free tombstone audit event holding the record identifier, the actor, the time and the coded reason. Copies inside backups SHALL age out under the backup retention schedule and are not removed by the hard delete. The system and its documentation MUST NOT describe a hard delete as permanent or complete beyond this stated scope.

#### Scenario: Audit values are removed and the tombstone remains
- **WHEN** a hard delete completes
- **THEN** live data and audit entries for the record hold no PHI values, a tombstone audit event records identifier, actor, time and reason, and verifying the audit trail shows the sanctioned redaction

#### Scenario: A user asks whether the data is gone forever
- **WHEN** a user views the confirmation of a completed hard delete
- **THEN** it states that patient-role live data and audit PHI values were removed, identifies categories of shared facts kept for another role and why, and states that backup copies age out on the backup schedule

#### Scenario: Eligible erroneous patient role shares data with a guarantor role
- **WHEN** an authorized signer hard deletes an erroneous patient role of a party that also has a guarantor role, and all PR-12 checks are clear
- **THEN** the patient-only assignment, coverages and audit PHI values are removed, the other role retains its own history and required shared facts under separate authority, the patient tombstone is verifiable, and confirmation describes the categories retained without exposing their values

#### Scenario: An identity or activity check is indeterminate
- **WHEN** the patient role has a historical same-person link, an open identity or duplicate review, or a configured source cannot establish the absence of linked activity
- **THEN** hard deletion is refused and the refusal is audited without exposing inaccessible linked records

### Requirement: PR-14 Registry fixtures and examples are synthetic
Test fixtures, seed data, examples and documentation for the patient registry MUST use synthetic data that is visibly marked as synthetic. They MUST NOT contain real PHI, real member identifiers or real contact details.

#### Scenario: Fixture review
- **WHEN** a reviewer inspects the registry's fixtures and examples
- **THEN** every person, identifier, address and contact value carries a synthetic marker and none is real

### Requirement: PR-15 One party per real person or organization; every other identity is a role or a link
openPractice SHALL keep one party record per real person or organization in its party register (ruling 32, adopting framework D1; this requirement's wording is spec-critique correction SC-2, design call F-6, PROPOSED). Every other identity SHALL be a role of a party or a link between parties. After attributable verification of identity, the existing party SHALL be reused. Where identity remains unconfirmed, PR-08 permits a separately audited record with a coded reason. Suspected duplicates enter review. This exception SHALL NOT authorize a second patient role for a party or for parties in an active same-person group. The system SHALL check this invariant when concurrent registration, role promotion and link changes commit. Linking two already-existing patient roles MAY place both in one group for investigation and duplicate retirement; the link creates neither role and neither is moved. The role and link model is:
- the administrative patient record of PR-01 is the patient role of a party;
- a guarantor who is another person (PR-06), a subscriber who is not the patient (PR-07), a person named as a contact, and a representative (change `define-patient-gateway-and-escalation` PG-06) are roles of, or links to, a party;
- a person who later becomes a patient, such as a guarantor parent, keeps the same party and gains the patient role;
- a payer and the practice are parties of type organization;
- the practice's owners SHALL be recorded as dated links from the practice's organization party to each owner's party (ruling 145), with provenance, and audited (PR-10). The practice manager records, changes and ends owner links, as a tier-2 act, and each record keeps the role used, as ruling 142 does for agreements; each is notified with no PHI to every holder of the practice manager or the controller or office manager recipe (change `define-actors-agents-and-work-inbox` AM-02), and to the outside accountant where one is on the roster (design call F-12, PROPOSED). Ending an owner link SHALL record a reason from a closed list: `ownership_ended`, `deceased` or `recorded_in_error`. Ending one with reason `recorded_in_error` SHALL record what the mistake was, SHALL be audited and notified as recording one is, and SHALL also be notified, with no PHI, to the party the link named and to every other party named by an owner link in force, at the contact details on record for each, and where a party has no contact detail on record, the undelivered notice SHALL become a work item, owned by whoever decided the ending, never the actor who ended the link, and shown to the outside accountant where one is on the roster; where the link names the actor's own party, or any party in its group, it is an own-record act under the rule below (design calls F-12 and F-7, PROPOSED; `define-practice-readiness` SR-04 and its design call RD-11, PROPOSED). Ending an owner link with reason `recorded_in_error` SHALL be decided by another holder of the practice manager or the controller or office manager recipe where one is on the roster, and otherwise under this requirement's one-person alternative (design call F-12, PROPOSED). Change `define-practice-readiness` SR-04, a draft not yet on `main`, reads this reason. The first owner link the practice ever records SHALL NOT be held for a second person or for an outside accountant, whoever it names; after any owner link has ever been recorded, subsequent owner-link operations follow their ordinary grant and applicable review rules without this exemption, even if the earliest link ended or was recorded in error (design call F-12, PROPOSED). An owner link SHALL grant no access and no authority anywhere: change `define-practice-readiness` SR-04 reads these links only to check independence (rulings 139 and 149), and where a requirement gives the practice's owner an act or a view, it means the holder of the practice manager recipe, and a recorded owner grants nothing by itself (ruling 193; the details are design call F-13, PROPOSED).

Search before create (PR-08) SHALL also show every party that exactly matches and holds another role, so that an existing party gains the patient role instead of a second record. Because a guarantor, contact, representative or customer may be recorded without a date of birth, the search SHALL also show every party whose name parts match exactly and that has no date of birth on record, marked as having none. For a party shown because it holds another role or has no date of birth, the result SHALL show only its name, its date of birth if any, and the kinds of role it holds, never the patients it is linked to, the links themselves or any balance, because customer-role-boundaries restricts the visibility of sensitive links. Giving an existing party the patient role SHALL require the verified matching evidence and attributable authority that customer-role-boundaries requires for a link, recorded with the person who confirmed it; a matching name, or a matching name and date of birth, is not evidence on its own. Each human roster entry SHALL reference exactly one party (change `define-actors-agents-and-work-inbox` AM-01). An AI agent and a named integration identity SHALL NOT be parties. The commerce customer role SHALL follow ruling 32: it applies only to membership and retail buyers, and a medical-billing accounting customer is derived from the guarantor or payer role (change `define-customer-financial-data-boundaries`, customer-role-boundaries). Table and DocType names are set in planning.

Sharing one party SHALL NOT merge what belongs to a role or a link. Communication authority that is specific to a relationship, effective dates, provenance and visibility stay with the role or link they belong to, and authority held through one role grants nothing through another (customer-role-boundaries). Confidential-communication instructions (PR-05) are the exception, because they protect the person: a restriction or alternate channel recorded through any role SHALL apply to every contact openPractice makes to that party, whatever the role, unless the person explicitly records, as a dated PR-05 instruction, that a named role is excepted. A role's own contact details never count as such an exception, and where two instructions conflict, the more restrictive applies. Reading a party that holds the patient role, through its guarantor, staff or any other role, is a read of a patient record and is audited under PR-10. Deactivation (PR-11) and hard delete (PR-12, PR-13) act on the patient role: a party that still holds another role or link keeps it, and its shared identity facts are not removed while any role or link remains. A deactivation with reason `deceased` SHALL raise review work for every other role and link the party holds, and SHALL change none of them automatically. A hard delete of a patient role created in error, on a party that holds another role or link, SHALL remove the patient role and the content only it uses, such as its coverages and its guarantor assignment, and SHALL keep the legal name, contact facts and confidential-communication instructions the other role or link still uses; PR-13's confirmation SHALL say which facts were kept and why. The party is openPractice's own record. It is never shared with, or matched to, an identity in HealthLinc or the EHR, which link to it only through their own link contracts (ruling 39; EB-01; PG-05). Finding that one person has two party records SHALL NOT merge them, automatically or otherwise (PR-08; ruling 150): it raises review work. This change defines no merge that moves roles, patient records, links or balances from one party to another, and no undo of one: a same-person link is version 1's answer, and a full merge, with its undo, would be its own later change (ruling 150). Until one exists, the two parties stay two records.

**Same-person groups (ruling 150; design call F-6, PROPOSED).** When two records turn out to be the same person, they are linked, not merged (ruling 150); every detail of the link below is design call F-6, PROPOSED under that ruling. A named human MAY record that two parties are the same person. It is a tier-4 act signed by a named human (BAB-17), made on the verified matching evidence and attributable authority that giving an existing party the patient role requires, after seeing both parties side by side, and audited with both parties' identifiers (PR-10). Same-person links join parties into a group: every party reachable from another through a chain of links is the same person, and every rule in this paragraph applies across the whole group. While a group stands:
- a confidential-communication restriction (PR-05) recorded on any party of the group, such as `no_home_mail`, `no_voicemail` or `alternate_channel_only`, applies to every contact openPractice makes to any party of the group, the more restrictive winning. Wherever openPractice passes a party's restrictions to another system, such as the EB-06 snapshot to the EHR or the `restriction_codes` that change `define-patient-gateway-and-escalation` carries on its notices, it SHALL include every restriction that crosses to that party, as restriction flags only and never another party's channels, and it SHALL republish a linked record's EB-06 snapshot when a link of its group is recorded, and when an end-of-link review changes the restrictions it carries. An alternate channel belongs only to the party it is recorded on: openPractice never routes a contact for one party to another party's alternate channel automatically. Where a restriction that crossed a link bars the channel a contact would have used, and the contacted party has no permitted channel of its own, openPractice sends nothing: it holds the contact and raises work, owned by a roster entry, for a named human to choose how to reach the person. That person MAY record another party's alternate channel on this party as a dated PR-05 instruction, after confirming it with the person, and from then on it is this party's own instruction. For a notice whose clock starts only on delivery, change `define-patient-gateway-and-escalation` PH-05 and PG-04 already govern (its design call DC-41): the clock does not start, and nothing takes effect, until a delivery through a permitted channel is recorded. No requirement in this change sets a delivery deadline for a statement or an estimate, so none is added; such a held contact stays work owned by a roster entry until a named human chooses a channel;
- search and display show the parties of a group as one person, to staff only, within the visibility limits that apply to each, and label each item with the party it belongs to. The combined view never reaches a patient-facing answer, a statement or a gateway view;
- every rule that asks whether the actor's own party holds a role on a record, such as the rule below for a staff member's own record and change `define-actors-agents-and-work-inbox` AM-01's rule that a roster entry never acts as patient, guarantor or representative, treats the whole group as one party. Recording or ending a link that involves the actor's own party, or any party in its group, is itself an own-record act under that rule;
- no party of the group gains a patient role while another party of the group holds one. The refusal SHALL say that a linked party already holds a patient role, and SHALL raise work, owned by a roster entry, to review the link;
- from the moment a link is recorded, a fact arriving on any patient record of the group, for a service date on which another record of the group holds a charge, SHALL NOT be charged automatically, whatever its resource reference, service code, modifier or rendering provider, and whether or not any record has been deactivated: it raises reconciliation work owned by a roster entry, and the fact is held from charging until that work decides it. That hold never releases by itself: at the payer's filing threshold it escalates to the practice manager, and escalation, not release, is its deadline escape under EB-18; no FD-5 automatic release applies to it. When a link is recorded, openPractice SHALL also raise reconciliation work for every service date on which two records of the new group already hold charges.

Ending a same-person link is a named human tier-4 act with a coded reason and an audit record. The system SHALL end only the selected edge and recompute active connected components. Where another active path still connects two parties, their group rules continue, including visibility limits, confidentiality, own-record checks and the patient-role invariant. Where the edge removal separates parties, active search and display SHALL use the resulting groups, with the existing disclosure rules. Roles, patient records, links to other systems, work, accounts and clinical or financial history retain their original ownership.

For a pair separated by the edge removal, every crossing confidentiality restriction and held contact SHALL remain protected until reviewed. The duplicate-charge safeguard and its pending holds SHALL continue for affected pairs, and own-record conflict checks SHALL continue for an actor and target whose parties were joined until distinct identity has been established. If either party already holds a patient role, creating one on the other party also waits for the independent finding; ordinary authorized read access to existing roles continues. The existing alternate-channel rule continues. The unlink reason, including `mistaken` alone, SHALL NOT authorize release of any pair's protections. A named human with the tier-4 grant who did not sign the unlink and has no own-record conflict SHALL record verified evidence of distinct identity for each pair whose safeguards are lifted. One decision MAY cover multiple named pairs only when it supplies evidence for each pair. The review SHALL record how each affected party's crossing restrictions and held contacts are resolved. Protections still required by an active path or a remaining unresolved review stay in force. Closure and release decisions under this paragraph have no one-person alternative.

Ending an edge SHALL raise owned review work with a due time and escalation to the practice manager. It SHALL identify affected pairs, candidate crossing restrictions, holds, and attributable actions that relied on the former group, including duplicate deactivations, withdrawn links, rebookings, held contacts and claims voided and rebilled. The reviewer SHALL disposition each affected pair and action. A retained safeguard MAY be the recorded outcome. Release MAY close only the reconciliation work covered by the authorized disposition, with its reason. When a required capability or review input is unavailable or unknown, the system SHALL hold the dependent action and raise work.

**Two patient records for one person (design call F-6, PROPOSED).** Retiring one of them is PR-11's deactivation with reason `duplicate`, and for that reason it is allowed only when all of these hold:
- the party of the record to be deactivated and the party of the record that stays are in the same group;
- the record to be deactivated holds no active EHR link, and no pending link request or identity review other than one withdrawn as stated below;
- where one of the two records holds an active EHR link, that record is the one that stays; where neither does but one has a pending link request or identity review, that one stays; where both have one aimed at the same EHR patient, the signer chooses which stays;
- the record that stays is active; a deactivated record that holds the active link is reactivated under PR-11 first;
- the record to be deactivated holds no future-dated appointment, no visit not yet charged and no unbilled charge; staff rebook, charge or cancel them first. Claims in flight and balances do not block it, because PR-11 keeps them working on a deactivated record;
- work, owned by a roster entry, has been raised to review the coverages, guarantor assignment and preferences of the record to be deactivated for the record that stays;
- it is signed at tier 4 (BAB-17) by a named human other than one who recorded any link of the group, after seeing both records side by side at the deactivation. Where the roster holds no other named human who may sign it, the same person signs after that check, and the deactivation is recorded as a use of a one-person alternative (ruling 107; `define-practice-readiness` SR-03).

The registry SHALL verify the group, authority, applicable tier-4 signature and activity predicates again at commit. It SHALL verify independence or the applicable, durably recorded one-person alternative allowed for duplicate deactivation. The separate scope-release review for unlink keeps its own independence rule. State that is unavailable, stale or indeterminate SHALL block the act, and any refusal, work item or successful decision SHALL be recorded through the versioned command. This command SHALL NOT transfer records, links or balances. A pending link request or identity review on the record to be deactivated that targets the same EHR patient as the staying record's active link or pending request SHALL first be withdrawn by a named human, with `link.revoked` and reason `duplicate_record`. One that targets a different EHR patient SHALL NOT be withdrawn: that is the next case.

**The EHR may hold two records for the person (design call F-6, PROPOSED).** Where two records of a group aim, through active or pending EHR links, at two different EHR patients (both hold an active link; one holds an active link and the other a pending link request or identity review; or both hold pending ones), neither is deactivated as a duplicate of the other and nothing is withdrawn. When a link is recorded joining such records, or a duplicate deactivation is attempted on them, openPractice SHALL record that the EHR may hold two records for this person and raise work, owned by a roster entry, for the practice to ask the EHR to review whether its two records are the same person; openPractice asks the EHR for no merge. The work escalates to the practice manager when it passes its due time. Both records stay active and keep receiving facts, and the group's protections apply meanwhile, including the safeguard above against charging a republished encounter twice.

**Facts after a duplicate deactivation (design call F-6, PROPOSED).**
- Facts stay with the record that received them, with their charges and claims (PR-11), and a retraction under an ended link is applied there as pointers only (EB-15, ruling 88).
- A corrected or new fact that arrives under the staying record's link for an encounter first received on the deactivated duplicate, matched by the EHR's resource reference, SHALL NOT be applied as a new version of facts the staying record never held. openPractice SHALL re-read the whole encounter under the staying link, raise work, owned by a roster entry, to void the duplicate's claim and rebill from the staying record, and hold the staying record's charge until the duplicate's charge is voided or credited. Only void and rebill applies here: EB-15's corrected-claim action is not offered for the duplicate's claim, because the corrected fact lives on the staying record. A retraction under the staying link that names a fact first received on the duplicate changes nothing by itself (EB-15) and leads to the same re-read and work.
- That hold never releases by itself. At the payer's filing threshold it escalates to the practice manager; escalation, not release, is this gate's deadline escape under EB-18, and no FD-5 automatic release applies to it.

**The gateway link (design call F-6, PROPOSED).** Where a patient's HealthLinc account is linked to a record deactivated as a duplicate, and the record that stays holds no active gateway link, linking the account to the record that stays is a new `link.request` under PG-05, made after verified identity and a named human's confirmation; once it is linked, a named human revokes the link on the duplicate under PG-07. Until then, the patient's app is served on the old link as a link to an inactive record (PG-04): urgent escalation, billing contact and own-records access stay granted, other capabilities are withheld with reason `registry_inactive`, and non-urgent clinical messaging and medical-AI consults are withheld only after the patient has been told and the approved grace period has passed (ruling 50). Where the record that stays already holds the active gateway link, nothing is requested again, and the duplicate's gateway link is revoked by a named human under PG-07 (design call F-6, PROPOSED).

**A staff member who is also a patient, guarantor, subscriber or representative.** One person may hold several roles (ruling 107): the roster entry and the other role reference the same party, and every sign-off records the role used. These actions SHALL be owned and decided by another human roster entry that holds the grant, or by an agent whose sponsor is not the actor (design call F-7, PROPOSED):
- on a record where the actor's own party, or any party in its same-person group, holds a role: a write-off, adjustment, discount, refund or balance transfer on a balance the person owes or guarantees; a change to their own coverage, guarantor assignment or deactivation; and any approval or sign-off on that record;
- recording or ending a same-person link that involves their own party or any party in its group;
- changing or ending an owner link that names their own party or any party in its group. Recording an owner link that names them is not caught, because it can only restrict the person it names, since an owner link grants no power (ruling 193); that holds only while no requirement gives an owner any power, and a change that gives owners a power must revisit it, and even then the first owner link the practice ever records is never held (design call F-12, PROPOSED);
- acts in which the actor's role arises only from the act itself: confirming a gateway account link (change `define-patient-gateway-and-escalation` PG-05) or a representative's authority (PG-06) whose account holder or representative is the actor's own party or any party in its group, and placing or lifting a payment hold (PH-04) on a patient who is the actor's own party or in its group, or whom the actor's own party, or any party in its group, represents, or on a balance the actor's own party, or any party in its group, owes or guarantees. Ruling 185 rules three of these: confirming one's own app-account link to a patient's record, confirming one's own authority as a patient's representative, and lifting a payment hold on a balance one owes each need a second person where one exists, and use ruling 144's one-person alternative where none does (ruling 185); the rest of this bullet, the same-person group and placing a hold included, is design call F-7 (PROPOSED).

When an agent acts, its sponsor's party and the party of any person on whose request or behalf it acts (its principal under change `define-actors-agents-and-work-inbox` AM-03, where that principal is a person), each with every party in its same-person group, count as the actor's own party under this rule (design call F-7, PROPOSED). Routine work, such as booking an appointment for oneself or one's child, recording a changed address, or reading the record, is not caught. Ruling 107 asks a rule that needs two people to name a one-person alternative. The alternative F-7 names under ruling 107 is this: where, at the moment of the act, the roster holds no other human roster entry that holds the grant and no agent holding the grant whose sponsor is not the actor, the person may act; ruling 192 rules that ruling 144's "only where the practice has nobody else" means nobody else who holds the permission for the act (ruling 192); that an agent holding it counts, unless the actor sponsors it, is change `define-actors-agents-and-work-inbox` design call D-39 (PROPOSED); the action records the role used and that the actor's own party holds a role on the record; and openPractice sends a notice of the action, with no clinical content, to the practice's accountant, the outside-accountant roster entry of change `define-actors-agents-and-work-inbox` AM-01 (ruling 141), listing everyone else who was on the roster at the time (ruling 192), each with the recipes they held (design call D-39, PROPOSED), which stays open until the accountant records that it was reviewed. While the roster holds no outside accountant other than the actor, the alternative is unavailable and the act waits (ruling 144), as the fail-closed rule of `define-practice-readiness` SR-03 requires. The alternative also applies to deciding a proposal the person made in another role, such as a change they proposed as a patient: where the practice has nobody else, the decision records both roles and notifies the outside accountant, and without an outside accountant on the roster it waits (ruling 144). Everywhere else the rule of change `define-actors-agents-and-work-inbox` WI-03 that the decider is never the proposer stands. Each use is recorded as a use of a one-person alternative, as `define-practice-readiness` SR-03 requires. An accountant's or counsel's sign-off SHALL NOT be self-approved (ruling 107).

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
- **WHEN** a staff member in a one-person practice starts a refund on their own balance, and the roster holds no outside accountant
- **THEN** the one-person alternative is unavailable, and the refund waits until another eligible person or an outside accountant is on the roster

#### Scenario: A sole staff member decides their own patient-app proposal
- **WHEN** the only staff member of a practice decides an address change they proposed as a patient, and an outside accountant is on the roster
- **THEN** the decision is allowed under the one-person alternative, records both roles, and notifies the outside accountant; with no outside accountant on the roster it waits

#### Scenario: The practice's owners are recorded
- **WHEN** the practice manager records that two physicians own the practice from a stated date
- **THEN** two dated links run from the practice's organization party to each physician's party, with provenance; the act is audited, and the holders of the practice manager and the controller or office manager recipes are notified, and so is the outside accountant where one is on the roster; neither physician gains any access or authority from the link

#### Scenario: An owner ends their own owner link
- **WHEN** an owner who holds the practice manager recipe tries to end the owner link to their own party
- **THEN** it is an own-record act: another human roster entry that holds the grant decides it, or the one-person alternative applies

#### Scenario: A sole owner records themselves as owner
- **WHEN** the only human on a practice's roster, its owner and practice manager, records the owner link that names their own party
- **THEN** it is recorded at once with the role used; it is the first owner link the practice has ever recorded, so it is never held for a second person or an outside accountant, and it is not an own-record act either, because it can only restrict the person it names

#### Scenario: An owner link is ended while an outside accountant is on the roster
- **WHEN** the practice manager ends the owner link of a physician who has sold their share, and the roster holds an outside accountant
- **THEN** the act is audited and notified with no PHI to the holders of the practice manager and the controller or office manager recipes and to the outside accountant

#### Scenario: A co-owner's link is ended as recorded in error
- **WHEN** the practice manager ends a co-owner's owner link with reason `recorded_in_error`, and an office manager is on the roster
- **THEN** the ending is routed to the office manager to decide; where no other holder of either recipe is on the roster, it goes under the one-person alternative, with its notice to the outside accountant, and waits while no outside accountant is on the roster; the ending records what the mistake was, and is notified, with no PHI, to the co-owner and to every other owner whose link is in force, at the contact details on record for each, within their confidential-communication instructions; where one of them has no contact detail on record, the undelivered notice becomes a work item owned by whoever decided the ending, never the practice manager who ended the link, and shown to the outside accountant where one is on the roster

#### Scenario: The only holder of the grant, with other staff on the roster
- **WHEN** a practice's only holder of the refund grant, who is also its patient, refunds their own credit balance, two front-desk staff without that grant are on the roster, and an outside accountant is on the roster
- **THEN** the one-person alternative applies, because nobody else holds the permission (ruling 192); the refund records both roles, and the notice to the outside accountant lists the two front-desk staff and every other roster entry, with the recipes each held

#### Scenario: A staff member confirms their own representative authority
- **WHEN** a staff member is asked to confirm their own authority as a representative of a patient they have no other role with, and another named human may confirm it
- **THEN** it is an own-record act: the other person confirms it, and the staff member cannot

#### Scenario: A correct link is ended in error, then an old encounter is republished
- **WHEN** the link between P1's and P2's parties is ended in error, and before the end-of-link review a fact arrives on P1 for a service date on which P2 holds a charge
- **THEN** it is not charged automatically and raises reconciliation work, because the safeguard keeps applying until the review decides it

#### Scenario: A staff parent books their child's appointment
- **WHEN** a staff member books an appointment for their own child
- **THEN** the booking proceeds as routine work and needs no one else

#### Scenario: An AI agent joins the roster
- **WHEN** an AI agent is added to the roster
- **THEN** its roster entry references no party, and no party record is created for it

#### Scenario: One person is found twice
- **WHEN** staff find that a guarantor's party and a patient's party are the same person
- **THEN** review work is raised; a named human records the same-person link at tier 4 after seeing both side by side, on verified evidence; nothing moves, and the guarantee, its account and the patient record each stay where they are

#### Scenario: A mail restriction across linked parties
- **WHEN** party P1 carries `no_home_mail` and an alternate address, and party P2, linked to P1 as the same person and with no alternate address of its own, holds the patient record whose statement is due
- **THEN** the statement is not mailed to P2's home address and is not sent to P1's alternate address: it is held, and work is raised for a named human to choose how to reach the person, who may record P1's alternate address on P2 as a dated instruction after confirming it with the person

#### Scenario: Two people linked by mistake
- **WHEN** parties A and X, two different people, are linked as the same person by mistake, and A carries `no_home_mail` and an alternate address
- **THEN** nothing of X's is sent to A's alternate address; X's mail is held for a named human instead of going to X's home address; when the link is ended with reason `mistaken`, A's restriction keeps applying to X until the end-of-link review, which may release it in one decision, after which X's contacts use X's own channels; the review also covers every contact held and every act made under the link

#### Scenario: A second signer catches a mistaken link
- **WHEN** the parties of two different people were linked by mistake, and a duplicate deactivation of one person's record is put to a named human other than the one who recorded the link
- **THEN** the side-by-side check at the deactivation shows two people, the deactivation is not signed, and the link is ended with reason `mistaken`

#### Scenario: A correct link is ended in error while a letter is held
- **WHEN** a statement for P2 is held because P1's `no_home_mail` crossed their link, and the link is ended in error
- **THEN** the statement stays held, and `no_home_mail` keeps applying to P2, so a new statement created after the end is held too and P2's EB-06 snapshot keeps the flag, until the end-of-link review, which records `no_home_mail` on P2 as its own if it applies; nothing is mailed to the barred address

#### Scenario: A duplicate patient record is retired
- **WHEN** the parties of patient records P1, with no EHR link, and P2, linked to EHR patient E, are linked as the same person; P1 has no future appointment, uncharged visit or unbilled charge, and work is raised to review its coverages; and a named human other than the one who recorded the link deactivates P1 as `duplicate` on a Monday, after a side-by-side check
- **THEN** P2 and its EHR link are untouched, and a visit on the Wednesday arrives from E on P2 and is charged as usual

#### Scenario: The linked record cannot be the one retired
- **WHEN** staff try to deactivate P2, which holds the active EHR link, as `duplicate` of P1
- **THEN** the deactivation is refused; only P1 can be deactivated, once its preconditions hold

#### Scenario: The EHR may hold two records for the person
- **WHEN** P1 is linked to EHR patient E1, and P2, linked to P1 as the same person, has a pending `link_existing` request to a different EHR patient E2
- **THEN** neither record is deactivated and P2's request is not withdrawn; openPractice records that the EHR may hold two records for this person and raises work for the practice to ask the EHR to review whether they are the same person, which escalates to the practice manager when overdue; both records keep receiving facts

#### Scenario: A duplicate with a future appointment
- **WHEN** the record to be deactivated as `duplicate` has an appointment next week
- **THEN** the deactivation is refused until staff rebook the appointment on the record that stays, or cancel it

#### Scenario: A duplicate with only a claim in flight
- **WHEN** the record to be deactivated has a submitted claim still open, but no future appointment, no visit not yet charged and no unbilled charge
- **THEN** the deactivation may proceed, and the claim keeps working on the deactivated record (PR-11)

#### Scenario: The EHR resolves its duplicate first
- **WHEN** P1, linked to EHR patient E1, and P2, linked to E2, belong to one group, and the EHR folds E2 into E1, revokes P2's link and republishes P2's old encounters under E1 with new references
- **THEN** the republished facts arrive on P1 for dates on which P2 holds charges, and none of them is charged automatically: each raises reconciliation work, although P2 has not been deactivated

#### Scenario: Three parties for one staff member
- **WHEN** a staff member's roster party R, their patient party P and a guarantor party K created for their child are linked R to P and P to K
- **THEN** R, P and K form one group: a restriction on R covers contacts to K, a write-off on K's balance is the staff member's own record, and K cannot gain a patient role while P holds one

#### Scenario: A staff member ends the link on their own parties
- **WHEN** a staff member who holds the tier-4 grant tries to end the link between their roster party and their patient party
- **THEN** ending it is an own-record act: another human roster entry that holds the grant decides it, or the one-person alternative applies, and the staff member cannot end it and then write off their own balance unseen

#### Scenario: A patient role refused because of the link
- **WHEN** staff try to give a patient role to a party whose group already holds one
- **THEN** the refusal says that a linked party already holds a patient role, and work is raised to review the link

#### Scenario: A correction for an encounter first received on the duplicate
- **WHEN** after a duplicate deactivation, a corrected diagnosis arrives under the staying record's link for an encounter openPractice first received on the duplicate
- **THEN** openPractice re-reads the whole encounter under the staying link, raises work to void the duplicate's claim and rebill from the staying record, and holds the staying record's charge until the duplicate's charge is voided or credited; at the filing threshold the hold escalates and is never released by itself

#### Scenario: The EHR gives an old encounter a new reference
- **WHEN** a fact arrives on one record of a group with a resource reference openPractice has not seen and a different service code, for a service date on which another record of the group holds a charge
- **THEN** it is not charged automatically, and reconciliation work is raised

#### Scenario: A same-person link is ended
- **WHEN** a named human ends a same-person link with a coded reason
- **THEN** the selected edge ends, active group rules continue for parties still connected, and the system opens owned, due and escalating review work for pairs and actions affected by any split. Crossing restrictions, held contacts, duplicate charge safeguards and the own-record conflict check persist for separated pairs until the appropriate scoped dispositions are recorded. Existing record ownership and historical evidence are preserved.

#### Scenario: Twins linked by mistake
- **WHEN** the link between twins' parties is ended with reason `mistaken`, and facts for both are held because both were seen on the same day
- **THEN** an independent tier-4 reviewer must verify that this pair is distinct before its safeguard is lifted. Only reconciliation for that pair closes with the recorded reason, and released facts return to the normal finalization checks

#### Scenario: Removing one edge leaves an alternate path
- **WHEN** A and B share a direct link and are also connected through C, and the A-to-B edge ends
- **THEN** all three retain group protections and the patient-role invariant, and no crossing privacy restriction, charge safeguard or own-record check is released by ending that edge

#### Scenario: An actor tries to use unlink to decide an own-record action
- **WHEN** an edge removal splits the actor's party from a target party and there is no independent distinct-identity finding for the pair
- **THEN** the target remains an own-record conflict for protected decisions, even if the unlink reason was recorded as mistaken or the actor holds approval authority in another role

#### Scenario: Concurrent activity invalidates duplicate retirement
- **WHEN** a qualified signer confirms retired and staying records, and another transaction adds an uncharged visit, active EHR link or future appointment before the retirement commits
- **THEN** the retirement fails its updated checks, preserves both roles and history, and raises owned review work

#### Scenario: A review that would close without deciding the safeguard
- **WHEN** an end-of-link review tries to close without a decision on the safeguard for a pair of records, or tries to release it for a correct link ended in error without a finding that the parties are different people
- **THEN** it cannot close, or the release is refused, and the safeguard stays

#### Scenario: The end-of-link review is overdue
- **WHEN** an end-of-link review passes its due time undecided
- **THEN** it escalates to the practice manager, and every crossing restriction keeps applying until it is decided

#### Scenario: A held fact reaches the filing threshold
- **WHEN** a fact held by the same-person safeguard is still unreconciled at the payer's filing threshold
- **THEN** the hold escalates to the practice manager and is not released

#### Scenario: A patient record is deactivated while its party is a guarantor
- **WHEN** a patient's record is deactivated with reason `no_longer_a_patient`, and the same party is the guarantor of a minor's balance
- **THEN** the patient role is deactivated, and the guarantor role, its contact facts and its balances continue unchanged

#### Scenario: A guarantor dies
- **WHEN** a patient's record is deactivated with reason `deceased`, and the same party is the current guarantor of a minor
- **THEN** review work is raised for the guarantor role and every other role and link the party holds, and none of them changes until a person acts
