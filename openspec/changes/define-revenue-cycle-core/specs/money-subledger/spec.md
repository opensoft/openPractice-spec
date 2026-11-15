## ADDED Requirements

### Requirement: SL-01 Every movement of money is one entry between two fixed buckets
openPractice SHALL record every movement of money it owns as one ledger entry that moves a positive amount from one balance bucket to another, both from the fixed list of SL-02 (ruling 124). Each entry SHALL debit one bucket and credit the other by the same amount, so the ledger balances by construction and no unbalanced entry can exist. There are two exceptions to two different buckets: `guarantor_transfer`, which debits one guarantor account's `patient_receivable` and credits another's by the same amount (PS-12), and `payer_share_transfer`, which debits a later coverage's `payer_receivable` and credits the earlier coverage's (RM-07) (RT-02). The pair of buckets SHALL be the one the entry's reason code fixes (RT-02); an entry SHALL NOT choose its own buckets. openPractice SHALL NOT author, add or rename accounts: the buckets are openPractice's own balances, and each accounting destination maps them to its own accounts through the reason codes (BAB-02, BAB-06).

#### Scenario: A self-pay charge is finalized
- **WHEN** a 150.00 self-pay office visit is finalized for a guarantor account
- **THEN** one entry moves 150.00 from `gross_service_revenue` to `patient_receivable`, recorded as a debit to `patient_receivable` and a credit to `gross_service_revenue`

#### Scenario: Someone tries an unbalanced entry
- **WHEN** a process tries to record a debit with no matching credit
- **THEN** it is refused, because an entry always names two buckets and one amount

### Requirement: SL-02 The bucket list is fixed and shipped
The buckets SHALL be exactly these, shipped by the product and not changeable by a practice (design call CR-9, PROPOSED):

| Bucket | What it holds |
|---|---|
| `unbilled_receivable` | Insured services given and not yet claimed, at the gross charge |
| `payer_receivable` | Amounts claimed from a payer, at the gross charge, per payer and coverage version |
| `patient_receivable` | Amounts a guarantor account owes, at the gross charge less any discount granted before the obligation |
| `gross_to_net_allowance` | The part of gross receivables the practice does not expect to collect for price reasons: contractual adjustments, price concessions and amounts held out under the constraint |
| `credit_loss_allowance` | Expected credit losses on amounts the practice did expect to collect, used only under the allowance method (RT-10) |
| `cash_clearing` | Money received and not yet explained in a bank deposit |
| `deposited` | Money explained against bank lines: deposits in, less bank debits matched to money paid out (BAB-14, SL-13). It is a record of what reached or left the bank; the bank balance stays the accounting system's (BAB-02) |
| `contract_liability` | Money received before the service it pays for was given |
| `unapplied_credit` | A guarantor account's money received and not yet allocated, or received beyond what it owes |
| `refund_liability` | Money the practice has identified as owed back, including a forwarding balance a payer will recover by offset (RM-08) |
| `unidentified_receipts` | Money received whose owner is not yet known |
| `other_route_share` | The part of an explained deposit that funds another route's balances (BAB-14) |
| `legacy_collections` | Money collected on balances from the practice's previous system (BAB-08) |
| `gross_service_revenue` | Services given, at the gross charge |
| `revenue_deduction` | Reductions from gross revenue: discounts, contractual adjustments, price concessions and estimate changes, and write-offs beyond what the allowance held (RT-09) |
| `credit_loss_expense` | Credit losses, kept apart from revenue (AI-12) |
| `fee_expense` | Merchant, card, chargeback and bank fees |
| `non_patient_income` | Money not tied to a patient service, such as payer interest |

A new bucket SHALL need a governed change.

#### Scenario: A practice tries to add a bucket
- **WHEN** an administrator tries to add a bucket named "marketing revenue"
- **THEN** the change is refused, and the administrator is told that buckets are fixed

### Requirement: SL-03 Each entry carries what traces it to its source
Every ledger entry SHALL carry (design call CR-10, PROPOSED):
- its reason code (RT-01), and, on a revenue or deduction entry, its service category;
- its amount and currency (SL-04);
- its thread (TH-01) where either of its buckets is a receivable, `gross_to_net_allowance`, `contract_liability`, `unapplied_credit`, `refund_liability`, `unidentified_receipts`, `gross_service_revenue` or `revenue_deduction`, and its charge line where it concerns one; where its two sides belong to different threads, such as a credit held for one visit applied to another visit's charge line, the thread of each side. An entry between other buckets only, such as a deposit or bank debit explained against clearing, a fee, payer interest, another route's share, a legacy collection or a close-level credit-loss provision, names a thread only where its source record belongs to one;
- its counterparty account (SL-06) where it touches a receivable, a credit or a liability;
- the payer class where it touches a receivable or revenue (ruling 28);
- four dates: the date of service, the effective date of the information it records, the accounting date in the open period it belongs to, and the time openPractice recorded it (AI-11);
- a reference to its source record, such as a final charge line, a claim version, a remittance line, a payment or a person's decision, and the estimate version where it records revenue or a deduction (RE-01);
- the actor, actor type and principal (AM-03);
- where it reverses another entry, a reference to that entry.

