## ADDED Requirements

### Requirement: RT-01 Reason codes are a fixed list the product ships, on two axes
Every ledger entry SHALL carry exactly one reason code from a fixed list that the product ships (ruling 125, BAB-06). The list SHALL have two axes:
- the **kind** of money movement, such as a self-pay service, a contractual adjustment or a patient payment (RT-02);
- the **service category**, such as an office visit or a lab test, carried only by entries that record revenue or a deduction from it (RT-03).

A practice SHALL NOT author, rename or remove a kind or a service category. The list SHALL be defined, and versioned, before any entry posts (BAB-06).

#### Scenario: A practice wants its own adjustment code
- **WHEN** a biller tries to create a reason code called "misc adjustment"
- **THEN** it is refused, and the biller is offered the shipped kinds that fit

### Requirement: RT-02 Each kind fixes one bucket pair and one neutral label
Each kind SHALL fix the bucket it debits, the bucket it credits (SL-01) and the neutral label of `openpractice-accounting-integration/1.0` that its lines carry (AI-16). The initial list SHALL be the following, which ruling 163 accepts as the starting list, with the practice's accountant confirming how each kind maps to the books (ruling 163; under D8):

| Kind | Debit | Credit | Neutral label |
|---|---|---|---|
| `service_insured` | `unbilled_receivable` | `gross_service_revenue` | `patient_services_revenue` |
| `service_self_pay` | `patient_receivable` | `gross_service_revenue` | `patient_services_revenue` |
| `missed_appointment_fee` | `patient_receivable` | `gross_service_revenue` | `patient_services_revenue` |
| `self_pay_discount` | `revenue_deduction` | `patient_receivable` | `gross_to_net_deduction` |
| `gross_to_net_estimate` | `revenue_deduction` | `gross_to_net_allowance` | `gross_to_net_deduction` |
| `estimate_decrease` | `revenue_deduction` | `gross_to_net_allowance` | `gross_to_net_true_up` |
| `estimate_increase` | `gross_to_net_allowance` | `revenue_deduction` | `gross_to_net_true_up` |
| `claim_accepted` | `payer_receivable` | `unbilled_receivable` | `unbilled_to_billed` |
| `contractual_adjustment` | `gross_to_net_allowance` | `payer_receivable` | `contractual_adjustment` |
| `patient_share_transfer` | `patient_receivable` | `payer_receivable` | `patient_responsibility_transfer` |
| `payer_payment` | `cash_clearing` | `payer_receivable` | `payment_received` |
| `patient_payment` | `cash_clearing` | `patient_receivable` | `payment_received` |
| `patient_payment_unapplied` | `cash_clearing` | `unapplied_credit` | `unapplied_payment` |
| `prepayment` | `cash_clearing` | `contract_liability` | `contract_liability` |
| `credit_applied` | `unapplied_credit` | `patient_receivable` | `payment_application` |
| `prepayment_applied` | `contract_liability` | `patient_receivable` | `payment_application` |
| `prepayment_to_credit` | `contract_liability` | `unapplied_credit` | `unapplied_payment` |
| `patient_overpayment_to_credit` | `patient_receivable` | `unapplied_credit` | `unapplied_payment` |
| `guarantor_transfer` | `patient_receivable` | `patient_receivable` | `patient_responsibility_transfer` |
| `payer_share_transfer` | `payer_receivable` | `payer_receivable` | `payer_responsibility_transfer` |
| `payer_write_off_against_allowance` | `gross_to_net_allowance` | `payer_receivable` | `write_off_against_allowance` |
| `patient_write_off_against_allowance` | `gross_to_net_allowance` | `patient_receivable` | `write_off_against_allowance` |
| `small_balance_write_off` | `revenue_deduction` | `patient_receivable` | `write_off` |
| `price_concession_write_off` | `revenue_deduction` | `patient_receivable` | `write_off` |
| `charity_care` | `revenue_deduction` | `patient_receivable` | `write_off` |
| `credit_loss_provision` | `credit_loss_expense` | `credit_loss_allowance` | `credit_loss` |
| `credit_loss_write_off` | `credit_loss_allowance` | `patient_receivable` | `credit_loss` |
| `credit_loss_direct_write_off` | `credit_loss_expense` | `patient_receivable` | `credit_loss` |
| `payer_overpayment` | `payer_receivable` | `refund_liability` | `refund_liability` |
| `patient_credit_to_refund` | `unapplied_credit` | `refund_liability` | `refund_liability` |
| `refund_issued` | `refund_liability` | `cash_clearing` | `refund` |
| `chargeback` | `patient_receivable` | `cash_clearing` | `chargeback` |
| `returned_item` | `patient_receivable` | `cash_clearing` | `returned_item` |
| `chargeback_fee` | `fee_expense` | `cash_clearing` | `chargeback_fee` |
| `bank_fee` | `fee_expense` | `cash_clearing` | `bank_fee` |
| `merchant_fee` | `fee_expense` | `cash_clearing` | `merchant_fee` |
| `virtual_card_fee` | `fee_expense` | `cash_clearing` | `virtual_card_fee` |
| `payer_takeback` | `payer_receivable` | `cash_clearing` | `payer_takeback` |
| `overpayment_recovered_by_takeback` | `refund_liability` | `cash_clearing` | `payer_takeback` |
| `payer_forwarding_balance_carried` | `cash_clearing` | `refund_liability` | `payer_forwarding_balance` |
| `payer_forwarding_balance_applied` | `refund_liability` | `cash_clearing` | `payer_forwarding_balance` |
| `payer_interest` | `cash_clearing` | `non_patient_income` | `payer_interest` |
| `unidentified_receipt` | `cash_clearing` | `unidentified_receipts` | `unidentified_receipt` |
| `unidentified_receipt_identified` | `unidentified_receipts` | `unapplied_credit` | `unidentified_receipt_reclassified` |
| `unidentified_receipt_to_payer` | `unidentified_receipts` | `cash_clearing` | `unidentified_receipt_reclassified` |
| `legacy_collection` | `cash_clearing` | `legacy_collections` | `legacy_collections` |
| `other_route_share` | `cash_clearing` | `other_route_share` | `other_route_share` |
| `deposit_explained` | `deposited` | `cash_clearing` | `deposit_clearing_release` |
| `bank_debit_explained` | `cash_clearing` | `deposited` | `bank_debit_clearing_release` |

