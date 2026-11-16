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
openPractice SHALL produce printable statements (ruling 122). A statement SHALL count as issued when a person prints and sends it and records that. Printing and recording a statement as issued SHALL be done by a person at FD-5 tier 1 (design call CR-66, PROPOSED); an agent stays at `propose` for it. Electronic delivery and the pay link SHALL come with the payment-processor feature (feature 6) and SHALL meet PS-06 and PS-10 then.

#### Scenario: Monthly statements are printed
- **WHEN** a biller prints this month's statements and records their mailing
- **THEN** each is frozen as issued with its mailing record

### Requirement: PS-05 Statement terms set the due date
Each guarantor account SHALL be under one set of statement terms, versioned and set by the practice, with a default of 30 days from issue (design call CR-36, PROPOSED). Setting or changing terms SHALL be FD-5 tier 2, done with a notification, and SHALL apply only to statements issued after the change (design call CR-66, PROPOSED). A statement's due date SHALL be its issue date plus its terms, and SHALL be the due date that `customer-detail` postings carry (AI-11). The due date SHALL be the clock on the thread while the balance is open (TH-03).

#### Scenario: Default terms
- **WHEN** a statement is issued on March 1 under the default terms
- **THEN** its due date is March 31

### Requirement: PS-06 Statements honor every communication restriction
A statement SHALL be addressed to the guarantor and SHALL honor the confidential-communication instructions of PR-05, including a restriction or alternate channel recorded through any of the guarantor party's roles and every restriction PR-15 applies across its same-person group, the more restrictive instruction winning a conflict; it SHALL never use an alternate channel recorded on another party of that group (PR-15). Where a restriction that crossed a same-person link bars the channel a statement would use, and the guarantor's party has no permitted channel of its own, openPractice SHALL send nothing: it SHALL leave the statement out of every print run and every sending, SHALL NOT record it as issued (PS-04), so no due date runs from it (PS-05), and SHALL hold it and raise work, owned by a roster entry, for a named human to choose how to reach the guarantor; that person MAY use another party's alternate channel only once it is recorded on the guarantor's party as its own PR-05 instruction (PR-15). A restriction that crossed a link SHALL keep applying after the link ends, and a statement held under it SHALL stay held, until PR-15's end-of-link review decides it (design call CR-96, PROPOSED). Where the patient is not the guarantor, the statement SHALL show the patient's name only as needed to identify the visit, and nothing more about the patient (AI-16's rule applied to statements; design call CR-37, PROPOSED). A representative SHALL receive a statement only where their proxy policy includes billing (PG-06).

#### Scenario: A mail restriction through another role
- **WHEN** a guarantor who is also a patient has recorded `no_home_mail` with an alternate address
- **THEN** her child's statement goes to the alternate address, and nothing is mailed home

#### Scenario: No permitted channel is left
- **WHEN** a guarantor's own party has only a home address, and `no_home_mail` crosses to it from a linked party that has an alternate address
- **THEN** no statement is printed or sent to either address, the statement is not recorded as issued, and work is raised for a named human to choose how to reach the guarantor

### Requirement: PS-07 Disputed money, and money under same-person reconciliation, is left off statements
Money on a thread marked `disputed` SHALL be left off every statement and every collection step until the dispute resolves (TH-05): a Good Faith Estimate dispute (GF-10), or an appeal of the payer's decision on which the patient's share depends. Likewise, while same-person reconciliation work is open for a service date (CG-05), every charge for that date on each record of the group SHALL be left off every statement and every collection step until that work is closed, so that the person is never billed twice for one visit (design call CR-97, PROPOSED). The statement SHALL show that an amount is under review without stating it as owed.

#### Scenario: The practice appeals a denial
- **WHEN** a payer denies part of a claim and the practice appeals, so the patient's share depends on the outcome
- **THEN** the patient's statement leaves the affected amount out and says it is under review

#### Scenario: A duplicate record's claim was already sent
- **WHEN** a same-person link is recorded while each record holds a charge for the same service date, one of them already on a submitted claim
- **THEN** until the reconciliation work is closed, neither record's statement shows a charge for that date as owed, and each shows that an amount is under review

### Requirement: PS-08 A payment hold never stops a statement
A payment hold SHALL NOT stop, delay or change any statement, and SHALL NOT stop any billing question (PH-02, PH-08).

#### Scenario: A held patient's statement
- **WHEN** a patient on a payment hold has an open balance at the statement run
- **THEN** their statement is produced as usual

### Requirement: PS-09 Balance changes are published to the hold rules and to the balance answer
Every change to a guarantor account's balance, credit or disputed amount, and every opening or closing of same-person reconciliation for a service date on which it holds a charge, SHALL produce an internal event naming the account and its undisputed, medical-billing balance, with no amount in any log (TH-15). That balance SHALL leave out disputed money and every charge PS-07 leaves off while same-person reconciliation is open for its service date (CG-05), so that no hold and no balance answer counts a charge that may be billed twice (design call CR-97, PROPOSED). The payment-hold rules SHALL use it to cancel a pending hold, re-evaluate a hold when a dispute or a reconciliation opens, and lift a hold when its basis is paid or no longer counts (PH-04, PH-06, PH-07, PH-09). The re-evaluation when a same-person reconciliation opens on an amount a hold relies on, and the automatic lift when that leaves the policy's criteria unmet, are PH-06 and PH-09 as PR #8 amends `define-patient-gateway-and-escalation` (its design call DC-68, PROPOSED; proposed amendment PA-11 in `design.md`). The read-only answer `patient_answer.balance` SHALL read the same balance (AO-11, D-15), and may be switched on once the feature that builds this requirement is ratified and built.

#### Scenario: A held balance is paid
- **WHEN** a guarantor pays the whole balance an active hold relies on
- **THEN** the event reaches the hold rules, and the hold lifts under PH-09

#### Scenario: The only amount over the threshold is under reconciliation
- **WHEN** a guarantor account's balance meets a hold policy's criteria only because of a charge whose service date is under open same-person reconciliation
- **THEN** the published balance leaves that charge out, so no hold is placed, a pending hold is cancelled, and an active hold whose criteria are no longer met is re-evaluated at once under PH-06 and lifts under PH-09 (DC-68)

### Requirement: PS-10 Sending statements is a delayed, cancellable act
Sending statements from openPractice, rather than printing them, SHALL be FD-5 tier 3: a visible countdown with a cancel window before they leave (design call CR-38, PROPOSED). An agent MAY send statements at most at `act_after_delay` (ruling 132), so the cancel window always runs. No statement SHALL be sent for a balance that GF-11 says must first receive the self-pay discount.

#### Scenario: A person stops a statement run
- **WHEN** an agent schedules the monthly statements and a biller sees an error during the cancel window
- **THEN** the biller cancels the run and nothing is sent

### Requirement: PS-11 Write-offs follow the material amount the practice sets
Each practice SHALL set, at setup, the amount at which a write-off becomes material, and the practice's accountant SHALL confirm it in the policy record (RE-09, ruling 129). The test SHALL add the write-off's amount to every other write-off in the trailing 30 days on the same guarantor account, and separately to every other write-off on the same thread, and SHALL use the larger total, so that a balance cannot be written off in pieces, nor split across accounts, to stay below the amount (design call CR-64, PROPOSED; the accountant confirms the rule with the amount). This requirement governs every write-off kind, including a payer-side write-off against the allowance (`payer_write_off_against_allowance`), whose test uses the thread total, and a write-off of an amount still in `unbilled_receivable` because no claim for it was accepted, such as a claim held under EG-05, a rejected claim dropped under CL-09, or a claim that can no longer be sent, whose test also uses the thread total. Such an amount SHALL be written off as `unbilled_write_off_against_allowance` and SHALL NOT pass through `payer_receivable`, which only an accepted claim reaches (ruling 165, CL-12). The write-off decision first trues up the estimate under RE-05 for the part the allowance did not already hold back, and the write-off then takes the whole amount from the allowance, so that net revenue on the line ends at zero and nothing is deducted twice (design call CR-101, PROPOSED). A write-off whose tested total is below the amount SHALL be FD-5 tier 2, done with a notification. A write-off whose tested total is at or above it SHALL be tier 4, signed by a named human only (BAB-17, ruling 33). Until the amount is set and confirmed, a named human SHALL make every write-off (ruling 173), at tier 4 (design call CR-39, PROPOSED), and an agent MAY only propose one. Every write-off SHALL use a write-off kind (RT-02, RT-07), first against what the estimate held back where RT-09 applies, and an agent SHALL stay at `propose` for every write-off (ruling 132). A write-off on a balance the actor owes or guarantees SHALL follow PR-15's rule for a staff member acting on their own record (design call F-7 of `define-mvp-practice-foundation`).

#### Scenario: A held claim is written off before it is billed
- **WHEN** a 200.00 insured charge whose estimate held back 86.00 is held because the provider's enrollment was denied, and a named human writes it off
- **THEN** a 114.00 `estimate_decrease` records the decision, 200.00 posts as `unbilled_write_off_against_allowance`, `unbilled_receivable` and the allowance end at zero for the line, `payer_receivable` is never touched, and the thread can close

#### Scenario: A small balance below the amount
- **WHEN** the material amount is 25.00, confirmed, and a biller writes off a 3.10 balance
- **THEN** the write-off is done at once, staff are notified, and it posts under RT-09, as `patient_write_off_against_allowance` up to the line's remaining allowance and as `small_balance_write_off` for any excess

#### Scenario: A balance is written off in pieces
- **WHEN** the material amount is 25.00, confirmed, and a biller writes off five amounts of 20.00 on the same guarantor account within a week
- **THEN** the first is done at tier 2, and each later one, whose tested total is 40.00 or more, needs a named human's tier-4 sign-off

#### Scenario: No amount set yet
- **WHEN** no material amount has been confirmed and a biller writes off 3.10
- **THEN** the write-off needs a named human's tier-4 sign-off

### Requirement: PS-12 A balance moves between guarantor accounts only by a named human's transfer
What a guarantor account owes SHALL move to another guarantor account only as a `guarantor_transfer` entry, for example when the patient's guarantor changes or a balance is moved under PR-15's own-record rule (design call CR-57, PROPOSED). The receiving account SHALL be a guarantor of record for the patient on that visit's date of service (PR-06); a transfer to any other account SHALL be refused. The transfer SHALL name both accounts, the visit thread and charge lines it concerns, the amount and the reason. It SHALL be made by a named human only, with a notification, and an agent MAY propose it and nothing more, whatever opt-in a practice records (ruling 174; TH-13); that this is never below FD-5 tier 2 is design call CR-57, PROPOSED. A transfer from or to a balance the actor owes or guarantees SHALL follow PR-15's rule for a staff member acting on their own record (design call F-7 of `define-mvp-practice-foundation`). A statement already issued SHALL stay as issued (PS-03).

#### Scenario: A guarantor changes after the visit
- **WHEN** a child's visit balance of 80.00 sits on her father's guarantor account and the practice records her mother as guarantor for that visit
- **THEN** a named human posts an 80.00 `guarantor_transfer` from the father's account to the mother's, staff are notified, and the father's issued statement is kept unchanged

#### Scenario: An agent proposes a transfer
- **WHEN** an agent finds a balance on the wrong guarantor account and the practice has opted the agent up for other actions
- **THEN** the agent can only propose the transfer, and a named human decides it