An entry missing any of these SHALL be refused.

#### Scenario: A payment without a source
- **WHEN** a process tries to record a patient payment entry with no payment record behind it
- **THEN** the entry is refused and the missing source is named

### Requirement: SL-04 Amounts are whole cents under the foundation's money rule
Every amount in the ledger SHALL be stored as a whole number of cents with its ISO 4217 currency code, and SHALL follow BAB-18 (ruling 103): decimal text with a currency code wherever an amount leaves openPractice, USD only in version 1, one rounding rule applied once when openPractice computes an amount, and cumulative rounding when a fixed amount is split, so the parts always equal the whole. A total SHALL be the sum of amounts already rounded, and a fixed amount, such as a payment, SHALL never be recomputed from its parts.

#### Scenario: A payment is split across three charge lines
- **WHEN** a 100.00 payment is allocated equally across three charge lines
- **THEN** the allocations are 33.33, 33.34 and 33.33, they add to 100.00, and the payment is still 100.00

### Requirement: SL-05 Entries are never edited, and balances are answerable as of any date
A ledger entry SHALL NOT be edited or deleted once recorded (BAB-04, FD-3). A correction SHALL be a reversing entry of the same amount between the same buckets in the opposite direction, referencing the entry it reverses, followed by a replacement entry where one is needed. Moving a payment that was recorded against the wrong guarantor account SHALL be such a correction, made by a person at FD-5 tier 2 with a notification, with an agent at `propose` and PR-15's rule for a staff member's own record applying (design call CR-99, PROPOSED). Every balance SHALL be answerable as of any past date and time, by both accounting date and recorded time. openPractice SHALL NOT lock a period (BAB-04).

#### Scenario: A payment was put on the wrong guarantor account
- **WHEN** staff correct a payment recorded last week against the wrong guarantor account
- **THEN** a reversing entry and a new entry are appended, both referencing the original, and an as-of query for last week still shows the original

### Requirement: SL-06 Receivables belong to counterparty accounts
Every receivable, credit and liability entry SHALL name one counterparty account:
- a **guarantor account** (PR-06), for what a guarantor owes or is owed. Patient balances SHALL belong to the guarantor account, not to the patient (ruling 124), and each entry SHALL still name the patient's thread;
- a **payer coverage account**, the payer together with the coverage version the claim used (PR-07), for what a payer owes or is owed;
- an **unidentified** account, for money whose owner is not yet known.

A counterparty account SHALL reference parties only through their roles (PR-15), and SHALL NOT copy a party's identity facts.

#### Scenario: A parent guarantees two children
- **WHEN** a parent is the guarantor for two children who each had a visit
- **THEN** both visits' patient balances belong to the parent's guarantor account, and each entry names its own child's thread

### Requirement: SL-07 An allocation is its own record
Applying a payment, credit or adjustment to charge lines SHALL be an allocation record that names the money, each charge line and the amount applied to it, and that posts the entries that move the money (SL-01). Every entry an allocation posts SHALL name the same counterparty account on each side that carries one, so that one guarantor account's money is never applied to another's charges. What a guarantor account owes SHALL move to another account only by `guarantor_transfer` (PS-12), and money it paid SHALL move only by correcting a payment recorded against the wrong account (SL-05) (design call CR-65, PROPOSED). No allocation SHALL take a charge line's `patient_receivable` below zero; the rest of the money SHALL stay in, or move to, `unapplied_credit` (design call CR-92, PROPOSED). Reallocating SHALL reverse the allocation and record a new one; it SHALL NOT edit the first (BAB-04). Reallocating SHALL be FD-5 tier 2, done with a notification (design call CR-66, PROPOSED). A split SHALL use BAB-18's cumulative rounding in the order the allocation states.

#### Scenario: A misapplied payment is reallocated
- **WHEN** a payment applied to visit A should have gone to visit B
- **THEN** the allocation to A is reversed, a new allocation to B is recorded, and both refer to the payment

#### Scenario: A credit is applied to another guarantor's charge
- **WHEN** an agent at `act_with_notice` tries to apply guarantor A's 30.00 credit to a charge owed by guarantor B
- **THEN** the allocation is refused, because both sides must name the same guarantor account

### Requirement: SL-08 Balances come only from entries
Every balance SHALL be computed from ledger entries and from nothing else: by counterparty account, by thread, by charge line, by bucket and by payer class. No record SHALL store a balance that is not the sum of its entries. A thread's amount (TH-03), a statement's total (PS-03), a hold's basis (PH-04, PH-07) and a reconciliation total (SL-09) SHALL each be read from entries.

#### Scenario: A balance and its entries disagree
- **WHEN** a stored total on any record differs from the sum of the entries behind it
- **THEN** the stored total is wrong by definition, and an integrity work item is raised

### Requirement: SL-09 The ledger supplies the detail manifest and the reconciliation totals
The ledger SHALL supply the detail from which every summary-journal line can be traced (AI-15), and the totals that AI-08's reconciliation report compares: each payer-class receivable bucket against the destination's control account at the same cut-off, and the gross-to-net allowance against the sum of current estimates (RE-01). A difference SHALL become a work item and SHALL never be corrected automatically.

