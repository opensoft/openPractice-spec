## ADDED Requirements

### Requirement: AI-01 One accounting integration contract serves every destination
openPractice SHALL define one versioned contract, `openpractice-accounting-integration/1.0`, which every destination adapter implements for ERPNext Accounts, QuickBooks Online and Microsoft Dynamics 365 Business Central. The contract operations SHALL be:
- `customer.upsert`
- `invoice.post`, `debit_note.post`, `credit_note.post`
- `payment.post`, `payment.apply`
- `reversal.post`, `refund.post`, `deposit.post`, `journal.post`
- `batch.export`, `bank_lines.read`, `reconciliation.report`

The contract SHALL carry a `MAJOR.MINOR` version. An adapter is compatible with openPractice exactly when both share the same MAJOR version. Additive changes increment MINOR. A breaking change requires a new MAJOR version, a new schema digest, and a migration path.

This contract SHALL own posting mechanics (ruling 26): posting identity and idempotency (AI-05), export batches with their freeze and release (AI-06), retries and delivery states (AI-07), the reconciliation report (AI-08), the posting modes `summary-journal` and `customer-detail` (AI-09), and the BAA gate (AI-14). Customer and party roles, routing by offering (insurance-billed healthcare, direct-pay healthcare, membership and retail), obligation ownership and purpose-bound business views belong to change `define-customer-financial-data-boundaries`, which references this contract for posting.

#### Scenario: A post-MVP adapter is planned
- **WHEN** an adapter for a new destination is planned against `1.x`
- **THEN** it implements the same operations, interoperates with any `1.x` core, and adds no destination-specific behavior to openPractice's core

#### Scenario: Another route posts through openPractice
- **WHEN** a route defined by `define-customer-financial-data-boundaries` sends an accounting effect through openPractice
- **THEN** it uses this contract's posting identity, batches, release, retries, reconciliation report, modes and BAA gate, and no second posting mechanism exists

### Requirement: AI-02 The accounting contract has one canonical schema identified by digest
The canonical schema SHALL be `openpractice-accounting-integration-1.0.schema.json` in this change's `contracts/` directory, identified by the SHA-256 digest of its bytes. It defines batch content, postings, summary lines and bank-line read results. Every adapter SHALL validate batch content against the schema with the recorded digest.

#### Scenario: A batch fails the schema
- **WHEN** an assembled batch does not validate against the canonical schema
- **THEN** release is refused and the failing postings are named

### Requirement: AI-03 The MVP builds only the ERPNext adapter
The MVP SHALL build and ship only the ERPNext Accounts adapter. QuickBooks Online and Business Central adapters SHALL follow after the MVP against `1.x`. The system SHALL refuse to configure a destination whose type has no conforming adapter installed.

#### Scenario: QuickBooks Online in the MVP
- **WHEN** an MVP deployment attempts to configure a QuickBooks Online destination
- **THEN** the system refuses and offers no partial integration

### Requirement: AI-04 Adapters are isolated, and the MVP reaches ERPNext only through its REST API
openPractice SHALL install, start, run its tests and operate with no ERPNext application present. The contract interface and delivery engine SHALL live in openPractice's core. Each adapter SHALL be loaded only when a destination of its type is configured. Core code MUST NOT import any adapter or any destination's libraries.

In the MVP, the ERPNext adapter SHALL call ERPNext only through its documented REST API over TLS, whether ERPNext runs on the same Frappe bench or remotely. It MUST NOT make in-process calls into ERPNext, execute SQL against ERPNext tables, or write to them directly. MVP acceptance SHALL be satisfied by the remote topology alone. Same-site in-process use requires counsel's clearance and a later governed change.

#### Scenario: openPractice without ERPNext
- **WHEN** openPractice is installed on a site with no ERPNext and no destination configured
- **THEN** installation, startup and the full test suite succeed

#### Scenario: ERPNext on the same bench
- **WHEN** ERPNext is installed on the same bench as openPractice
- **THEN** the adapter still calls ERPNext only through its REST API over TLS and makes no in-process call