An entry SHALL use the pair and label its kind fixes. A money event that no kind fits SHALL become a work item and SHALL NOT post. Two kinds have one bucket on both sides: `guarantor_transfer` moves an amount from one guarantor account's `patient_receivable` to another's (PS-12), and `payer_share_transfer` moves it from one coverage's `payer_receivable` to the next coverage position's (RM-07) (SL-01). Three labels, `write_off_against_allowance`, `bank_debit_clearing_release` and `payer_responsibility_transfer`, are not yet in the accounting contract; proposed amendment PA-1 in `design.md` adds them, with the counter payer class a summary-journal `payer_share_transfer` line needs to name both payer classes, and RT-04 holds back what needs them until it is adopted.

The kinds added for money that would otherwise have nowhere to go are design calls (PROPOSED): a prepayment that will not be applied, and a charge reduced below what was paid on it (CR-56, PY-03); a transfer between guarantor accounts (CR-57, PS-12); a balance passed from one payer to the next (CR-91, RM-07); money that arrived before its remittance (CR-58, RM-01); a payer's forwarding balance (CR-59, RM-08); a bank debit matched to money paid out (CR-60, SL-13); a patient write-off against the allowance (CR-61, RT-09); a direct credit-loss write-off (CR-62, RT-10); and a takeback that recovers an overpayment the practice already identified (CR-63, RM-09).

#### Scenario: A contractual adjustment is posted
- **WHEN** a payer allows 120.00 on a 200.00 charge and reports the 80.00 reduction with a code the table classifies as contractual
- **THEN** an entry of kind `contractual_adjustment` moves 80.00, debiting `gross_to_net_allowance` and crediting `payer_receivable`, with the label `contractual_adjustment`

#### Scenario: A cancelled procedure was prepaid
- **WHEN** a patient's 100.00 prepayment for a procedure stays in `contract_liability` and the procedure is cancelled
- **THEN** it moves to the guarantor account's `unapplied_credit` as `prepayment_to_credit` with the cancellation (AP-04), without reversing or re-recording the payment

#### Scenario: A payer-level adjustment no kind fits
- **WHEN** a remittance carries a payer-level adjustment that no kind in the list fits
- **THEN** nothing posts for it, and a work item is raised for the biller

### Requirement: RT-03 Service categories are shipped and benign, and each catalog item maps to one
The service categories SHALL be a fixed list the product ships (ruling 125). The initial list SHALL be `office_visit`, `procedure`, `lab_work`, `lab_test`, `imaging`, `injection_and_supply`, `missed_appointment` and `other_patient_service` (design call CR-13, PROPOSED). No category SHALL name a condition, a diagnosis or a sensitive kind of care. Each catalog item SHALL map to exactly one category (SC-05). The mapping SHALL be versioned and effective-dated; a change SHALL apply from its effective date onward, and an amount already posted under the old category SHALL move only by a posted reclassification (framework §5A.2.9).

