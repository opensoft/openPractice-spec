## ADDED Requirements

### Requirement: BAB-01 openPractice owns billing and is the receivable source of truth
openPractice SHALL own claims, patient statements, payer receivables, patient receivables, payment allocation, refunds, collections and the financial audit lineage that connects them. openPractice SHALL be the source of truth for every receivable balance and every payment allocation.

#### Scenario: The balances disagree
- **WHEN** the accounting system shows a different balance for a guarantor account than openPractice does
- **THEN** openPractice's balance is authoritative and the difference appears as a reconciliation exception

### Requirement: BAB-02 The accounting system is the books of record, and openPractice never becomes a ledger
The connected accounting system SHALL be the books of record for the general ledger, chart of accounts, bank balances, the full bank reconciliation, taxes, payroll, payables and the final financial statements. openPractice MUST NOT implement any of the following:
- a general ledger or chart-of-accounts authoring;
- accounts payable or payroll;
- tax computation;
- financial-statement production;
- full bank reconciliation.

#### Scenario: A user looks for a balance sheet
- **WHEN** a user looks for a balance sheet, vendor payables or a full bank reconciliation in openPractice
- **THEN** openPractice offers none, and directs the user to the accounting system

### Requirement: BAB-03 The accounting destination mirrors and never feeds back as truth
The destination SHALL hold a mirror of what openPractice posts. A destination-side change to an openPractice-posted document SHALL be reported as a reconciliation exception. openPractice MUST NOT import that change as receivable or allocation truth.

#### Scenario: A bookkeeper edits a mirrored invoice
- **WHEN** a bookkeeper changes the amount of an openPractice-posted invoice
- **THEN** the next reconciliation report lists the mismatch and openPractice's receivable is unchanged

### Requirement: BAB-04 Posted financial records are immutable and corrected by dated reversal
Once a financial record is posted in openPractice, it SHALL NOT be edited or deleted. A correction SHALL be a new dated reversal, with a replacement where needed, that references the record it corrects. Balances SHALL be answerable as of any past date. openPractice SHALL NOT impose a period-close lock (FD-3).

#### Scenario: A payment was applied to the wrong visit
- **WHEN** staff correct last week's payment allocation
- **THEN** a dated reversal and a new allocation are appended, both referencing the original, and an as-of query for last week still shows the original

### Requirement: BAB-05 Export batches are versioned and immutable for every destination
FD-3's export-batch freeze SHALL apply to every accounting destination. A released batch SHALL be frozen and identified by its batch sequence and content digest. Later corrections SHALL land in a subsequent batch, with back-references to the batch and posting they correct.

#### Scenario: A correction follows a released batch
- **WHEN** a reversal is posted for a posting in a released batch
- **THEN** the released batch is unchanged and the reversal appears in a later batch with a back-reference

### Requirement: BAB-06 The reason taxonomy is the join key, mapped per destination and posting mode
Every openPractice ledger entry SHALL carry exactly one reason category, from a taxonomy defined before any entry posts. Each destination SHALL map reason categories to its existing accounts through an account mapping that is versioned per destination and per posting mode. A batch SHALL be refused release if any of its lines has no mapping in the batch's destination-and-mode mapping version.

#### Scenario: An unmapped reason category
- **WHEN** a batch about to be released holds a line whose reason category has no mapping for that destination and mode
- **THEN** release is refused and the missing mapping becomes work

### Requirement: BAB-07 The integration records money and never moves it
The accounting integration SHALL record financial events and MUST NOT instruct the destination, or any bank or processor, to originate a payment, transfer or refund. Refunds SHALL be decided in openPractice and posted to the destination only as records of issued refunds.

#### Scenario: A refund is approved
- **WHEN** the owner approves a patient refund in openPractice
- **THEN** the destination receives a refund record only after the refund is recorded as issued, and no payment instruction is sent

### Requirement: BAB-08 Legacy receivables are excluded, but a mixed deposit still balances
Balances represented by old-system balance threads (FD-9) SHALL NOT be posted to a destination as customer-detail receivables. When a deposit combines legacy collections with openPractice money, the legacy share SHALL post to a mapped legacy-collections account, so that the deposit entry equals the bank line. The bookkeeper decides which account that is.

#### Scenario: A wire mixes legacy and new money
- **WHEN** a payer wire of 500.00 pays 320.00 on openPractice claims and 180.00 on legacy run-off claims
- **THEN** the deposit entry equals 500.00: 320.00 releases from clearing and 180.00 posts to the mapped legacy-collections account, and no legacy receivable is created

### Requirement: BAB-09 openPractice keeps a register of every practice bank account
openPractice SHALL keep a practice bank account register holding every account the practice uses for money that openPractice tracks: operating, deposit, lockbox, card-processor settlement and any other type. Each entry SHALL carry:
- a label, the bank, and the account type;
- the masked account number;
- the mapped bank account in the accounting system;
- a status and effective dates.

Register changes SHALL be dated, kept as history, and audited.