### Requirement: AI-05 Posting identifiers are minted at assembly, and keys carry no personal data
At batch assembly, the system SHALL mint an opaque `posting_id` for each customer-detail posting. Its idempotency key SHALL be `<destination_id>:<posting_id>`. A posting that replaces an earlier one, for example after a period-lock refusal, SHALL get a new `posting_id` and a `supersedes` link to the one it replaces. It MUST NOT reuse the earlier key.

A summary-journal line SHALL use the key `<destination_id>:<batch_seq>:<line_no>`.

Idempotency keys MUST NOT contain names, account numbers, member identifiers, dates of birth, bank numbers or any other per-person or per-account data.

Delivering the same posting or line again SHALL leave exactly one destination document. After an indeterminate outcome, the adapter SHALL look up the key in the destination before any retry creates a document.

#### Scenario: A delivery times out
- **WHEN** posting an invoice times out without a response
- **THEN** the adapter queries the destination by the key, records the existing document if one exists, and creates one only if none exists

#### Scenario: A period-lock refusal is corrected
- **WHEN** a released posting is refused because the destination closed its period
- **THEN** a later batch carries a new posting with a new `posting_id`, a `supersedes` link to the refused one, and a new key

### Requirement: AI-06 Export batches are released by a person and frozen at release
The system SHALL assemble postings into export batches per destination. Each batch SHALL carry:
- a batch sequence that strictly increases per destination;
- the contract version;
- the destination-and-mode mapping version and the posting mode;
- the releasing actor's opaque reference and the release time;
- a SHA-256 content digest over the RFC 8785 canonical JSON of the batch content.

A batch SHALL be released only by an explicit action of an authorized person. Scheduled automatic release MUST NOT be enabled by default. Release SHALL freeze the content. Release SHALL be refused in each of these cases:
- a line lacks a mapping;
- the batch fails the canonical schema;
- the batch is in customer-detail mode and the destination's BAA gate is not satisfied;
- a deposit posting cites a bank line that has not been explained.

#### Scenario: Release with an unexplained deposit
- **WHEN** a person releases a batch whose deposit posting cites an unexplained bank line
- **THEN** release is refused and the line is named

#### Scenario: A released batch is audited later
- **WHEN** an auditor recomputes a released batch's digest
- **THEN** it matches the recorded digest

### Requirement: AI-07 Delivery failures are classified and never duplicate or silently drop postings
The system SHALL track delivery state separately from the frozen batch content, using these states: `pending`, `posted`, `failed_transient`, `failed_permanent`, `indeterminate` and `superseded`.
- A `posted` state records the destination document reference.
- The recorded or sync timestamp SHALL be stored only as delivery state and MUST NOT be used as an accounting date.
- Transient failures retry with backoff and the same key.
- Indeterminate outcomes are resolved by lookup.
- A permanent failure becomes work owned by a Roster entry. It is resolved by redelivering the unchanged posting after the destination-side cause is fixed, or by a superseding posting in a later batch.

#### Scenario: The destination rejects a posting
- **WHEN** the destination permanently rejects a posting because its target account was archived there
- **THEN** the posting becomes `failed_permanent` work, the batch content is not edited, and the resolution is a redelivery or a superseding posting

### Requirement: AI-08 A reconciliation report proves the mirror
After each delivery, and on demand as of any date, the system SHALL produce a reconciliation report for a destination. The report SHALL compare openPractice totals with the destination's totals for documents carrying openPractice keys: by reason category, and in customer-detail mode also by customer. It SHALL list missing documents, unexpected documents, amount mismatches and destination-side edits. It SHALL state its exclusions: unidentified receipts held as liabilities, bank lines labelled "accounting system's", and legacy collections. Each exception SHALL become work owned by a Roster entry.

#### Scenario: The mirror ties
- **WHEN** every posting of a delivered batch is posted and nothing was edited in the destination
- **THEN** the report shows zero difference and lists its exclusions

#### Scenario: A mirrored payment was deleted in the destination
- **WHEN** a bookkeeper deletes an openPractice-posted payment in the destination
- **THEN** the report lists it as missing and creates work

