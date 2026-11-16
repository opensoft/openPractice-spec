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
The canonical schema SHALL be `openpractice-accounting-integration-1.0.schema.json` in this change's `contracts/` directory, identified by the SHA-256 digest of its bytes. It defines batch content, postings, summary lines, bank-line read results and recipient-profile documents (AI-23). Every adapter SHALL validate batch content against the schema with the recorded digest.

Its amounts follow BAB-18 (ruling 103): decimal text with two places and an ISO 4217 code, USD only in 1.0, which the schema enforces, one currency per batch, and one currency per bank-lines result, which carries its `currency` once. Its rounding, including how a fixed amount is split, is BAB-18's one rule (design call F-9, PROPOSED), which this contract adopts as its statement of rounding.

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

A batch SHALL be released only by an explicit release decision of an authorized signer: a named human or, where that action type is opted in, a named AI agent identified by an openWallet ID (BAB-17). Scheduled automatic release MUST NOT be enabled by default. Release SHALL freeze the content. Release SHALL be refused in each of these cases:
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
After each delivery, and on demand as of any date, the system SHALL produce a reconciliation report for a destination. The report SHALL compare openPractice totals with the destination's totals for documents carrying openPractice keys: by reason category; in summary-journal mode also by service category, on lines that are revenue or a deduction from revenue (design call F-14, PROPOSED); and in customer-detail mode also by customer. In `summary-journal` mode it SHALL also prove, for each payer-class control account, that openPractice's subledger total equals the destination's control-account balance as of the same cut-off, and that the gross-to-net allowance equals the sum of openPractice's current estimates. It SHALL list missing documents, unexpected documents, amount mismatches and destination-side edits. It SHALL state its exclusions: unidentified receipts held as liabilities, bank lines labelled "accounting system's", legacy collections, and other-route shares of explained deposits (BAB-14). Each exception SHALL become work owned by a Roster entry.

#### Scenario: The mirror ties
- **WHEN** every posting of a delivered batch is posted and nothing was edited in the destination
- **THEN** the report shows zero difference and lists its exclusions

#### Scenario: A control account drifts
- **WHEN** the Medicare-class receivable control account in a summary-journal destination differs from openPractice's subledger total at the cut-off
- **THEN** the report shows the difference for that payer class and creates work, and nothing is corrected automatically

#### Scenario: A mirrored payment was deleted in the destination
- **WHEN** a bookkeeper deletes an openPractice-posted payment in the destination
- **THEN** the report lists it as missing and creates work

### Requirement: AI-09 Posting mode is chosen before a destination's first released batch
Each destination SHALL have a posting mode, `customer-detail` or `summary-journal`, and each batch SHALL record its mode. Every destination SHALL be created in `summary-journal` mode, the MVP default and recommended setup (ruling 28). `customer-detail` SHALL be a per-destination opt-in: a destination MAY move to it only before its first released batch, and only after its BAA gate is satisfied. After a destination's first released batch, the system SHALL refuse every change of its posting mode, until the owner ratifies a continuity procedure that defines the opening receivable true-up. A BAA lapse SHALL halt customer-detail delivery, and MUST NOT silently convert any work to summary-journal mode.

#### Scenario: Mode chosen before go-live
- **WHEN** an administrator enables customer-detail mode on a destination that has no released batch and has a satisfied BAA gate
- **THEN** the destination switches to `customer-detail`, and its first batch is released in that mode

#### Scenario: A mode change after first release
- **WHEN** an administrator tries to change the mode of a destination that already has a released batch
- **THEN** the change is refused, because no ratified continuity procedure exists

#### Scenario: The BAA lapses
- **WHEN** a customer-detail destination's BAA lapses
- **THEN** customer-detail delivery halts, pending work is held unconverted, and the mode does not change

### Requirement: AI-10 Summary-journal is the default; customers exist only in opt-in customer-detail mode, as guarantor accounts and payers named behind the BAA gate
`summary-journal` SHALL be the default and recommended posting mode (ruling 28). It posts balanced category totals to payer-class control accounts, and patient-level receivable detail stays in openPractice. `customer-detail` SHALL be a per-destination opt-in, available only after the destination's BAA gate is satisfied and only before its first released batch. A destination SHALL stay in the mode its first released batch used (AI-09). The customers this requirement describes exist only in `customer-detail` mode (rulings 2, 25 and 28). Each SHALL be derived from the guarantor or payer role of a party, and SHALL NOT be a separate party role (ruling 32).

