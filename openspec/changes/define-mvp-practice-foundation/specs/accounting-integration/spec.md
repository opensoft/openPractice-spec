## ADDED Requirements

### Requirement: One accounting integration contract serves every destination
openPractice SHALL define one versioned accounting integration contract, `openpractice-accounting-integration/1.0`, which every accounting destination adapter implements. The contract SHALL cover ERPNext Accounts, QuickBooks Online and Microsoft Dynamics 365 Business Central. It SHALL define these operations: `customer.upsert`, `invoice.post`, `payment.post`, `credit_note.post`, `reversal.post`, `refund.post`, `deposit.post`, `journal.post`, `batch.export` and `reconciliation.report`. The contract SHALL carry a `MAJOR.MINOR` version: additive changes increment MINOR, and breaking changes require a new MAJOR version and a migration path.

#### Scenario: A new destination type is proposed
- **WHEN** an adapter for a destination is planned
- **THEN** it implements the same contract operations and version, and adds no destination-specific behavior to openPractice's core

### Requirement: The MVP builds only the ERPNext adapter
The MVP SHALL build and ship only the ERPNext Accounts adapter. QuickBooks Online and Business Central adapters SHALL follow after the MVP against the same contract version. The system SHALL refuse to configure a destination whose type has no conforming adapter installed.

#### Scenario: A practice tries to configure QuickBooks Online in the MVP
- **WHEN** an MVP deployment attempts to configure a QuickBooks Online destination
- **THEN** the system refuses because no conforming adapter is installed and offers no partial integration

### Requirement: Adapters are isolated and openPractice has no import-time ERPNext dependency
openPractice SHALL install, start, run its tests and operate with no ERPNext application present. The contract interface and delivery engine SHALL live in openPractice's core. Each adapter SHALL be loaded only when a destination of its type is configured, and core code MUST NOT import any adapter or any destination's libraries. The ERPNext adapter SHALL support an ERPNext instance on the same Frappe site or bench and a remote ERPNext instance reached over TLS. In both cases it SHALL use ERPNext's documented document or REST interfaces, and MUST NOT execute SQL against, or write directly to, ERPNext tables.

#### Scenario: openPractice is installed without ERPNext
- **WHEN** openPractice is installed on a site with no ERPNext application and no accounting destination configured
- **THEN** installation, startup and the full test suite succeed

#### Scenario: ERPNext shares the site
- **WHEN** the ERPNext adapter posts to an ERPNext instance on the same site
- **THEN** it creates destination documents through ERPNext's documented document interface and performs no direct table write

### Requirement: Every posting carries a deterministic idempotency key
Every contract entry SHALL carry an idempotency key built from the destination identifier, the operation, the source ledger entry identifier and the source entry version. Delivering the same entry more than once SHALL leave exactly one destination document. After a timeout or other indeterminate outcome, the adapter SHALL look the entry up in the destination by its idempotency key before any retry creates a document.

#### Scenario: A delivery times out
- **WHEN** posting an invoice times out without a response
- **THEN** the adapter queries the destination by the entry's idempotency key, records the existing document if one was created, and creates one only if none exists

#### Scenario: A batch is redelivered
- **WHEN** a released batch is delivered a second time after a worker restart
- **THEN** no destination document is duplicated

### Requirement: Export batches are released by a person and frozen at release
The system SHALL assemble postings into export batches per destination. Each batch SHALL carry a batch sequence that strictly increases per destination, the contract version, the account-mapping version, the posting mode, the releasing actor, the release time and a content digest over its canonical content. A batch SHALL be released only by an explicit action of an authorized person, and scheduled automatic release MUST NOT be enabled by default. Release SHALL freeze the batch's content. The system SHALL refuse release in each of these cases:
- an entry lacks an account mapping;
- the batch is in patient-level mode and the destination's BAA gate is not satisfied;
- a deposit entry references a bank line that is not explained.

#### Scenario: Release is attempted with an unexplained deposit
- **WHEN** a person releases a batch whose deposit entry references a bank line that is not yet explained
- **THEN** the release is refused and the unexplained line is named

#### Scenario: A released batch is inspected later
- **WHEN** an auditor recomputes the content digest of a released batch
- **THEN** the digest matches the recorded value and the batch content is unchanged

### Requirement: Delivery failures are classified and never produce duplicates or silent loss
The system SHALL track delivery state per entry, separately from the frozen batch content, using the states `pending`, `posted`, `failed_transient`, `failed_permanent`, `indeterminate` and `superseded`.
- A `posted` entry SHALL record the destination document reference.
- `failed_transient` entries SHALL be retried with backoff and the same idempotency key.
- `indeterminate` entries SHALL be resolved by lookup before any retry.
- A `failed_permanent` entry SHALL become work owned by a Roster entry. It SHALL be resolved either by redelivering the unchanged entry after the destination-side cause is fixed, or by a correcting entry in a later batch that marks the failed entry `superseded`.

#### Scenario: The destination rejects an entry
- **WHEN** the destination permanently rejects an entry because its target account was archived there
- **THEN** the entry becomes `failed_permanent` work, the batch content is not edited, and the resolution is recorded either as a redelivery or as a superseding entry in a later batch

### Requirement: A reconciliation report proves the mirror
After each batch delivery, and on demand as of any date, the system SHALL produce a reconciliation report for a destination. The report SHALL compare openPractice's totals by reason category, and in patient-level mode by customer, with the destination's totals for documents that carry openPractice idempotency keys. It SHALL list missing documents, unexpected documents, amount mismatches and destination-side edits as exceptions. It SHALL state what it excludes: unidentified receipts, lines dispositioned as not ours to explain, and old-system collections. Each exception SHALL become work owned by a Roster entry.

