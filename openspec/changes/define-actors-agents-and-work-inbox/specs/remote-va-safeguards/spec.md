## ADDED Requirements

### Requirement: VA-01 A verified BAA with the VA company comes first
No remote VA SHALL access PHI until an authorized person, who is the practice manager (ruling 142), records and verifies a HIPAA Business Associate Agreement (BAA) with the VA company. The record follows the pattern of AI-14: counterparty, execution date, effective and renewal or expiry dates, scope statement, owner-local document reference, verifying person, the role used (ruling 142) and time. When the BAA expires or is revoked, every VA from that company SHALL lose PHI access at once. The system MUST NOT assume that any VA company offers or has signed a BAA.

#### Scenario: A VA company with a verified BAA
- **WHEN** a VA company's BAA is recorded and verified, and one of its VAs is added
- **THEN** that VA may access PHI within the VA recipe

#### Scenario: The BAA lapses
- **WHEN** the VA company's BAA expires
- **THEN** each of its VAs loses PHI access at once, and their queues are reassigned

### Requirement: VA-02 A remote VA works only their own queue and cannot export
A remote VA SHALL see only the work items assigned to them, and only the records those items reference. A VA SHALL NOT export, print, bulk-download or bulk-copy records. The system offers these functions to no VA. The system SHALL record that screen capture cannot be prevented by software alone; this residual risk belongs in the practice's risk analysis.

#### Scenario: A VA opens an assigned item
- **WHEN** a VA opens a work item assigned to them
- **THEN** the VA sees the referenced record, and the read is audited

#### Scenario: A VA tries to export
- **WHEN** a VA requests a patient list export
- **THEN** the request is refused, and the attempt is logged with a coded reason

### Requirement: VA-03 Sensitive fields are masked for remote VAs
Fields that the roster masks by default stay masked for every remote VA: government identifiers, bank and card numbers, and any field the practice adds. A VA SHALL NOT unmask them. Further masked fields MAY be set per VA recipe in planning.

#### Scenario: A VA views a guarantor
- **WHEN** a VA opens a guarantor record
- **THEN** government identifiers and bank numbers are masked

#### Scenario: A VA tries to unmask
- **WHEN** a VA attempts to unmask a masked field
- **THEN** the attempt is refused and logged

### Requirement: VA-04 Remote VA access is bound to approved sessions and devices, and it expires
A remote VA's access SHALL require all of the following:
- an approved device;
- a session that times out when idle;
- the session and location rules the practice approves.

The exact rules SHALL be set in planning. Access SHALL fail closed when a device, session or location cannot be verified. Every VA roster entry SHALL carry an end date, and access SHALL end on that date without further action.

#### Scenario: An unapproved device
- **WHEN** a VA signs in from a device that is not approved
- **THEN** access is refused

#### Scenario: Access reaches its end date
- **WHEN** a VA's end date passes
- **THEN** the VA's sessions end and access stops, with no one having to act

### Requirement: VA-05 Each remote VA is individually accountable
Each remote VA SHALL be a named individual roster entry under their VA company. There SHALL be no shared logins. Every action SHALL be attributed to that individual (AM-03). The practice SHALL be able to report one VA's actions over any period.

#### Scenario: A per-VA report
- **WHEN** the office manager asks for one VA's actions last week
- **THEN** the report lists that VA's actions, with no other person's actions mixed in

#### Scenario: A shared login is detected
- **WHEN** two concurrent sessions use one VA identity from different devices
- **THEN** both sessions end, and the event becomes work for the office manager