The customers created by this contract derive from openPractice medical billing. There SHALL be two kinds:
- **Guarantor accounts**, for patient balances. These include self-pay and direct-pay healthcare charges (FD-4), which are medical billing. Each SHALL be keyed by its opaque guarantor account number.
- **Payers**, for insurance balances. Each SHALL be identified by payer name and payer identifier.

This contract SHALL create customer records only in `customer-detail` mode, which requires a satisfied BAA gate (AI-14). There, a guarantor-account customer MAY carry the guarantor's name, billing address and billing contact, the minimum necessary for billing. Where any confidential-communication restriction (PR-05) applies to the guarantor's party, including one that crosses a same-person link or is kept after a link ends (PR-15), the customer SHALL carry no billing address, no billing contact and no profile field that holds an address, a telephone number or an email address; its account number and name may still be carried. When such a restriction starts to apply after the destination has received an address or contact for that customer, openPractice SHALL raise work, owned by a roster entry, to remove them from the destination (design call F-11, PROPOSED). Each customer-detail destination SHALL have a versioned recipient profile, in the single shape AI-23 defines, that lists its permitted customer fields with their purpose, recipient and authority, and every customer-detail batch SHALL record the identifier and version of the profile it used. A field beyond name, billing address and billing contact SHALL be sent only after a new profile version documents that field's purpose, recipient and authority. No profile SHALL permit content that AI-16 excludes.

No guarantor or patient name, address or contact SHALL reach a destination whose BAA gate is not satisfied, and `summary-journal` mode SHALL create no customer record (AI-15). This requirement governs only the customers this contract creates. Customer and party roles, and the customer records that other routes create in the same destination, are governed by change `define-customer-financial-data-boundaries` (AI-22).

#### Scenario: Self-guarantor patient after the BAA gate
- **WHEN** a self-pay patient who is their own guarantor is invoiced on a destination in customer-detail mode with a satisfied BAA gate
- **THEN** the destination customer carries the guarantor account number, name, billing address and billing contact, and nothing else about the person

#### Scenario: A guarantor with a mail restriction
- **WHEN** a guarantor whose party carries `no_home_mail` is invoiced on a destination in customer-detail mode
- **THEN** the customer carries the guarantor account number and name, and no billing address or contact, so the destination holds no channel the restriction bars

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
In both posting modes, postings SHALL use these accounting dates:
- in `customer-detail` mode, every invoice, debit-note and credit-note line SHALL carry its date of service; in `summary-journal` mode, charge totals SHALL be grouped by date of service, subject to AI-15's minimum group size, and carry no per-visit date;
- the posting date of a charge posting (an invoice, debit note or credit note, or a summary-journal charge total) SHALL be the date of service when that period is open in the destination; otherwise it SHALL be the first open date, the line keeps the date of service, and the posting is flagged as a late charge;
- the posting date of a payment, payment application, refund, reversal, deposit or journal posting SHALL be its own event date: the receipt date of a payment, the application date of an application, the issue date of a refund, and the bank line's posted date for a deposit or other bank-line event. The same closed-period rule applies: when that period is closed, the posting date SHALL be the first open date and the posting is flagged with the same flag;
- a re-estimate made at a close (AI-12) SHALL be dated in the period being closed when that period is open in the destination, and otherwise on the first open date;
- the invoice date SHALL be the charge-finalization date;
- the due date SHALL come from the statement terms that apply to the customer.

The adapter's recorded or sync timestamp SHALL be stored separately and SHALL NOT be an accounting date.

#### Scenario: Charges finalized two days after the visit
- **WHEN** a visit on November 10 is finalized on November 12 and November is open in the destination
- **THEN** the invoice line carries service date November 10, the posting date is November 10, the invoice date is November 12, and the due date follows the customer's statement terms

#### Scenario: A payment arrives weeks after the visit
- **WHEN** a payment for a November 10 visit is received on December 3 and December is open in the destination
- **THEN** the payment posts with posting date December 3, not the date of service

### Requirement: AI-12 Net revenue is the constrained expected entitlement, estimated at the date of service and re-estimated at each close
In both posting modes, the system SHALL recognize net revenue as the constrained expected entitlement (ruling 30, superseding ruling 22). At the date of service it SHALL post the gross charge as a gross line together with its gross-to-net deduction, so that the net equals the constrained expected entitlement. The estimate SHALL come from a versioned estimator, using fee-schedule or contract terms, the payer's observed adjudication history (the FD-2 learned baseline), portfolio collection experience and any approved overlay. It SHALL be constrained so that a significant reversal is not probable. A payer with no history SHALL receive a conservative constrained estimate; there SHALL be no gross-then-adjust fallback.