#### Scenario: The Medicare-class receivable drifts
- **WHEN** openPractice's Medicare-class payer receivable differs from the destination's control account at the cut-off
- **THEN** the report shows the difference, a work item is raised, and nothing is corrected automatically

### Requirement: SL-10 A dispute is a status, not a balance
A dispute on money, such as a denied amount under appeal or a Good Faith Estimate dispute, SHALL be recorded as a status on the thread (TH-05) and on the affected charge lines, naming the amount contested. It SHALL NOT be recorded as an asset or a balance of its own. The recognized amount SHALL stay the constrained estimate (RE-07), and the contested amount above it SHALL sit within the gross-to-net allowance (framework §5A.2.6). A disputed amount SHALL be identifiable, so that a payment hold never counts it (PH-06).

#### Scenario: A denial is appealed
- **WHEN** a payer denies 12.00 and the practice appeals
- **THEN** the thread is marked disputed for 12.00, no balance is created for the dispute, and the 12.00 is held out of the recognized amount

### Requirement: SL-11 Balances from an old system stay outside the medical receivables
A balance brought over from the practice's previous system SHALL be recorded only as an `old_system_balance` case thread (TH-08, ruling 131), carrying its amount as a memo with a one-line provenance boundary. It SHALL NOT enter any receivable bucket, SHALL NOT be posted to a destination as a receivable (BAB-08), and SHALL NOT be claimed, rebilled or appealed from openPractice. Money collected on it SHALL post to `legacy_collections`. The import that creates these threads SHALL be built before the first practice that needs it goes live (ruling 131). Each import batch SHALL take effect only on a named human's sign-off (BAB-17) after reviewing its count and memo total, and an agent stays at `propose` for it (design call CR-66, PROPOSED).

#### Scenario: A legacy payment arrives in a mixed deposit
- **WHEN** a payer deposit pays 320.00 on openPractice claims and 180.00 on old-system balances
- **THEN** 320.00 settles payer receivables, 180.00 posts to `legacy_collections`, and no old-system receivable is created

### Requirement: SL-12 The ledger is audited and tamper-evident
Every ledger entry, allocation and reversal SHALL be audited with its actor, time and source in a tamper-evident trail that no one can edit or delete, on the pattern of PR-10. Every read of a ledger record tied to a patient SHALL be audited as a read of PHI. Verifying the trail SHALL flag any entry that was altered, and any entry whose reversal reference points to nothing.

#### Scenario: Someone alters an entry outside the application
- **WHEN** an entry's amount is changed directly in the database
- **THEN** verification of the trail flags the alteration

### Requirement: SL-13 Money paid out of the bank clears when its bank line is matched
When deposit explanation matches a bank debit line to money openPractice recorded as paid out and not netted inside a deposit (BAB-14), namely an issued refund, a chargeback, a returned item, a payer takeback or a fee, openPractice SHALL post `bank_debit_explained` for the matched amount, referencing the bank line and the entries it clears (design call CR-60, PROPOSED). `cash_clearing` SHALL then return to zero for that money. A bank debit line that matches nothing openPractice recorded SHALL stay the accounting system's (BAB-14) and post nothing here.

#### Scenario: A refund check clears the bank
- **WHEN** a 15.00 refund check recorded as `refund_issued` is paid by the bank, and deposit explanation matches the 15.00 debit line to it
- **THEN** `bank_debit_explained` posts 15.00, and `cash_clearing` holds nothing for that refund

#### Scenario: A takeback inside a remittance
- **WHEN** a payer takes back 50.00 by netting it inside a 400.00 deposit
- **THEN** no `bank_debit_explained` posts for it, because `deposit_explained` for the 400.00 net deposit already clears it

### Requirement: SL-14 A fee posts only from a matched record, and a deposit shortfall stays owned work
A fee, whether `merchant_fee`, `virtual_card_fee`, `chargeback_fee` or `bank_fee`, SHALL post by itself only from a processor or bank record that deposit explanation has matched (BAB-14). Any other fee SHALL be recorded by a person at FD-5 tier 2, with a notification, citing its evidence, and an agent SHALL stay at `propose` for it (design call CR-94, PROPOSED). A difference between the money receipted for a deposit and the bank line that no matched record explains, including a bank's correction of a deposit, SHALL stay in `cash_clearing` as owned work (TH-12), and SHALL NOT be posted as a fee. The list has no kind for cash over or short, because ruling 130 leaves the drawer count and close sheet to a later change.

#### Scenario: A deposit is short with no bank record
- **WHEN** 100.00 in cash is receipted, the bank credits 90.00 for that deposit, and no bank record explains the 10.00
- **THEN** no fee posts, the 10.00 stays in `cash_clearing` as owned work, and staff are told

#### Scenario: A processor fee on a settlement
- **WHEN** a card settlement's processor record shows a 2.40 fee netted from the settlement
- **THEN** the fee posts as `merchant_fee` from the matched record, and the net settlement matches its bank line
