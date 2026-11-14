## ADDED Requirements

### Requirement: openPractice owns billing and is the receivable source of truth
openPractice SHALL own claims, patient statements, payer receivables, patient receivables, payment allocation, refunds, collections and the financial audit lineage that connects them. openPractice SHALL be the source of truth for every receivable balance and every payment allocation.

#### Scenario: Owner asks what a patient owes
- **WHEN** the owner asks for a patient's balance while the accounting system shows a different figure
- **THEN** openPractice's balance is authoritative and the difference appears as a reconciliation exception

### Requirement: The accounting system is the books of record and openPractice never becomes a ledger
The connected accounting system SHALL be the books of record for the general ledger, chart of accounts, bank accounts, taxes, payroll, payables and the final financial statements. openPractice MUST NOT implement a general ledger, chart-of-accounts authoring, accounts payable, payroll, tax computation or financial-statement production. Its accounting role SHALL be limited to producing postings for the destination and mapping its reason taxonomy to the destination's existing accounts.

#### Scenario: A user asks openPractice for a balance sheet
- **WHEN** a user looks for a balance sheet or vendor payables in openPractice
- **THEN** openPractice offers none, and its documentation directs the user to the accounting system

### Requirement: The accounting destination mirrors and never feeds back as truth
The accounting destination SHALL hold a mirror of what openPractice posts. A change made in the destination to a document that openPractice posted SHALL be reported as a reconciliation exception, and openPractice MUST NOT import it as receivable or allocation truth.

#### Scenario: A bookkeeper edits a mirrored invoice in the accounting system
- **WHEN** a bookkeeper changes the amount of an invoice that openPractice posted
- **THEN** the next reconciliation report lists the mismatch as an exception and openPractice's receivable is unchanged

### Requirement: Posted financial records are immutable and corrected by dated reversal
Once a financial record is posted in openPractice, it SHALL NOT be edited or deleted. A correction SHALL be a new dated reversal, with a replacement where one is needed, that references the record it corrects. Balances SHALL be answerable as of any past date. openPractice SHALL NOT impose a period-close lock (FD-3).

#### Scenario: A posted payment was applied to the wrong visit
- **WHEN** staff correct a payment allocation posted last week
- **THEN** the system appends a dated reversal and a new allocation, both referencing the original, and an as-of query for last week still shows the original allocation

### Requirement: Export batches are versioned and immutable for every destination
FD-3's export-batch freeze SHALL apply to every accounting destination, not only QuickBooks. Once an export batch is released, its content SHALL be frozen and identified by its batch sequence and content digest. Later corrections SHALL land in a subsequent batch and carry back-references to the batch and entry they correct.

#### Scenario: A correction follows an exported batch
- **WHEN** a reversal is posted for an entry that was in an already released batch
- **THEN** the released batch is unchanged and the reversal appears in the next batch with a back-reference to the original batch and entry

### Requirement: The reason taxonomy is the general-ledger join key
Every openPractice financial ledger entry SHALL carry exactly one reason category from the reason taxonomy. The taxonomy SHALL be defined before any entry posts. Each accounting destination SHALL map reason categories to its own existing accounts through a versioned mapping. The system SHALL refuse to release a batch containing an entry whose reason category has no mapping for that destination.

#### Scenario: An unmapped reason category is in a batch
- **WHEN** a batch is about to be released containing an entry whose reason category has no account mapping for the destination
- **THEN** the release is refused and the missing mapping is named as work

### Requirement: The integration records money and never moves it
The accounting integration SHALL record financial events in the destination and MUST NOT instruct the destination, or any connected bank or processor, to originate a payment, transfer or refund. Refunds SHALL be decided in openPractice as governance tier-4 hard stops and posted to the destination only as records.

#### Scenario: A refund is approved
- **WHEN** the owner approves a patient refund in openPractice
- **THEN** the destination receives a refund record only after openPractice records the refund as issued, and no payment instruction is sent through the integration

### Requirement: Legacy receivables running off are excluded from patient-level postings
Balances represented by Old-System Balance threads (FD-9) SHALL NOT be posted to an accounting destination as patient-level receivables. Collections on those balances SHALL be handled under the treatment the bookkeeper chooses, which remains an open question.

#### Scenario: A legacy balance is collected
- **WHEN** a payment on a legacy run-off balance lands in the practice's bank account
- **THEN** openPractice does not post a patient-level invoice or payment for it, and the line is treated as an old-system collection

### Requirement: openPractice explains practice-revenue bank lines and the accounting system reconciles the account
openPractice SHALL explain the bank lines that carry practice revenue, as the Bank Line Inbox describes, and SHALL post a deposit to the destination only for a bank line it has explained. The accounting system SHALL own the bank account and its reconciliation. A bank line that openPractice dispositions as not its business (kind 12, "not ours to explain") SHALL NOT be exported.

#### Scenario: A payer wire is explained
- **WHEN** openPractice explains a payer wire by its remittance trace number
- **THEN** it posts one deposit to the destination equal to the bank line, which the accounting system's own bank reconciliation can match

#### Scenario: A rent payment appears on a shared account
- **WHEN** a rent debit on a shared account is dispositioned as not ours to explain
- **THEN** no posting for it reaches the accounting destination

### Requirement: Destination period locks are respected without an openPractice close
When the destination refuses a posting because its period is closed, openPractice SHALL leave the frozen entry unchanged and mark it superseded. It SHALL post the correction in the destination's next open period, in a later batch, with a back-reference to the refused entry.

#### Scenario: Late remittance after the bookkeeper's close
- **WHEN** an adjustment dated in a period the destination has closed is refused by the destination
- **THEN** openPractice posts a correcting entry dated in the destination's open period, in a later batch, referencing the refused entry
