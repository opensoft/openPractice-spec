# Spec Delta

## Purpose

Keep identifiable business and accounting views useful while enforcing
purpose-specific access, destination handling and independent outreach authority.

## ADDED Requirements

### Requirement: Financial route does not determine privacy classification
The system SHALL evaluate the care/payment relationship and identifying context separately from billing route. It SHALL treat identifiable medical financial records, including medical membership and cash-pay healthcare records, as protected under the applicable deployment profile. Removing codes, substituting identifiers or moving a record into commerce SHALL NOT automatically classify it as de-identified or non-PHI.

#### Scenario: Medical membership has no diagnosis code
- **WHEN** a named customer's membership invoice relates to access to medical care but contains no medical code
- **THEN** the system applies protected handling rather than assuming ordinary unrestricted retail data

#### Scenario: Identifiers are replaced in an analytics export
- **WHEN** a patient-linked financial report substitutes customer keys for names without an applicable de-identification determination
- **THEN** the report remains protected and the substitution grants no additional recipient or export authority

### Requirement: Every business output has a scoped recipient profile
The system SHALL authorize each financial/customer projection and export against a versioned recipient profile in the single shape that change `define-mvp-practice-foundation` defines (AI-23). That shape covers the recipient (actor role, organization or destination), purpose, relationship/row scope, sources, fields, sensitivity, output channels and applicable handling obligations. This change SHALL NOT define a second profile shape. Checks SHALL cover structured fields, attachments, logs and connector diagnostics. An unresolved profile or destination unable to satisfy required safeguards SHALL prevent protected delivery.

#### Scenario: Connector destination cannot support required handling
- **WHEN** an accounting destination lacks required contractual or safeguard support for its proposed PHI payload
- **THEN** the system refuses protected delivery and creates a visible configuration exception without silently exporting it through a generic file

#### Scenario: Identity link is outside the recipient's relationship scope
- **WHEN** an employer payer requests financial or purchase records beyond the obligations it is authorized to pay or view
- **THEN** the system excludes those records even if they are linked to the same member or household

### Requirement: Accountant and biller access are independently bounded
The system SHALL scope outsourced accounting and billing actors to their actual jobs, attributed identities, effective access periods and applicable vendor arrangements, including required BAAs. Financial access SHALL NOT imply chart access or broader commercial outreach rights. Revocation SHALL stop future protected use without erasing required financial history or custody obligations.

#### Scenario: One firm provides accounting and billing
- **WHEN** separate actors at the same firm perform accounting and insurance billing
- **THEN** each receives its authorized financial view and the shared firm identity does not merge their permissions

#### Scenario: Accountant engagement ends
- **WHEN** an accountant's authorized access ends while financial records remain subject to retention or hold
- **THEN** future access and scheduled deliveries are refused while the records and applicable custody controls are preserved

### Requirement: Business metrics use reconciled source events and explicit semantics
Authorized analytics SHALL combine selected customer, subscription, medical-billing, commerce and accounting facts without requiring chart replication. Each financial metric SHALL identify its version, source events, period, categories, exclusions and treatment of invoices, collections, recognition, refunds and fees. Aggregate access SHALL NOT automatically permit identifiable drill-down or export, and attributed campaign revenue SHALL NOT be presented as proven incremental lift without supporting analysis.

#### Scenario: The practice manager compares medical and membership revenue
- **WHEN** the holder of the practice manager recipe, authorized for the view, requests recognized revenue by offering category
- **THEN** the system applies the stated metric version and period, counts authoritative recognition events once and does not add invoice or receipt totals as extra recognized revenue

#### Scenario: Manager can view totals but lacks customer detail
- **WHEN** a manager with aggregate-only scope requests an identifiable drill-down or customer-list export
- **THEN** the system refuses that output independently of access to the aggregate

### Requirement: Audience analysis does not authorize outreach
The system SHALL evaluate campaign use and sending separately from analytics access. Before audience disclosure or sending, it SHALL verify actual purpose, audience origin, recipient/channel authority, preferences, vendor scope, and any required authorization or applicable exception under the deployment's approved policy. Generic contact opt-in SHALL NOT be substituted for a required HIPAA authorization. Unresolved authority SHALL prevent the affected disclosure/send and SHALL NOT prevent access to care.

#### Scenario: Financial audience is proposed for an unrelated promotion
- **WHEN** a user selects a patient-linked financial cohort for an unrelated or third-party promotion without the required authority
- **THEN** the system holds the campaign and refuses audience disclosure or sending despite the user's permission to analyze revenue

#### Scenario: Queued campaign permission is revoked
- **WHEN** the relevant communication permission is revoked before a queued send executes
- **THEN** the system re-evaluates current authority and suppresses the send while recording its disposition