#### Scenario: An item is remapped
- **WHEN** an administrator remaps a catalog item from `procedure` to `office_visit` from the first of next month
- **THEN** charges dated before that date keep `procedure`, and charges from that date carry `office_visit`

### Requirement: RT-04 The service category reaches accounting only in summary-journal totals
An entry's service category SHALL reach an accounting destination only on `summary-journal` lines, where AI-15 already summarizes by benign service category and no line identifies a person. It SHALL NOT appear on any `customer-detail` posting, whose lines carry only neutral labels that never reveal the type of service (AI-16). The accounting contract on `main` does not carry a service-category field. Proposed amendment PA-1 in `design.md` adds it (ruling 125), and PR #8 applies it at `664fee8`, an open draft in which the field rests on ruling 125 and the rest of the amendment is PROPOSED; nothing there is ratified. Until the foundation change carrying the amendment merges, no summary-journal batch holding revenue or deduction lines from this change SHALL be released, and no category SHALL be folded into `reason_category` in its place: ruling 164 rules the refusal, which blocks accounting go-live until the contract is amended, and that the contract counts as amended once the foundation change carrying PA-1 merges is design call CR-14, PROPOSED. Likewise, no batch SHALL carry a line whose label the contract does not yet list (RT-02); those entries stay in openPractice's ledger and wait for the amendment. In `customer-detail` mode, a line of a kind for which the accounting contract defines no customer-detail posting yet, in AI-13 or the posting map of `define-mvp-practice-foundation`, SHALL be held in openPractice and not sent until the contract maps it; PA-1 proposes those postings, including the kinds that post nothing there (design call CR-98, PROPOSED).

#### Scenario: A customer-detail invoice line
- **WHEN** a self-pay visit is posted to a destination in `customer-detail` mode
- **THEN** the line carries the neutral label `patient_services_revenue` and no service category

#### Scenario: The amendment has not landed
- **WHEN** a summary-journal batch with revenue lines is ready, and the foundation change carrying the service-category field has not merged
- **THEN** release is refused and the reason names the missing field

### Requirement: RT-05 Payer adjustment codes are kept as received and normalized by a versioned table
Every adjustment a payer reports SHALL keep its codes exactly as received: the group code, the claim adjustment reason code and any remark codes. Each SHALL also be normalized to a kind (RT-02), or to a denial, which posts no entry (RM-07), and to a plain-language label, by a versioned normalization table that the product ships from license-free code sets (FD-11). The table SHALL classify an adjustment as `contractual_adjustment` only where it reports a reduction the payer's contract or fee schedule makes to the amount billed, and as a denial where the payer refuses to pay for the service or a part of it; an adjustment it cannot place with certainty SHALL be classified as a denial, so that a person decides it (design call CR-55, PROPOSED). A code the table does not cover SHALL become a work item, and that adjustment SHALL NOT post until a person classifies it. Classifying it SHALL be done by a person at FD-5 tier 2, with a notification, and SHALL apply only to that adjustment; covering the code for later adjustments SHALL be a new version of the table (design call CR-66, PROPOSED). A change to the table SHALL apply only to adjustments received after its effective date.

#### Scenario: An unknown adjustment code
- **WHEN** a remittance line carries an adjustment reason code the table does not cover
- **THEN** the line's other amounts follow RM-04 and RM-05, the unknown adjustment does not post, and a work item names it

### Requirement: RT-06 A discount and a write-off are different codes, and the moment decides which
A reduction granted before the patient owes anything, such as the self-pay discount applied when a charge is finalized, SHALL post as `self_pay_discount`. A reduction granted after an amount was owed SHALL post as one of the write-off kinds. The choice SHALL follow from the moment of the act, and no screen SHALL offer the user a choice between a discount and a write-off (self-pay brainstorm §6). A write-off SHALL follow the governance of PS-11 (ruling 129).

#### Scenario: Late coverage reverses a discount
- **WHEN** a self-pay patient turns out to have coverage for the visit
- **THEN** the `self_pay_discount` entry is reversed with a dated reason, and no write-off is recorded

### Requirement: RT-07 Price concessions, credit losses and charity care stay distinct
openPractice SHALL keep three reasons for not collecting apart, and SHALL record which applies, with its reason, at the moment of the decision (AI-12; framework §5A.2.6):
- a **price concession**, an amount the practice did not expect to collect;
- a **credit loss**, an amount the practice did expect to collect and did not;
- **charity care**, an amount the practice chose not to collect because of the patient's means.