Billing, adjudication, remittance, cash and disputes SHALL be evidence events that update the estimate, never revenue triggers. Each re-estimate or true-up, whether from evidence or from the estimate run at each close, SHALL be a new dated entry referencing the estimate version or close snapshot it applies, and MUST NOT edit an earlier entry (FD-3). A disputed amount SHALL stay out of the recognized amount until including it meets the constraint. Price concessions SHALL reduce revenue, while the later non-payment of an amount that was expected to be collected SHALL post as a credit loss, separately from revenue.

Every estimate SHALL record its estimator version, input window, cohort and evidence references in openPractice. Close re-estimates SHALL post automatically from the approved estimator version, with no approval step at close (FD-3: month-end is printing, not a procedure), and each close SHALL store a reproducible snapshot automatically. Only estimator versions and the D8 policy items SHALL need approval. The D8 approval and every estimator-version approval SHALL be given by the practice's accountant only, the outside-accountant roster entry of BAB-17 (ruling 141), and neither SHALL be opted in for agent signing (BAB-17). Neither SHALL be self-approved (`define-practice-readiness` SR-04).

#### Scenario: Payer with history
- **WHEN** a 200.00 charge is captured for a payer whose fee schedule allows 120.00 and whose history supports a constrained expected entitlement of 114.00
- **THEN** the date of service carries a 200.00 gross line and an 86.00 gross-to-net deduction referencing the estimate version, and net revenue is 114.00

#### Scenario: Adjudication changes the estimate
- **WHEN** the payer later denies 12.00 of a claim estimated at 114.00, and the denial is disputed
- **THEN** a new dated entry lowers the estimate by 6.00 to exclude the disputed amount, and no earlier entry changes

#### Scenario: Payer without history
- **WHEN** a charge is captured for a payer with no adjudication history
- **THEN** the date of service carries the gross line and a deduction to a conservative constrained estimate, and nothing waits for remittance

#### Scenario: A period closes before settlement
- **WHEN** the estimate run at a close changes the expected entitlement of open balances
- **THEN** the difference posts automatically, with no approval step, as a new entry dated in the closing period if it is open, otherwise on the first open date, referencing the close snapshot

#### Scenario: A new estimator version is proposed
- **WHEN** automation proposes a new estimator version
- **THEN** it applies only after the practice's accountant approves it, from a stated effective date

### Requirement: AI-13 The contract posts every money event that touches a receivable or a deposit
In customer-detail mode, the contract SHALL define the following postings. Each SHALL keep the deposit entry equal to its bank line. The payer-debit and provider-level-offset rows as revised, and every row after the one for an unidentified receipt identified later, are design call F-19, PROPOSED.

