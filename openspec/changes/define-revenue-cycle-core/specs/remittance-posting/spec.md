## ADDED Requirements

### Requirement: RM-01 A remittance is the payer's answer, and it is not the money
openPractice SHALL record each payer remittance as the payer's answer: its claim payments, each claim payment's service-line decisions (RM-06), and any payer-level adjustments (RM-08). The payment itself SHALL be a separate record carrying the payer's trace or check reference, never a bank account or routing number (BAB-10), so that deposit explanation can match it to a bank line (BAB-14). A remittance may arrive before or after its money, and one deposit may cover several remittances (framework §4.3). The remittance SHALL be frozen as received (TH-14).

#### Scenario: The answer arrives before the money
- **WHEN** a remittance arrives on Monday and its EFT reaches the bank on Wednesday
- **THEN** the answer posts on Monday, the payment waits in `cash_clearing`, and the deposit is explained against Wednesday's bank line

### Requirement: RM-02 Paper and electronic remittances produce the same records
A remittance received electronically through an adapter (CP-01) and a paper remittance that staff enter by hand SHALL produce the same records and the same postings. A hand-entered remittance SHALL record who entered it and a reference to the scanned paper.

#### Scenario: A paper explanation of benefits
- **WHEN** a biller enters a paper remittance from a payer that sends no electronic remittances
- **THEN** it creates the same claim payments, line decisions and postings an electronic one would, marked as entered by hand

### Requirement: RM-03 Each claim payment attaches to a claim only on an exact key match
Each claim payment SHALL attach to a submitted claim version only when it matches exactly one, by the payer's claim control number or the thread's key set (TH-10). A claim payment that matches no claim SHALL become a work item; where it matches an `old_system_balance` thread, its money SHALL post as `legacy_collection` (SL-11). A claim payment that matches more than one claim SHALL become a work item naming every candidate.

#### Scenario: A payment for a claim from the old system
- **WHEN** a remittance pays a claim that the practice's previous system sent and an old-system balance thread holds
- **THEN** the money posts as `legacy_collection`, and no openPractice receivable is created

### Requirement: RM-04 Exactly matched lines post by themselves, with notice and undo
A claim payment whose every part matches exactly SHALL post without a person, at FD-5 tier 2, done with a notification and undoable (ruling 120). Exactly matched SHALL mean (design call CR-52, PROPOSED):
- it attached to exactly one claim version under RM-03;
- every service line matches exactly one claim line by its line identity, procedure code, units and billed amount;
- every adjustment code is in the normalization table (RT-05);
- on every line, the paid amount, the adjustments and the patient responsibility add up to the billed amount;
- no line reverses or takes back an earlier posting.

Undo SHALL be a reversal of everything the posting made (SL-05), available until the payment has been explained in a deposit; after that, a correction SHALL need a person's reason (design call CR-53, PROPOSED). This automatic posting is a shipped rule, not an agent action.

#### Scenario: A clean remittance
- **WHEN** a remittance pays a two-line claim with standard contractual adjustments whose codes are all in the table and whose amounts add up
- **THEN** it posts without a person, the biller is notified, and the posting can be undone from the record

### Requirement: RM-05 Anything not exactly matched is a proposal for a human
A claim payment that does not match exactly SHALL NOT post. openPractice SHALL prepare the posting it would make and present it as a proposal in the work inbox, where a person approves, changes or rejects it (WI-03; ruling 120). An agent MAY prepare such a proposal and SHALL stay at `propose` (TH-13).

#### Scenario: An unknown adjustment code
- **WHEN** a claim payment's line carries an adjustment code the table does not cover
- **THEN** nothing posts for that claim payment, and a proposed posting waits for a biller

### Requirement: RM-06 Each service line keeps the payer's decision as received
For each service line openPractice SHALL keep, as received: the allowed amount, the paid amount, each adjustment with its group code, reason code and remark codes, both raw and normalized (RT-05), the patient responsibility as the payer split it (deductible, coinsurance, copay or not covered), and the payer's claim control number. These SHALL be kept so that a later payer's claim can carry what this payer decided (CL-02) and so that the appeal evidence is complete.