#### Scenario: A lockbox account is added
- **WHEN** an authorized user adds a lockbox account effective from a date
- **THEN** the register holds it with label, bank, type, masked number, mapped accounting account, status `active` and its effective date

### Requirement: BAB-10 Full bank account and routing numbers are encrypted, restricted and never exported
openPractice SHALL store full bank account and routing numbers encrypted at rest. They SHALL be visible only to the owner and billing-administrator roles, and masked everywhere else. Every view of a full number SHALL be audited. Full numbers MUST NOT appear in logs, error messages, exports, accounting postings, idempotency keys or diagnostic bundles.

#### Scenario: A front-desk user opens the register
- **WHEN** a front-desk user views the bank account register
- **THEN** only masked numbers are shown

#### Scenario: The owner reveals a full number
- **WHEN** the owner reveals a full account number
- **THEN** it is shown and the view is audited with actor and time

#### Scenario: A posting is built for a deposit
- **WHEN** a deposit posting for a register account is assembled
- **THEN** it references the register entry and the mapped accounting account, and contains no full account or routing number

### Requirement: BAB-11 Payer deposit links record how and where each payer pays
openPractice SHALL keep a payer deposit link for each payer and billing entity, where the billing entity is identified by TIN plus NPI. Each link SHALL carry:
- the payment method: `eft`, `check` or `virtual_card`;
- the linked practice bank account;
- an enrollment status: `requested`, `pending`, `active` or `rejected`;
- the payer's enrollment reference and confirmation evidence;
- an effective-dated history.

Deposit enrollment SHALL use the same enrollment-case lifecycle as provider participation enrollment. Deposit enrollment MUST NOT hold a claim; only participation enrollment gates claims. An `active` link sets the expected payment method and the expected account for that payer's money.

#### Scenario: A payer approves EFT
- **WHEN** a payer confirms EFT enrollment for a billing entity into the operating account
- **THEN** the link becomes `active`, with the enrollment reference and evidence, and that payer's wires are expected in the operating account

#### Scenario: A payer has not enrolled the practice for EFT
- **WHEN** a deposit link is `pending`
- **THEN** claims to that payer are not held on that account, and that payer's payments are expected by check

### Requirement: BAB-12 Deposits landing in an unexpected or closed account are flagged
openPractice SHALL check every revenue deposit it explains against the payer's active deposit link and the account's register status. It SHALL flag a deposit that lands in an account other than the payer's linked account, or in an account whose status is closed, as work owned by a Roster entry.

#### Scenario: A wire lands in the wrong account
- **WHEN** a payer wire arrives in the card-settlement account while the payer's link names the operating account
- **THEN** the deposit is flagged as landing in an unexpected account

#### Scenario: Money lands in a closed account
- **WHEN** a deposit arrives in an account the register marks closed
- **THEN** it is flagged as landing in a closed account

### Requirement: BAB-13 A bank change produces a tracked re-enrollment worklist
When a practice bank account is replaced or closed, openPractice SHALL create a re-enrollment worklist. The worklist SHALL hold one item for every payer deposit link that names the old account, and SHALL track each item until that payer confirms the new account. An item SHALL clear only when the payer's confirmation is recorded on the deposit link.

#### Scenario: The practice changes banks
- **WHEN** the operating account is replaced by a new account
- **THEN** every payer linked to the old account appears on the re-enrollment worklist, and each item clears only when that payer's confirmation of the new account is recorded

### Requirement: BAB-14 openPractice reconciles deposits only; the accounting system owns the bank reconciliation
openPractice SHALL reconcile only revenue-related bank lines: deposits, chargebacks, payer takebacks and processor fees. It SHALL label every other bank line "accounting system's" and SHALL NOT reconcile it. For each deposit it explains, openPractice SHALL post exactly one deposit entry to the destination, equal to the bank line. The accounting system SHALL own bank balances and the full bank reconciliation.

#### Scenario: A payer wire is explained
- **WHEN** openPractice explains a payer wire by its remittance trace number
- **THEN** it posts one deposit entry equal to the bank line, which the accounting system's reconciliation can match

#### Scenario: A rent debit appears
- **WHEN** a rent debit appears on an account openPractice reads
- **THEN** it is labelled "accounting system's", openPractice does not reconcile it, and no posting is made for it

### Requirement: BAB-15 Closed destination periods are never edited, and late charges are flagged
openPractice SHALL never post into, or edit, a period the destination reports closed, and SHALL never edit a released batch. The posting date SHALL be the date of service when that period is open. Otherwise it SHALL be the first open date, the date of service SHALL be kept on the line, and the posting SHALL be flagged as a late charge. A posting the destination refuses after release because its period closed SHALL be superseded by a new posting in a later batch, without editing the original.

#### Scenario: A charge arrives after the destination closed October
- **WHEN** a charge for an October 28 date of service is assembled after the destination closed October
- **THEN** its posting date is the first open date, its line keeps October 28 as the date of service, and it is flagged as a late charge