#### Scenario: The mirror ties
- **WHEN** every entry of a delivered batch is posted and no destination document was edited
- **THEN** the report shows zero difference by reason category and lists its exclusions

#### Scenario: A mirrored document was deleted in the destination
- **WHEN** a bookkeeper deletes an openPractice-posted payment in the destination
- **THEN** the report lists the payment as missing and creates work to resolve it

### Requirement: Posting mode is a per-destination setting
Each destination SHALL have a posting mode, either `patient_level` or `summary`, and each batch SHALL record the mode it was released under. A destination SHALL be created in `summary` mode. A change of posting mode SHALL take effect at a batch boundary and SHALL never alter a released batch.

#### Scenario: A destination is first configured
- **WHEN** an ERPNext destination is configured
- **THEN** it starts in `summary` mode until patient-level mode is enabled for it

### Requirement: Patient-level invoices are the default posting granularity
Patient-level invoices SHALL be the default posting granularity for openPractice, replacing the deposit-level journal as the default. In patient-level mode the contract SHALL post the following:
- customers, with a guarantor account for patient receivables and a payer for insurance receivables;
- invoices for receivables;
- payments, into a clearing account mapped per destination;
- credit notes for adjustments, responsibility transfers and write-offs;
- dated reversals;
- refunds;
- deposits that move explained bank lines from the clearing account to the bank account.

`summary` mode SHALL exist only as the fallback for destinations whose BAA gate is not satisfied.

#### Scenario: A visit's charges are finalized
- **WHEN** a claim-billed visit's charges are finalized in a destination running in patient-level mode
- **THEN** the next batch contains an invoice to the payer customer referencing the visit by an opaque openPractice reference

#### Scenario: Patient responsibility is determined
- **WHEN** a remittance assigns part of a visit's balance to the patient
- **THEN** the batch carries a credit note on the payer invoice and an invoice to the guarantor customer, both linked by the same source event

### Requirement: Patient-level mode requires a verified Business Associate Agreement
Patient-level mode SHALL NOT be enabled for a destination until an authorized person has recorded, and verified, an executed HIPAA Business Associate Agreement (BAA) with that destination's vendor. The record SHALL capture the counterparty, execution date, effective and renewal or expiry dates, a scope statement covering the accounting service, an owner-local document reference, the verifying person and the verification time. Enabling patient-level mode SHALL be a governance tier-4 hard stop. When the recorded BAA expires or is revoked, the system SHALL halt patient-level delivery for that destination, hold its unreleased and undelivered patient-level work, and create work for an authorized person. It MUST NOT silently convert that work to summary mode. The system MUST NOT assume that any vendor offers or has signed a BAA.

#### Scenario: No BAA is recorded
- **WHEN** an administrator tries to enable patient-level mode for a destination with no verified BAA record
- **THEN** the system refuses and the destination stays in `summary` mode

#### Scenario: A BAA expires
- **WHEN** a destination's recorded BAA passes its expiry date
- **THEN** patient-level delivery halts, pending patient-level work is held unconverted, and an authorized person is given the work

### Requirement: Summary mode carries no PHI
In `summary` mode the contract SHALL post only aggregated journal entries, at deposit level or day level, made of account-level lines by reason category with amounts, posting dates and batch references. Summary-mode postings MUST NOT contain any of the following:
- patient, guarantor or subscriber names or identifiers;
- member or claim identifiers;
- dates of service;
- addresses or contact details;
- any other element that identifies an individual or relates a posting to one.

#### Scenario: A summary batch is inspected
- **WHEN** a reviewer inspects a summary-mode batch
- **THEN** it contains only account lines, amounts, dates and batch references, and no individual's name or identifier

### Requirement: Patient-level postings carry the minimum necessary
In patient-level mode, postings SHALL contain only the following:
- the guarantor display name and an opaque guarantor account reference;
- payer name and payer identifier;
- opaque openPractice references for patient, visit, claim and source entry;
- amounts, posting dates and invoice dates;
- reason-category descriptions.

Patient-level postings MUST NOT contain any of the following:
- dates of birth, addresses, telephone numbers or email addresses;
- government identifiers, member or group identifiers, or payer claim control numbers;
- diagnosis or procedure codes or descriptions, service descriptions beyond the reason category, dates of service, or clinical content.

#### Scenario: An invoice is built for a guarantor
- **WHEN** the adapter builds a patient-level invoice
- **THEN** it carries the guarantor display name, opaque references, amounts, dates and reason-category lines, and no diagnosis, procedure, date of birth or address

### Requirement: Integration credentials are protected and least-privileged
Each destination's credentials SHALL be stored in the platform's secret store, scoped to the accounting objects that the contract operations need, rotated under the Credential And Certificate Expiry Radar, and kept out of logs, fixtures, documentation and commits. The system MUST NOT request or use a destination permission that can originate a payment where the destination offers that permission separately.

#### Scenario: Credentials appear in a diagnostic bundle
- **WHEN** a support diagnostic bundle is generated for a failing delivery
- **THEN** it contains no credential, token or secret

### Requirement: The accounting boundary is covered by contract tests
Each adapter SHALL pass contract tests that exercise every contract operation, idempotent replay, indeterminate-outcome lookup, batch freezing, mode gating and reconciliation. The tests SHALL run against a contract double, or a disposable destination instance loaded with synthetic data only. Tests MUST NOT depend on live vendor services or real PHI.

#### Scenario: CI runs the adapter suite
- **WHEN** continuous integration runs the ERPNext adapter's contract tests
- **THEN** they pass against a contract double or a disposable synthetic instance, with no network access to a vendor's production service