### Requirement: AI-09 Posting mode is chosen before a destination's first released batch
Each destination SHALL have a posting mode, `customer-detail` or `summary-journal`, and each batch SHALL record its mode. Every destination SHALL be created in `summary-journal` mode. It MAY move to `customer-detail` only before its first released batch, and only after its BAA gate is satisfied. After a destination's first released batch, the system SHALL refuse every change of its posting mode, until the owner ratifies a continuity procedure that defines the opening receivable true-up. A BAA lapse SHALL halt customer-detail delivery, and MUST NOT silently convert any work to summary-journal mode.

#### Scenario: Mode chosen before go-live
- **WHEN** an administrator enables customer-detail mode on a destination that has no released batch and has a satisfied BAA gate
- **THEN** the destination switches to `customer-detail`, and its first batch is released in that mode

#### Scenario: A mode change after first release
- **WHEN** an administrator tries to change the mode of a destination that already has a released batch
- **THEN** the change is refused, because no ratified continuity procedure exists

#### Scenario: The BAA lapses
- **WHEN** a customer-detail destination's BAA lapses
- **THEN** customer-detail delivery halts, pending work is held unconverted, and the mode does not change

### Requirement: AI-10 Customers created by this contract are guarantor accounts and payers, named only in customer-detail mode behind the BAA gate
Customer-detail invoices SHALL be the default posting granularity, replacing the deposit-level journal as the default. Summary-journal mode SHALL exist only as the fallback for destinations whose BAA gate is not satisfied.

The customers created by this contract derive from openPractice medical billing. There SHALL be two kinds:
- **Guarantor accounts**, for patient balances. These include self-pay and direct-pay healthcare charges (FD-4), which are medical billing. Each SHALL be keyed by its opaque guarantor account number.
- **Payers**, for insurance balances. Each SHALL be identified by payer name and payer identifier.

This contract SHALL create customer records only in `customer-detail` mode, which requires a satisfied BAA gate (AI-14). There, a guarantor-account customer MAY carry the guarantor's name, billing address and billing contact, the minimum necessary for billing. Each customer-detail destination SHALL have a versioned recipient profile that lists its permitted customer fields and their purpose, and every customer-detail batch SHALL record the identifier and version of the profile it used. A field beyond name, billing address and billing contact SHALL be sent only after a new profile version documents that field's purpose, recipient and authority. No profile SHALL permit content that AI-16 excludes.

No guarantor or patient name, address or contact SHALL reach a destination whose BAA gate is not satisfied, and `summary-journal` mode SHALL create no customer record (AI-15). This requirement governs only the customers this contract creates. Customer and party roles, and the customer records that other routes create in the same destination, are governed by change `define-customer-financial-data-boundaries` (AI-22).

#### Scenario: Self-guarantor patient after the BAA gate
- **WHEN** a self-pay patient who is their own guarantor is invoiced on a destination in customer-detail mode with a satisfied BAA gate
- **THEN** the destination customer carries the guarantor account number, name, billing address and billing contact, and nothing else about the person

#### Scenario: Destination in summary-journal mode
- **WHEN** the same patient's charge is posted to a destination in summary-journal mode
- **THEN** no customer record is created and no name, address or contact reaches the destination

#### Scenario: A field beyond the default
- **WHEN** a practice wants guarantor customers to carry an extra collections reference
- **THEN** the field is sent only after a new recipient-profile version documents its purpose, recipient and authority, and each batch records that version

#### Scenario: Insurance balance
- **WHEN** a claim-billed visit is invoiced
- **THEN** the invoice is raised to the payer customer, identified by payer name and identifier

### Requirement: AI-11 Accounting dates are the date of service, the finalization date and the statement terms
In customer-detail mode, postings SHALL use these accounting dates:
- every invoice, debit-note and credit-note line SHALL carry its date of service;
- the posting date SHALL be the date of service when that period is open in the destination; otherwise it SHALL be the first open date, the line keeps the date of service, and the posting is flagged as a late charge;
- the invoice date SHALL be the charge-finalization date;
- the due date SHALL come from the statement terms that apply to the customer.

The adapter's recorded or sync timestamp SHALL be stored separately and SHALL NOT be an accounting date.