| Event | Postings |
|---|---|
| A collect-at-visit payment that arrives before any invoice | `payment.post` as an unapplied payment, a customer credit on the guarantor account, later allocated with `payment.apply` |
| A customer credit | Kept as an unapplied credit until it is applied with `payment.apply` or refunded with `refund.post` |
| A chargeback | A `reversal.post` of the original payment, which reopens the receivable, plus a `journal.post` for the chargeback fee |
| A returned item | A `reversal.post` of the original payment, plus a `journal.post` for the bank fee |
| A payer debit or takeback, except a takeback of an overpayment already owed back (below) | A `reversal.post`, in whole or in part, of the payment allocation it recovers |
| Provider-level offsets inside a wire | Named `journal.post` lines in the deposit's composition, including interest and forwarding balances; a takeback in the wire is the posting its own row states, the payer-debit row's `reversal.post` or, for an overpayment already owed back, the `journal.post` against refund liability, shown in the deposit's composition; where the remittance moves no money, these postings are made on their own and net to zero in clearing |
| A virtual-card payment | Booked net of a `journal.post` virtual-card fee line |
| An unidentified receipt identified later | A `journal.post` that releases the unidentified-receipts liability, plus a `payment.post` on the identified customer, both linked by `applies_to` to the original unidentified-receipt posting. `supersedes` is reserved for replacing a posting. |
| A prepayment that will not be applied | No posting where the destination's customer-detail mapping sends `prepayment` and `patient_payment_unapplied` to the same account, because the prepayment is already a customer credit; otherwise a `journal.post` with label `unapplied_payment` that moves the amount from the first account to the second |
| A payment above what a charge line now owes | A `reversal.post` of that part of the payment's `payment.apply`, leaving it as the customer's unapplied credit |
| A balance moved to another guarantor account | A `credit_note.post` on the first customer's invoice and an `invoice.post` to the second customer, both with label `patient_responsibility_transfer` and linked by `applies_to` |
| A balance passed to the payer in the next coverage position | A `credit_note.post` on the earlier payer's invoice and an `invoice.post` to the later payer, both with label `payer_responsibility_transfer` and linked by `applies_to` |
| An unidentified receipt identified as a payer's payment | The remittance's own postings follow their own rows, as if its money had arrived with it: the payer's payment as a `payment.post` on the payer, a payer debit or takeback as its row states, and provider-level offsets as named `journal.post` lines, made on their own because each receipt's deposit entry is already posted. Each receipt linked to the remittance is released by a `journal.post` with label `unidentified_receipt_reclassified`, from the unidentified-receipts liability to clearing, linked by `applies_to` to that receipt's original unidentified-receipt posting and never more than remains of it. A remittance may release from several receipts: its releases total its payment total, or all that remains of its receipts where they hold less, so the receipts clear exactly whatever payer-level adjustments the remittance nets; where the linked receipts hold more than the payment total, the person who makes the links names the amount released from each, and what remains stays on its receipt, in the unidentified-receipts liability. A release is made with the remittance's postings or, for a receipt linked after the remittance has posted, at the link. Where the receipts hold less than the payment total, the shortfall stays in clearing for its bank line, or becomes work where that bank line was already explained. Each deposit entry stays the one posted for its receipt, equal to its bank line, and nothing of the remittance is matched to a bank line again, so no cash is counted twice (design call F-23, PROPOSED) |
| A takeback that recovers an overpayment already owed back to the payer | A `journal.post` against the refund-liability account, with label `payer_takeback`, and never a `reversal.post`, which would reopen the receivable in the destination |
| A bank debit that openPractice has matched | Only where the destination's record under AI-20 says openPractice sends them: a `journal.post` releasing the clearing account, with label `bank_debit_clearing_release` |
| A write-off against the allowance | No posting where the gross-to-net credit notes holding the amount are on the same customer's invoice (self-pay, and every payer-side write-off, including one of an amount still unbilled because no claim for it was accepted, since the payer's invoice carries it from finalization: design call F-25, PROPOSED); where they are on the payer's invoice and the write-off is of the patient's share, a `credit_note.post` on the guarantor's invoice and a `debit_note.post` on the payer's invoice, both with label `write_off_against_allowance`, linked by `applies_to` |
| A balance written off as a credit loss, under the allowance method or the direct method | A `credit_note.post` on the guarantor's invoice, with label `credit_loss`; for a receivable that has no invoice, such as a refunded payment later charged back (`define-revenue-cycle-core` PY-09, a draft not yet on `main`), a `journal.post` with label `credit_loss` for that customer, linked by `applies_to` to the `reversal.post` that reopened it; the provision and its reversal stay `journal.post` lines |
| A claim accepted | No posting: the payer's invoice already carries the claim-billed charges from finalization |
| A contractual adjustment realized against the allowance | No posting: the gross-to-net credit notes already took the expected adjustment off the payer's invoice, and any difference posts as a `gross_to_net_true_up` note |
| A patient credit moved to refund liability | No posting: it stays a customer credit until `refund.post` |

#### Scenario: Payment collected at the desk before the invoice
- **WHEN** a patient pays 40.00 at check-in and the invoice is finalized two days later
- **THEN** the 40.00 posts as an unapplied payment on the guarantor account, and is applied to the invoice when the invoice posts

#### Scenario: A wire nets a takeback
- **WHEN** a payer wire of 400.00 pays 450.00 on new claims and takes back 50.00 from a prior claim
- **THEN** the deposit entry equals 400.00, and the 50.00 reverses the prior payment allocation with a back-reference

#### Scenario: The money for a remittance that nets a takeback arrived first
- **WHEN** a 400.00 payer EFT was explained as an unidentified receipt, its deposit entry posted equal to the bank line, and the remittance then arrives paying 450.00 on new claims and taking back 50.00 from a prior claim
- **THEN** the 450.00 posts as a `payment.post` on the payer, the 50.00 reverses the prior payment allocation, and one `journal.post` releases 400.00 from the unidentified-receipts liability, linked by `applies_to` to the original posting; clearing and the liability both end at zero, no second deposit entry is posted, and releasing 450.00 instead would leave 50.00 in each