How each is presented in the books is the accountant's decision, recorded in the policy record (RE-09), not this change's. The shipped kinds are a starting classification for the accountant to confirm or replace (design call CR-68, PROPOSED): `price_concession_write_off`, `small_balance_write_off` and `charity_care` reduce revenue, after RT-09 has used what the estimate held back; and a credit loss posts to `credit_loss_expense` under RT-10 and never changes revenue. Whether charity care may be shown as a deduction from revenue or must be kept out of revenue altogether is a question for the accountant. A different treatment SHALL be a new kind in a new version of the list (RT-08). Until the accountant has confirmed the classification, no accounting destination SHALL go live (AI-20).

#### Scenario: A balance expected to be collected is not paid
- **WHEN** a patient balance that was expected to be collected becomes uncollectible, and the accountant has chosen a credit-loss method (RT-10)
- **THEN** it posts as a credit loss under that method, not as a price concession, and revenue does not change

### Requirement: RT-08 The list is versioned, and what has posted is never remapped in place
The reason list SHALL be versioned. A new kind or category SHALL be an additive version. A kind or category that any entry has used SHALL never be removed, renamed or given a different bucket pair or label in place. Each destination SHALL map the reason codes to its own accounts through a mapping versioned per destination and posting mode (BAB-06), and a batch holding a line with no mapping SHALL be refused release (BAB-06).

#### Scenario: A kind's buckets change
- **WHEN** a new version proposes that an existing, used kind credit a different bucket
- **THEN** the version is refused, and a new kind is proposed instead

### Requirement: RT-09 A patient write-off first uses what the estimate already held back
A write-off of a patient balance that reduces revenue, whether a price concession, a small balance or charity care, SHALL first post as `patient_write_off_against_allowance`, up to the charge line's remaining `gross_to_net_allowance` balance (SL-08), and only the excess SHALL post with its write-off kind to `revenue_deduction` (design call CR-61, PROPOSED). The write-off record SHALL keep its RT-07 classification and reason for the whole amount, and its governance SHALL follow PS-11 for the whole amount. A write-off SHALL NOT also produce an estimate true-up (RE-05), because the part the allowance absorbs realizes what the estimate held back and the excess already reduces revenue. A credit loss does not come under this requirement (RT-10).

#### Scenario: A self-pay balance is written off after a partial payment
- **WHEN** a self-pay charge of 200.00 has a 60.00 discount and an estimator constraint of 20.00, the patient pays 100.00, and a person writes off the remaining 40.00 as a price concession
- **THEN** 20.00 posts as `patient_write_off_against_allowance` and 20.00 as `price_concession_write_off`, deductions total 100.00 (60.00 + 20.00 + 20.00), net revenue is 100.00, equal to what was collected, and no `estimate_decrease` posts

#### Scenario: The allowance already covers the write-off
- **WHEN** a charge line's remaining allowance is 15.00 and a person writes off a 10.00 small balance on it
- **THEN** all 10.00 posts as `patient_write_off_against_allowance`, revenue does not change, and 5.00 stays in the allowance for the next close re-estimate (RE-06)

### Requirement: RT-10 Credit losses follow the method the accountant chose, and nothing posts before that
The policy record (RE-09) SHALL state the practice's credit-loss method, chosen by the practice's accountant (design call CR-62, PROPOSED):
- **allowance method:** at each close the expected credit loss on open patient balances is provided for as `credit_loss_provision`, or released by its reversal, using rates the approved estimator version states (RE-06); a balance found uncollectible is then written off against the allowance as `credit_loss_write_off`;
- **direct method:** a balance found uncollectible is written off straight to expense as `credit_loss_direct_write_off`, with no provision.

openPractice SHALL NOT choose between them. Until the accountant has chosen, no credit-loss entry SHALL post: a balance a person judges uncollectible SHALL stay in `patient_receivable`, its decision SHALL be kept as owned work (TH-12), and no accounting destination SHALL go live (AI-20). A credit loss SHALL never change revenue or the estimate (RT-07, RE-05), and SHALL follow PS-11's governance.

#### Scenario: No method has been chosen
- **WHEN** a person judges a 75.00 balance uncollectible before the accountant has chosen a credit-loss method
- **THEN** nothing posts, the balance stays owed, and the decision waits as owned work

#### Scenario: The allowance method at close
- **WHEN** the accountant has chosen the allowance method and a period closes with open patient balances
- **THEN** the close posts the change in the expected credit loss as `credit_loss_provision`, and `credit_loss_expense` carries it
