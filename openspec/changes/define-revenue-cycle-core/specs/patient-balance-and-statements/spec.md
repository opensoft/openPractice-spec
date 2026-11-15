## ADDED Requirements

### Requirement: PS-01 A patient balance belongs to the guarantor account
What a patient owes SHALL be owed by the patient's guarantor account (PR-06, ruling 124). A guarantor account's balance due SHALL be its `patient_receivable` balance (SL-06, SL-08). Money it has paid and not yet applied, in `unapplied_credit` or `contract_liability`, SHALL be shown beside the balance as a credit and SHALL reduce the balance only when it is allocated (PY-04), never by silent netting (design call CR-34, PROPOSED).

#### Scenario: A guarantor with a credit and a balance
- **WHEN** a guarantor account owes 40.00 on one visit and holds a 25.00 unapplied credit
- **THEN** the balance due shows 40.00 with a 25.00 credit beside it, until a person or an allowed agent allocates the credit

### Requirement: PS-02 Every amount on a balance traces to a visit and a charge line
Every amount that makes up a guarantor account's balance SHALL trace to its ledger entries, its visit thread and, where it concerns one, its charge line (SL-03). A balance SHALL be answerable as of any past date (SL-05).

#### Scenario: Why do I owe this
- **WHEN** a guarantor asks why their balance is 64.00
- **THEN** staff can show each visit, charge line, payer adjustment and payment that makes up the 64.00

### Requirement: PS-03 A statement is a frozen rendering, never edited
A statement SHALL be a versioned document that renders a guarantor account's ledger as of a stated date: for each visit, the date, the patient, the administrative service names, the charges, what payers paid and adjusted, the patient's payments and credits, and the balance due, with the statement terms and due date (PS-05). It SHALL carry no diagnosis, no clinical description and no code (TH-14; design call CR-35, PROPOSED). Once issued, a statement SHALL be frozen as issued and SHALL never be edited; a correction SHALL be a new statement that replaces it, both kept.

#### Scenario: A statement had an error
- **WHEN** a statement is found to have shown a payment on the wrong visit
- **THEN** the ledger is corrected by reversal (SL-05), a new statement is issued that replaces the first, and the first is kept as issued

### Requirement: PS-04 Statements are printable in this change, and electronic delivery comes later
openPractice SHALL produce printable statements (ruling 122). A statement SHALL count as issued when a person prints and sends it and records that. Electronic delivery and the pay link SHALL come with the payment-processor feature (feature 6) and SHALL meet PS-06 and PS-10 then.

#### Scenario: Monthly statements are printed
- **WHEN** a biller prints this month's statements and records their mailing
- **THEN** each is frozen as issued with its mailing record

### Requirement: PS-05 Statement terms set the due date
Each guarantor account SHALL be under one set of statement terms, versioned and set by the practice, with a default of 30 days from issue (design call CR-36, PROPOSED). A statement's due date SHALL be its issue date plus its terms, and SHALL be the due date that `customer-detail` postings carry (AI-11). The due date SHALL be the clock on the thread while the balance is open (TH-03).

#### Scenario: Default terms
- **WHEN** a statement is issued on March 1 under the default terms
- **THEN** its due date is March 31

### Requirement: PS-06 Statements honor every communication restriction
A statement SHALL be addressed to the guarantor and SHALL honor the confidential-communication instructions of PR-05, including a restriction or alternate channel recorded through any of the person's roles (PR-15 as PR #8 adds it), the more restrictive instruction winning a conflict. Where the patient is not the guarantor, the statement SHALL show the patient's name only as needed to identify the visit, and nothing more about the patient (AI-16's rule applied to statements; design call CR-37, PROPOSED). A representative SHALL receive a statement only where their proxy policy includes billing (PG-06).

#### Scenario: A mail restriction through another role
- **WHEN** a guarantor who is also a patient has recorded `no_home_mail` with an alternate address
- **THEN** her child's statement goes to the alternate address, and nothing is mailed home

### Requirement: PS-07 Disputed money is left off statements
Money on a thread marked `disputed` SHALL be left off every statement and every collection step until the dispute resolves (TH-05): a Good Faith Estimate dispute (GF-10), or an appeal of the payer's decision on which the patient's share depends. The statement SHALL show that an amount is under review without stating it as owed.

#### Scenario: The practice appeals a denial
- **WHEN** a payer denies part of a claim and the practice appeals, so the patient's share depends on the outcome
- **THEN** the patient's statement leaves the affected amount out and says it is under review

### Requirement: PS-08 A payment hold never stops a statement
A payment hold SHALL NOT stop, delay or change any statement, and SHALL NOT stop any billing question (PH-02, PH-08).

#### Scenario: A held patient's statement
- **WHEN** a patient on a payment hold has an open balance at the statement run
- **THEN** their statement is produced as usual

### Requirement: PS-09 Balance changes are published to the hold rules and to the balance answer
Every change to a guarantor account's balance, credit or disputed amount SHALL produce an internal event naming the account and its undisputed, medical-billing balance, with no amount in any log (TH-15). The payment-hold rules SHALL use it to cancel a pending hold, re-evaluate a hold when a dispute opens, and lift a hold when its basis is paid (PH-04, PH-06, PH-07, PH-09). The read-only answer `patient_answer.balance` SHALL read the same balance (AO-11, D-15), and may be switched on once the feature that builds this requirement is ratified and built.

#### Scenario: A held balance is paid
- **WHEN** a guarantor pays the whole balance an active hold relies on
- **THEN** the event reaches the hold rules, and the hold lifts under PH-09

### Requirement: PS-10 Sending statements is a delayed, cancellable act
Sending statements from openPractice, rather than printing them, SHALL be FD-5 tier 3: a visible countdown with a cancel window before they leave (design call CR-38, PROPOSED). An agent MAY send statements at most at `act_after_delay` (ruling 132), so the cancel window always runs. No statement SHALL be sent for a balance that GF-11 says must first receive the self-pay discount.

#### Scenario: A person stops a statement run
- **WHEN** an agent schedules the monthly statements and a biller sees an error during the cancel window
- **THEN** the biller cancels the run and nothing is sent

### Requirement: PS-11 Write-offs follow the material amount the practice sets
Each practice SHALL set, at setup, the amount at which a write-off becomes material, and the practice's accountant SHALL confirm it in the policy record (RE-09, ruling 129). A write-off below that amount SHALL be FD-5 tier 2, done with a notification. A write-off at or above it SHALL be tier 4, signed by a named human only (BAB-17, ruling 33). Until the amount is set and confirmed, every write-off SHALL be tier 4 (design call CR-39, PROPOSED). Every write-off SHALL use a write-off kind (RT-02, RT-07), and an agent SHALL stay at `propose` for every write-off (ruling 132). A write-off on a balance the actor owes or guarantees SHALL follow PR-15's rule for a staff member acting on their own record (PR #8, design call F-7).

#### Scenario: A small balance below the amount
- **WHEN** the material amount is 25.00, confirmed, and a biller writes off a 3.10 balance
- **THEN** the write-off is done at once, staff are notified, and it posts as `small_balance_write_off`

#### Scenario: No amount set yet
- **WHEN** no material amount has been confirmed and a biller writes off 3.10
- **THEN** the write-off needs a named human's tier-4 sign-off