#### Scenario: Charges finalized two days after the visit
- **WHEN** a visit on November 10 is finalized on November 12 and November is open in the destination
- **THEN** the invoice line carries service date November 10, the posting date is November 10, the invoice date is November 12, and the due date follows the customer's statement terms

### Requirement: AI-12 Accrual valuation books the expected contractual adjustment at the date of service
For a claim-billed visit in customer-detail mode, the system SHALL post, at the date of service:
- the billed charge;
- an expected contractual adjustment, calculated from that payer's observed payment history (the FD-2 learned baseline).

When the remittance arrives, the system SHALL post the difference between the actual and the expected adjustment as a true-up, with a credit note or debit note referencing the original. For a payer without sufficient history, and until learned-baseline data exists, the system SHALL post the gross charge at the date of service and the whole contractual adjustment at remittance.

#### Scenario: Payer with history
- **WHEN** a 150.00 charge is billed to a payer whose learned baseline expects 100.00 allowed, and the remittance allows 104.00
- **THEN** the date of service carries 150.00 billed and a 50.00 expected adjustment, and the remittance posts a 4.00 debit-note true-up

#### Scenario: Payer without history
- **WHEN** the same charge is billed to a payer with no learned baseline
- **THEN** 150.00 posts at the date of service and the full contractual adjustment posts at remittance

### Requirement: AI-13 The contract posts every money event that touches a receivable or a deposit
In customer-detail mode, the contract SHALL define the following postings. Each SHALL keep the deposit entry equal to its bank line.

| Event | Postings |
|---|---|
| A collect-at-visit payment that arrives before any invoice | `payment.post` as an unapplied payment, a customer credit on the guarantor account, later allocated with `payment.apply` |
| A customer credit | Kept as an unapplied credit until it is applied with `payment.apply` or refunded with `refund.post` |
| A chargeback | A `reversal.post` of the original payment, which reopens the receivable, plus a `journal.post` for the chargeback fee |
| A returned item | A `reversal.post` of the original payment, plus a `journal.post` for the bank fee |
| A payer debit or takeback | A `reversal.post`, in whole or in part, of the payment allocation it recovers |
| Provider-level offsets inside a wire | Named `journal.post` lines in the deposit's composition, including interest, forwarding balances and takebacks |
| A virtual-card payment | Booked net of a `journal.post` virtual-card fee line |
| An unidentified receipt identified later | A `journal.post` that releases the unidentified-receipts liability, plus a `payment.post` on the identified customer, linked by `supersedes` |

#### Scenario: Payment collected at the desk before the invoice
- **WHEN** a patient pays 40.00 at check-in and the invoice is finalized two days later
- **THEN** the 40.00 posts as an unapplied payment on the guarantor account, and is applied to the invoice when the invoice posts

#### Scenario: A wire nets a takeback
- **WHEN** a payer wire of 400.00 pays 450.00 on new claims and takes back 50.00 from a prior claim
- **THEN** the deposit entry equals 400.00, and the 50.00 reverses the prior payment allocation with a back-reference

#### Scenario: Chargeback
- **WHEN** a card payment of 75.00 is charged back with a 15.00 fee
- **THEN** the payment is reversed, the receivable reopens, and the 15.00 fee posts to its mapped account, with each debit equal to its explained bank line

### Requirement: AI-14 Customer-detail mode requires a verified BAA or an operator attestation
Customer-detail mode SHALL NOT be enabled for a destination until an authorized person records, and verifies, one of the following:
- an executed HIPAA Business Associate Agreement (BAA) with the destination's vendor;
- for a destination the practice operates itself, such as self-hosted ERPNext, an attestation naming the operator. The operator is either the practice itself or an existing business associate whose BAA covers the accounting site.

The record SHALL capture:
- the counterparty or operator;
- the execution or attestation date;
- effective and renewal or expiry dates;
- a scope statement covering the accounting service;
- an owner-local document reference;
- the verifying person and the verification time.

Enabling customer-detail mode SHALL be a governance tier-4 hard stop. When the BAA expires or is revoked, or the attestation is withdrawn, customer-detail delivery SHALL halt, pending work SHALL be held unconverted, and an authorized person SHALL be given the work. The system MUST NOT assume that any vendor offers or has signed a BAA.