#### Scenario: Two deposits for one remittance
- **WHEN** deposits of 600.00 and 400.00 were each explained as an unidentified receipt, each with its deposit entry equal to its bank line, and a person links both to one remittance that pays 1000.00
- **THEN** the 1000.00 posts as a `payment.post` on the payer, and two `journal.post` releases of 600.00 and 400.00 move the money from the unidentified-receipts liability to clearing, each linked by `applies_to` to its own receipt's posting; clearing and the liability both end at zero, and no further deposit entry is posted

#### Scenario: A receipt linked after its remittance posted
- **WHEN** a 1000.00 remittance posted with no receipt linked, so its `payment.post` left 1000.00 in clearing, and a person then links to it a 600.00 deposit explained earlier as an unidentified receipt
- **THEN** a `journal.post` releases 600.00 at the link, linked by `applies_to` to that receipt's posting, and the other 400.00 stays in clearing for its bank line, whose deposit entry clears it when that line is explained; had that bank line already been explained, the 400.00 would be work

#### Scenario: Chargeback
- **WHEN** a card payment of 75.00 is charged back with a 15.00 fee
- **THEN** the payment is reversed, the receivable reopens, and the 15.00 fee posts to its mapped account, with each debit equal to its explained bank line

### Requirement: AI-14 Customer-detail mode requires a verified BAA or an operator attestation
Customer-detail mode SHALL NOT be enabled for a destination until an authorized person, who is the practice manager (ruling 142), records, and verifies, one of the following:
- an executed HIPAA Business Associate Agreement (BAA) with the destination's vendor;
- for a destination the practice operates itself, such as self-hosted ERPNext, an attestation naming the operator. The operator is either the practice itself or an existing business associate whose BAA covers the accounting site.

The record SHALL capture:
- the counterparty or operator;
- the execution or attestation date;
- effective and renewal or expiry dates;
- a scope statement covering the accounting service;
- an owner-local document reference;
- the verifying person, the role in which they verified it (ruling 142), and the verification time.

The record SHALL be kept in openPractice's accounting destination register. It is the single BAA-gate record for that destination, and every route that writes PHI-bearing customer records into the destination SHALL consult it, including routes that change `define-customer-financial-data-boundaries` defines.

Enabling customer-detail mode SHALL be a governance tier-4 hard stop. When the BAA expires or is revoked, or the attestation is withdrawn, customer-detail delivery SHALL halt, pending work SHALL be held unconverted, and the practice manager SHALL be given the work (ruling 142). The system MUST NOT assume that any vendor offers or has signed a BAA.

#### Scenario: Self-hosted ERPNext
- **WHEN** a practice running its own ERPNext records an attestation naming itself as the operator, and the practice manager verifies it
- **THEN** the BAA gate is satisfied for that destination

#### Scenario: Nothing recorded
- **WHEN** customer-detail mode is requested with no verified BAA or attestation
- **THEN** it is refused and the destination stays in `summary-journal` mode

### Requirement: AI-15 Summary-journal mode posts balanced category totals to payer-class control accounts, creates no customer records and carries no PHI
`summary-journal` mode, the default (ruling 28), SHALL post balanced category totals to payer-class control accounts, summarized by period, payer class, benign service category and account. Patient-level receivable detail SHALL stay in openPractice, in a detail manifest from which every summary line can be traced. The summary SHALL cover:
- services performed: a gross line with its gross-to-net deduction at the constrained estimate (AI-12);
- claim acceptance by the payer or clearinghouse: reclassification from unbilled to billed receivable, with no revenue effect (ruling 165); a rejection reverses any reclassification an earlier acceptance of the same claim made (ruling 165), and otherwise causes none; the acceptance of a replacement reclassifies only the difference from what the claim has already reclassified, net of reversals, and an accepted void reverses what it has reclassified; a claim to a later coverage position causes none (design call F-20, PROPOSED);
- adjudication: the contractual adjustment realized against the allowance, and any estimate difference trued up;
- payer and patient payments: cash clearing against the payer-class or patient receivable;
- patient-share and payer-share transfers between control accounts, with no revenue effect (payer-share transfers: design call F-15, PROPOSED);
- disputes, credit losses, refund liabilities and reversals, each as a new entry.

