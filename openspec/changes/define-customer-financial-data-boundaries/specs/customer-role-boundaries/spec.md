# Spec Delta

## Purpose

Preserve usable customer identity across practice and accounting systems while
separating patient, member, payer and representative roles and clinical access.

## ADDED Requirements

### Requirement: Parties have distinct effective roles
The system SHALL distinguish person identity, customer, patient, membership subscriber, membership beneficiary, insurance subscriber, payer/guarantor and representative relationships. Each relationship SHALL preserve its authority, provenance, effective dates and organizational scope. Customer creation SHALL NOT require clinical patient creation. The customer role SHALL apply only to membership and retail buyers (ruling 32). For medical billing, an accounting-side customer (a guarantor account or payer, in `customer-detail` mode only) SHALL be derived from the guarantor or payer role and SHALL NOT be a separate party role.

#### Scenario: Retail purchaser has no patient relationship
- **WHEN** a person purchases a retail product without an established patient relationship
- **THEN** the system creates or uses the necessary customer record without creating a patient, encounter or claim

#### Scenario: Membership purchaser and beneficiary differ
- **WHEN** a subscriber pays for a membership benefiting another person
- **THEN** the system records their separate roles and does not grant the subscriber general access to the beneficiary's clinical or purchase history

### Requirement: Cross-system identity links are controlled
The system SHALL preserve a controlled mapping between operational party and domain-specific customer or patient identifiers. Linking and correction SHALL require verified matching evidence and attributable authority, preserve prior financial history, and restrict visibility of sensitive links. Any approval, release decision or attestation this change requires SHALL follow the FD-5 tier of its action under `define-mvp-practice-foundation` BAB-17 (ruling 33): a named AI agent identified by an openWallet ID MAY sign only an action type opted in for agent signing, and tier-4 actions, including record merges and refunds leaving the practice, SHALL be signed by a named human only. A shared contact detail alone SHALL NOT authorize a merge.

#### Scenario: Shared phone creates an ambiguous match
- **WHEN** two customer or patient records share a phone number but identity evidence does not resolve the match
- **THEN** the system creates review work and does not merge or cross-disclose the records

#### Scenario: A medical-billing customer is derived, not created as a role
- **WHEN** a guarantor's balance is posted to an accounting destination in `customer-detail` mode
- **THEN** the accounting-side customer is derived from the guarantor role, and no separate customer role is added to the party

#### Scenario: Customer identity does not grant patient lookup
- **WHEN** an accountant can view a customer's accounting identifier but lacks patient-record authority
- **THEN** the system does not expose the EHR identifier, patient lookup link or clinical record through that view

### Requirement: Shared fields have explicit ownership and update direction
The system SHALL declare the owning source, permitted update direction, destination fields and conflict behavior for each synchronized identity/contact field. Updates SHALL preserve confidential-contact instructions and relationship-specific communication authority.

#### Scenario: Billing address changes
- **WHEN** an authorized user corrects an accounting billing address
- **THEN** only permitted destination fields change and the system preserves unrelated clinical identity and confidential communication destinations

### Requirement: Accounting receives a limited customer and financial record
The system SHALL support necessary customer name, billing address/contact, scoped payer relationship, membership/invoice details, payments, refunds and balances in connected accounting. A versioned recipient profile, in the single shape that change `define-mvp-practice-foundation` defines (AI-23), SHALL define the permitted fields and purpose. The default accounting projection SHALL exclude chart notes, diagnoses, medication lists, results, full claim payloads, EHR medical-record numbers and service-revealing reason categories; any narrow additional field SHALL require documented purpose, recipient and authority. For a medical-membership record, offering and membership labels SHALL be neutral and SHALL NOT reveal a service, condition or type of care. A PHI-bearing customer record is a care-linked record, such as a medical-billing guarantor account including direct-pay and self-pay healthcare, or a medical-membership record. Any system that writes a PHI-bearing customer record into a connected accounting destination SHALL require that destination's BAA gate, whose record lives in openPractice's accounting destination register (`define-mvp-practice-foundation` AI-14); openPractice's offering routing enforces this for every routed obligation (financial-domain-routing). Name and billing address/contact of such a customer SHALL reach a destination only after that gate is satisfied; medical-billing customers SHALL be sent only in the `customer-detail` mode of `openpractice-accounting-integration/1.0`, and the `summary-journal` mode SHALL carry no customer records or names.

#### Scenario: Medical membership is invoiced
- **WHEN** a permitted accounting workflow issues a named customer's medical membership invoice
- **THEN** the projection contains the necessary customer and financial facts under the protected handling profile without copying the chart

#### Scenario: Broad source payload includes clinical detail
- **WHEN** an accounting connector receives a source object containing a chart note or diagnosis outside its allowed projection
- **THEN** it excludes that content from delivery, attachments and diagnostic output rather than forwarding the source object wholesale

### Requirement: Clinical authority and access remain independent
The connected EHR SHALL remain authoritative for encounters, signed clinical notes, diagnoses, medications, orders and results. openPractice medical billing SHALL consume only necessary coded facts and approved evidence under bounded contracts. Customer, accounting, membership and commerce permissions SHALL NOT grant clinical read or write authority.

#### Scenario: Biller needs coded service information
- **WHEN** an authorized biller prepares an insurance claim
- **THEN** the system supplies the required coded service facts and approved evidence without granting unrelated chart access or clinical editing authority

#### Scenario: Membership administrator requests a chart
- **WHEN** an actor authorized only for membership administration requests a linked chart
- **THEN** access is refused independently of the actor's ability to view or change the membership