#### Scenario: Self-hosted ERPNext
- **WHEN** a practice running its own ERPNext records an attestation naming itself as the operator, and an authorized person verifies it
- **THEN** the BAA gate is satisfied for that destination

#### Scenario: Nothing recorded
- **WHEN** customer-detail mode is requested with no verified BAA or attestation
- **THEN** it is refused and the destination stays in `summary-journal` mode

### Requirement: AI-15 Summary-journal mode creates no customer records and carries no PHI
In `summary-journal` mode, the contract SHALL post only aggregated journal lines, one group per explained bank line or per day. Each line carries a reason category, a neutral label, an amount, a posting date and a batch reference. The contract SHALL NOT create or update any customer record in this mode. Summary postings MUST NOT contain any of the following:
- customer records or customer names, or account numbers of patients or guarantors;
- member, claim or subscriber identifiers;
- dates of service;
- addresses or contact details;
- any element that identifies an individual or relates a line to one.

#### Scenario: A summary batch is inspected
- **WHEN** a reviewer inspects a summary-journal batch
- **THEN** it holds only reason categories, neutral labels, amounts, dates and batch references, and no customer record

### Requirement: AI-16 Customer-detail postings carry the minimum necessary, with neutral labels
Customer-detail postings created by this contract SHALL contain only:
- the opaque guarantor account number and, for guarantor-account customers, the name, billing address and billing contact (AI-10);
- fields that the batch's recipient profile adds under AI-10;
- payer name and identifier;
- opaque `posting_id` and source references;
- amounts;
- posting, invoice and due dates;
- dates of service on lines;
- neutral financial labels from the schema's fixed list.

A neutral label SHALL NOT reveal the type of service. Under any recipient profile, customer-detail postings MUST NOT contain any of the following:
- chart notes or other clinical content;
- diagnoses, or diagnosis or procedure codes or descriptions;
- medication lists;
- results;
- full claim payloads;
- EHR medical-record numbers;
- service-revealing reason categories or descriptions.

Unless the batch's recipient profile adds the field under AI-10, customer-detail postings also MUST NOT contain:
- the name of any person other than the customer, such as a patient who is not the guarantor;
- dates of birth, or addresses, telephone numbers or email addresses other than the customer's billing address and billing contact;
- government identifiers, member or group identifiers, or payer claim control numbers;
- full bank account or routing numbers.

#### Scenario: A practice has a service-specific revenue category
- **WHEN** an invoice line comes from a reason category whose internal name describes a sensitive service
- **THEN** the line sent to the destination carries the neutral label `patient_services_revenue`, and the internal category name stays out of the posting

#### Scenario: A parent is guarantor for a child's visit
- **WHEN** a child's visit is invoiced to a parent's guarantor account in customer-detail mode
- **THEN** the customer carries the parent's name and billing contact, and no customer field or line carries the child's name

#### Scenario: A recipient profile lists a diagnosis
- **WHEN** a proposed recipient-profile version lists a diagnosis field
- **THEN** the version is refused, and no posting carries a diagnosis

### Requirement: AI-17 Revenue bank lines are read from the destination, with statement upload as a fallback
The adapter SHALL read revenue-related bank lines from the destination with `bank_lines.read`: deposits, chargebacks, payer takebacks and processor fees. Where the destination cannot supply them, an uploaded bank statement SHALL be the fallback. Lines outside those categories SHALL be labelled "accounting system's" and SHALL NOT be reconciled. Each explained deposit SHALL produce exactly one `deposit.post`, equal to the bank line, composed of clearing-account releases and named lines.

#### Scenario: Net card settlement
- **WHEN** a card settlement of 97.00 funds receipts of 100.00 posted at gross, with a 3.00 fee
- **THEN** one deposit entry equal to 97.00 releases 100.00 from clearing and posts the 3.00 fee to its mapped account

### Requirement: AI-18 Integration credentials are protected and least-privileged
Each destination's credentials SHALL be stored in the platform's secret store and scoped to the objects that the contract operations need. Each credential SHALL be recorded with an owner and an expiry date, and work SHALL be raised before it expires. Credentials SHALL be kept out of logs, fixtures, documentation and commits. The system MUST NOT request or use a destination permission that can originate a payment, where the destination offers that permission separately.