The practice's accountant SHALL confirm the claim-acceptance treatment (ruling 165), as part of the receivable classification they approve under AI-20 (design call F-20, PROPOSED).

Each line SHALL carry a reason category, a neutral label, an amount, a posting date and a batch reference; SHALL carry a payer class where it touches a receivable or revenue; and SHALL carry its benign service category and its group level where it is revenue or a deduction from revenue, and a service category on no other line (ruling 125; its details are design call F-14, PROPOSED). A payer-responsibility-transfer line SHALL carry the payer class that receives the amount as its payer class and the payer class that gives it up as its counter payer class, and no other line SHALL carry a counter payer class; its amount SHALL be greater than zero, so a reversed payer-share transfer SHALL be sent as a new payer-responsibility-transfer line with its two classes swapped (design call F-15, PROPOSED). A patient-responsibility-transfer, chargeback, contractual-adjustment, payment-application, payment-received, returned-item, unbilled-to-billed or write-off-against-allowance line SHALL always carry its payer class, because it always touches a receivable; a credit-loss, payer-takeback, refund-liability, unapplied-payment or unidentified-receipt-reclassified line SHALL carry it where its reason category touches a receivable (design call F-17, PROPOSED). A write-off-against-allowance line of an amount still unbilled, because no claim for it was accepted, carries the reason category `unbilled_write_off_against_allowance` of `define-revenue-cycle-core`, a draft not yet on `main` (its design call CR-101), with its payer class, and the destination's summary-journal mapping (BAB-06) SHALL map that reason category to the unbilled receivable control account of the line's payer class, never to the billed one (design call F-25, PROPOSED). A gross-to-net line, meaning a patient-services revenue, gross-to-net deduction or gross-to-net true-up line, SHALL carry the estimate version or close snapshot it applies, by reference (design call F-14, PROPOSED). Each batch SHALL record the version of the practice's whole effective-dated service-category mapping that it used (design call F-14, PROPOSED). Deposits SHALL be grouped per explained bank line. The contract SHALL NOT create or update any customer record in this mode. Summary postings MUST NOT contain any of the following:
- customer records or customer names, or account numbers of patients or guarantors;
- member, claim or subscriber identifiers;
- a per-visit or per-person date of service;
- addresses or contact details;
- any element that identifies an individual or relates a line to one.

Each destination SHALL have a minimum group size, which defaults to 11 underlying visits. A day-level group for a payer class and category that falls below it SHALL roll up to the week. If the week group is still below it, the group SHALL roll up to the accounting period. A rolled-up group SHALL record its group level and SHALL NOT cross an accounting-period boundary. A week that spans a period end SHALL be split at the boundary, and each part SHALL be re-tested against the minimum group size within its own period. A part that meets the minimum SHALL be dated on its last day inside its own period. A part still below the minimum SHALL roll up to its own period, dated on that period's last day. When that date falls in a period the destination has closed, the group SHALL be dated on the first open date and flagged late (AI-11). Day-level detail SHALL stay in openPractice's detail manifest.

#### Scenario: A claim rejected after the clearinghouse accepted it
- **WHEN** a claim that the clearinghouse accepted is then rejected by the payer, and its corrected replacement is accepted
- **THEN** the summary reverses the reclassification the first acceptance made, and the replacement's acceptance reclassifies the amount once, so the billed receivable is not booked twice

#### Scenario: A summary batch is inspected
- **WHEN** a reviewer inspects a summary-journal batch
- **THEN** it holds no customer record, customer name, account number, member, claim or subscriber identifier, per-visit or per-person date of service, or anything else AI-15 says summary postings MUST NOT contain; any service category is a benign code

#### Scenario: A day's charges are posted
- **WHEN** a practice captures twenty Medicare-class office visits on one day
- **THEN** one balanced day-level group posts the gross total and its deduction to the Medicare-class control accounts, dated that day, and the twenty visits stay in openPractice's detail manifest

#### Scenario: A small day rolls up
- **WHEN** a practice captures three commercial-class office visits on one day and fewer than eleven in that week
- **THEN** no day-level or week-level group posts for them; they roll up to the period-level group for that payer class and category, and the day-level detail stays in the manifest

#### Scenario: A week crosses a month end
- **WHEN** a week from October 28 to November 3 has 12 commercial-class office visits in October and 4 in November, with fewer than eleven on any day
- **THEN** the week splits at October 31: the October part posts as a week-level group dated October 31, the November part rolls up to November's period-level group dated November 30, and no October revenue is dated in November