#### Scenario: A line with a deductible
- **WHEN** a line is allowed 100.00, paid 0.00 and assigned 100.00 to the deductible
- **THEN** the line keeps all three amounts and the deductible split as the payer reported them

### Requirement: RM-07 Posting an answer realizes the adjustment, trues up the estimate and moves the patient's share
Posting a claim payment SHALL make these entries (AI-12, AI-15):
- `contractual_adjustment` for the billed amount less the allowed amount;
- a true-up for any difference between the estimate and what the answer shows (RE-05);
- `payer_payment` for what the payer paid;
- `patient_share_transfer` for the patient's responsibility, unless the patient has a later coverage position for that date, in which case the share SHALL wait as work for a person to decide whether a later payer is billed first (design call CR-54, PROPOSED).

A denied amount SHALL never be written off automatically: it SHALL become work for a person to appeal, which marks it disputed (SL-10), or to write off under PS-11 (design call CR-55, PROPOSED). The thread SHALL move from `claimed` to `remitted` (TH-04).

#### Scenario: Paid with a patient share
- **WHEN** a 200.00 claim is allowed 120.00, paid 96.00, with 24.00 coinsurance, and the estimate expected 114.00
- **THEN** 80.00 posts as `contractual_adjustment`, 96.00 as `payer_payment`, 24.00 as `patient_share_transfer`, and a 6.00 `estimate_increase` true-up records that the estimate was low

#### Scenario: A denied line
- **WHEN** a payer denies one line of a claim
- **THEN** nothing is written off, and a work item asks the biller to appeal or to write it off

### Requirement: RM-08 Payer-level adjustments are resolved, and a remittance that does not add up does not post
A remittance's payer-level adjustments SHALL each resolve to a claim or to a named non-patient line: a takeback SHALL post as `payer_takeback` and link to what it recovers (RM-09), and payer interest SHALL post as `payer_interest` (AI-13). Any other payer-level adjustment SHALL become work until a person classifies it (RT-02). A remittance whose payment total does not equal its claim payments less its payer-level adjustments SHALL NOT post, and SHALL become work.

#### Scenario: A payment nets a takeback
- **WHEN** a remittance pays 450.00 on new claims and takes back 50.00 from an earlier claim, for a payment of 400.00
- **THEN** 450.00 posts against the new claims, 50.00 posts as `payer_takeback` linked to the earlier claim, and the 400.00 payment matches its bank line

### Requirement: RM-09 Recoupments and overpayments have a case and a clock
A payer's demand for money back, or a takeback, SHALL mint a `recoupment` case thread linked `recoups` to the visit it concerns (TH-08, TH-09), with the payer's decide-by date as its clock. A person SHALL decide whether to appeal or agree, and the offset SHALL link `offsets` to the later remittance line where it lands. An overpayment the practice identifies itself SHALL post as `payer_overpayment` and mint a `credit_balance` case (PY-06), whose clock comes from a counsel-verified parameter (framework §5A.2.7).

#### Scenario: A demand letter arrives
- **WHEN** a payer's letter demands 75.00 back on a paid visit by the 24th
- **THEN** a recoupment case is minted with the 24th as its clock, linked to the visit, and a person decides whether to appeal

### Requirement: RM-10 A payer's reversal is a new posting, never an edit
A payer's reversal or corrected remittance SHALL post reversing entries that reference the entries they undo, followed by the corrected entries (SL-05). No earlier posting SHALL be edited, and a thread the earlier posting had closed SHALL move forward again (TH-07).

#### Scenario: A corrected remittance
- **WHEN** a payer reverses an earlier payment of 96.00 and repays 90.00
- **THEN** the 96.00 posting is reversed with a reference, 90.00 posts, and the thread reopens if it had closed

### Requirement: RM-11 Receiving and reading remittances are protected operations
Receiving a remittance from an adapter, and reading remittance detail, SHALL be protected reimbursement operations under `add-reimbursement-retention-control` ("Protected reimbursement operations are authorized before execution"), each producing an owner-local use event. Remittances as received SHALL be retained under that change's obligations, and access to their detail SHALL be limited to roles that need it.

#### Scenario: A front-desk user opens remittance detail
- **WHEN** a user whose role does not include remittance access tries to read a remittance's line detail
- **THEN** the read is refused and the refusal is recorded as a use event