#### Scenario: Credential nears expiry
- **WHEN** a destination credential is within its warning window before expiry
- **THEN** work is raised for its owner before delivery fails

### Requirement: AI-19 No PHI in logs, errors, diagnostics or rejection reasons
Logs, error messages, diagnostic bundles and rejection or exception reasons from the accounting integration MUST NOT contain:
- names, billing addresses, billing contacts or guarantor account numbers;
- dates of service tied to an account;
- full bank or routing numbers;
- credentials;
- clinical content.

They SHALL reference postings only by `posting_id`, batch sequence, and destination.

#### Scenario: A delivery fails
- **WHEN** a posting fails and a diagnostic bundle is generated
- **THEN** the bundle names the `posting_id`, batch sequence, destination and a coded error, and contains no personal data or credential

### Requirement: AI-20 Each destination is verified before go-live
Before any destination goes live, its adapter SHALL verify and record whether that destination supports two things:
- a service date on each invoice line;
- cash-basis reporting over the postings this contract makes.

A destination that cannot carry line service dates SHALL NOT go live in customer-detail mode until the owner decides how to proceed.

#### Scenario: ERPNext verification
- **WHEN** the ERPNext adapter is prepared for go-live
- **THEN** its verification record states, with evidence, whether line service dates and cash-basis reporting are supported in the pinned ERPNext version

### Requirement: AI-22 The contract never copies care linkage into records it does not own and never creates a second collectible balance
This contract SHALL post only to customers it creates (AI-10), and only obligations that arise from openPractice medical billing. It MUST NOT copy care linkage or PHI into a customer record it does not own. Care linkage means patient, guarantor, visit, claim, coverage or clinical references. A record it does not own includes a membership or retail customer that another route created in the same destination.

Membership and retail obligations SHALL be owned by the routes that change `define-customer-financial-data-boundaries` defines, and this contract MUST NOT create a second collectible balance for any of them. Each obligation SHALL have one authoritative owner and one posting route, so that no obligation, collection or revenue is duplicated across routes. Where such a route sends accounting effects through openPractice, it SHALL use this contract's posting mechanics (AI-01), and a customer kind or operation that `1.x` lacks SHALL be added only by a governed change, as a new MINOR version. Self-pay and direct-pay healthcare charges (FD-4) are medical billing, and this contract SHALL post them.

#### Scenario: A person is also a commerce customer
- **WHEN** a guarantor also exists in the destination as a membership customer created by another route
- **THEN** this contract posts the guarantor's medical charges to its own guarantor-account customer, adds nothing to the other record, and no obligation appears under both

#### Scenario: A membership renewal falls due
- **WHEN** a patient's membership subscription renews
- **THEN** the renewal obligation stays with the route that owns it, and this contract creates no receivable for it

#### Scenario: A cash-pay visit
- **WHEN** a self-pay patient is charged for a visit under FD-4
- **THEN** this contract posts the charge to the guarantor-account customer as medical billing

### Requirement: AI-21 The accounting boundary is covered by contract tests
Each adapter SHALL pass contract tests covering:
- every operation;
- idempotent replay and indeterminate-outcome lookup;
- `supersedes` handling;
- batch freezing and release refusals;
- the mode lock;
- dates and late-charge flagging;
- accrual true-up;
- the AI-13 events;
- reconciliation;
- the absence of PHI in keys and diagnostics;
- the AI-10 and AI-16 customer-field rules: the BAA gate, the recipient-profile version and the exclusions;
- the absence of customer records in summary-journal mode (AI-15);
- the AI-22 rules against posting to records the contract does not own and against a second collectible balance.

The tests SHALL run against a contract double or a disposable destination instance loaded with synthetic data only. Tests MUST NOT depend on live vendor services or real PHI.

#### Scenario: CI runs the adapter suite
- **WHEN** CI runs the ERPNext adapter's contract tests
- **THEN** they pass against a contract double or a disposable synthetic instance, reached over REST, with no access to a vendor's production service