#### Scenario: A partial payer-share reversal
- **WHEN** 14.00 of a payer-share transfer from the Medicare class to a Medigap plan's class is reversed
- **THEN** it is sent as a new payer-responsibility-transfer line of 14.00, with the Medicare class as its payer class and the Medigap class as its counter payer class, and never as a negative line

### Requirement: AI-16 Customer-detail postings carry the minimum necessary, with neutral labels
Customer-detail postings created by this contract SHALL contain only:
- the opaque guarantor account number and, for guarantor-account customers, the name, billing address and billing contact, except where AI-10 withholds the address and contact (AI-10);
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
- service-revealing reason categories or descriptions;
- full bank account or routing numbers (BAB-10).

Unless the batch's recipient profile adds the field under AI-10, customer-detail postings also MUST NOT contain:
- the name of any person other than the customer, such as a patient who is not the guarantor;
- dates of birth, or addresses, telephone numbers or email addresses other than the customer's billing address and billing contact;
- government identifiers, member or group identifiers, or payer claim control numbers;
- a payer relationship on a guarantor-account customer, which the schema admits only as a profile field.

#### Scenario: A practice has a service-specific revenue category
- **WHEN** an invoice line comes from a reason category whose internal name describes a sensitive service
- **THEN** the line sent to the destination carries the neutral label `patient_services_revenue`, and the internal category name stays out of the posting

#### Scenario: A parent is guarantor for a child's visit
- **WHEN** a child's visit is invoiced to a parent's guarantor account in customer-detail mode
- **THEN** the customer carries the parent's name and billing contact, and no customer field or line carries the child's name

#### Scenario: A recipient profile lists a diagnosis
- **WHEN** a proposed recipient-profile version lists a diagnosis field
- **THEN** the version is refused, and no posting carries a diagnosis

#### Scenario: A recipient profile lists a bank account number
- **WHEN** a proposed recipient-profile version lists a refund bank account or routing number field
- **THEN** the version is refused, and no posting carries a full bank account or routing number

### Requirement: AI-17 Revenue bank lines are read from the destination, with statement upload as a fallback
The adapter SHALL read revenue-related bank lines from the destination with `bank_lines.read`: deposits, chargebacks, payer takebacks, processor fees and refund disbursements. Where the destination cannot supply them, an uploaded bank statement SHALL be the fallback. Lines outside those categories SHALL be labelled "accounting system's" and SHALL NOT be reconciled. Each explained deposit SHALL produce exactly one `deposit.post`, equal to the bank line, composed of clearing-account releases and named lines, including an `other_route_share` line for any share that funds another route's balances (BAB-14). Each refund disbursement line SHALL be matched to the issued refund recorded with `refund.post` (BAB-07).

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

A destination that cannot carry line service dates SHALL NOT go live in customer-detail mode until the product owner decides how to proceed.

No destination SHALL go live until the practice's accountant has approved, and openPractice has recorded, the reporting basis, the estimator, the receivable classification, the materiality approach and the reopening policy (ruling 30). The D8 approval and every estimator-version approval SHALL be given by the practice's accountant only, the outside-accountant roster entry of BAB-17 (ruling 141), and neither SHALL be opted in for agent signing (BAB-17). Neither SHALL be self-approved (`define-practice-readiness` SR-04).

No destination SHALL go live, and openPractice SHALL NOT send a summary that needs a service category, a counter payer class or one of the three labels the amendment of 2026-11-15 adds, while the contract version it sends under cannot carry them (ruling 164).

Before any destination goes live, in either posting mode, openPractice SHALL also record, per destination, who books the bank debits that openPractice explains: either openPractice sends explained-bank-debit lines, with label `bank_debit_clearing_release`, or the destination books bank debits from its own bank feed and codes each against the account mapped to `cash_clearing`. The practice manager SHALL record it, keeping the role used, and the practice's accountant SHALL confirm it, beside the D8 record; that confirmation is part of accounting policy, within what ruling 141 lets the outside accountant approve (ruling 197). The record, and every change to it, SHALL take effect only once the accountant confirms it; until then the last confirmed record stands, and where none was ever confirmed there is none. Until a confirmed record exists, no explained-bank-debit line SHALL be sent. If a confirmed change makes openPractice the sender, only debits explained after the change's effective date SHALL be sent, and that date is never earlier than the accountant's confirmation (design call F-18, PROPOSED).

#### Scenario: ERPNext verification
- **WHEN** the ERPNext adapter is prepared for go-live
- **THEN** its verification record states, with evidence, whether line service dates and cash-basis reporting are supported in the pinned ERPNext version

#### Scenario: The accountant has not approved the policy
- **WHEN** a destination is otherwise ready but no accountant approval of the revenue-recognition policy is recorded
- **THEN** go-live is refused

#### Scenario: No record of who books the bank debits
- **WHEN** a destination is otherwise ready, but no confirmed record says who books the bank debits openPractice explains
- **THEN** go-live is refused, and no `bank_debit_clearing_release` line is sent to it

#### Scenario: A change to the bank-debit record that the accountant has not confirmed
- **WHEN** the practice manager changes a destination's confirmed record from the destination booking bank debits from its own feed to openPractice sending them, and the accountant has not yet confirmed the change
- **THEN** the last confirmed record stands and no `bank_debit_clearing_release` line is sent; once the accountant confirms, only debits explained after the change's effective date, never earlier than the confirmation, are sent

#### Scenario: Someone other than the accountant tries to approve
- **WHEN** a named AI agent, or a person who is not the practice's accountant, tries to approve the D8 policy or an estimator version
- **THEN** the approval is refused, whatever the agent opt-in settings, and the policy or version stays unapproved

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

### Requirement: AI-23 One recipient-profile definition serves every projection
This contract SHALL own the only recipient-profile definition, the `recipientProfileDocument` shape in the canonical schema (ruling 26). Each version SHALL record:
- its identifier and version, and its effective dates;
- the recipient: an accounting destination, an organization or an actor role;
- the purpose, the relationship and row scope, the source provenance, and the sensitivity;
- the output channels and handling obligations;
- every permitted field, each with its purpose, recipient and authority.

A changed field list SHALL require a new version. No version SHALL permit content that AI-16 bars under any recipient profile. Change `define-customer-financial-data-boundaries` SHALL use this definition for its business outputs and SHALL NOT define a second shape.

#### Scenario: A business-output profile is defined
- **WHEN** an accountant's financial view is authorized under `define-customer-financial-data-boundaries`
- **THEN** its profile is a version of the same shape, validated against the canonical schema, with each field's purpose, recipient and authority

### Requirement: AI-21 The accounting boundary is covered by contract tests
Each adapter SHALL pass contract tests covering:
- every operation;
- idempotent replay and indeterminate-outcome lookup;
- `supersedes` handling;
- batch freezing and release refusals;
- the mode lock;
- dates and late-charge flagging, including close re-estimate dating;
- constrained-estimate revenue, evidence-driven true-ups and the absence of any gross-then-adjust fallback;
- summary-journal control-account reconciliation by payer class;
- the service category on every summary-journal line that is revenue or a deduction from revenue, its absence from every other line and from every customer-detail posting, the counter payer class on every payer-responsibility-transfer line and on no other line, a payer class on every patient- or payer-responsibility-transfer line and on every chargeback, contractual-adjustment, payment-application, payment-received, returned-item, unbilled-to-billed and write-off-against-allowance line, a payer class on every credit-loss, payer-takeback, refund-liability, unapplied-payment and unidentified-receipt-reclassified line whose reason category touches a receivable, tested by reason category, no payer-responsibility-transfer amount of zero or less, and the mapping version on every batch (design calls F-14, F-15 and F-17, PROPOSED);
- no explained-bank-debit line sent to a destination without its confirmed AI-20 record saying that openPractice sends them, or for a bank debit explained before that record's effective date; an unconfirmed record or change taking no effect (design call F-18, PROPOSED);
- the AI-13 events;
- reconciliation;
- the absence of PHI in keys and diagnostics;
- the AI-10 and AI-16 customer-field rules: the BAA gate, the recipient-profile version and the exclusions;
- the absence of customer records in summary-journal mode (AI-15);
- the AI-22 rules against posting to records the contract does not own and against a second collectible balance;
- the single deposit poster and `other_route_share` composition (BAB-14), and refund-disbursement matching (AI-17);
- event dates for payments, refunds and deposits (AI-11);
- recipient-profile documents validated against the AI-23 shape.

The tests SHALL run against a contract double or a disposable destination instance loaded with synthetic data only. Tests MUST NOT depend on live vendor services or real PHI.

#### Scenario: CI runs the adapter suite
- **WHEN** CI runs the ERPNext adapter's contract tests
- **THEN** they pass against a contract double or a disposable synthetic instance, reached over REST, with no access to a vendor's production service
