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
Each kind SHALL fix the bucket it debits, the bucket it credits (SL-01) and the neutral label of `openpractice-accounting-integration/1.0` that its lines carry (AI-16). The initial list SHALL be (design call CR-12, PROPOSED; the practice's accountant confirms the classifications under D8):

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
| `payer_write_off_against_allowance` | `gross_to_net_allowance` | `payer_receivable` | `write_off` |
| `small_balance_write_off` | `revenue_deduction` | `patient_receivable` | `write_off` |
| `price_concession_write_off` | `revenue_deduction` | `patient_receivable` | `write_off` |
| `charity_care` | `revenue_deduction` | `patient_receivable` | `write_off` |
| `credit_loss_provision` | `credit_loss_expense` | `credit_loss_allowance` | `credit_loss` |
| `credit_loss_write_off` | `credit_loss_allowance` | `patient_receivable` | `credit_loss` |
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
| `payer_interest` | `cash_clearing` | `non_patient_income` | `payer_interest` |
| `unidentified_receipt` | `cash_clearing` | `unidentified_receipts` | `unidentified_receipt` |
| `unidentified_receipt_identified` | `unidentified_receipts` | `unapplied_credit` | `unidentified_receipt_reclassified` |
| `legacy_collection` | `cash_clearing` | `legacy_collections` | `legacy_collections` |
| `other_route_share` | `cash_clearing` | `other_route_share` | `other_route_share` |
| `deposit_explained` | `deposited` | `cash_clearing` | `deposit_clearing_release` |

An entry SHALL use the pair and label its kind fixes. A money event that no kind fits SHALL become a work item and SHALL NOT post.

#### Scenario: A contractual adjustment is posted
- **WHEN** a payer allows 120.00 on a 200.00 charge
- **THEN** an entry of kind `contractual_adjustment` moves 80.00, debiting `gross_to_net_allowance` and crediting `payer_receivable`, with the label `contractual_adjustment`

#### Scenario: A payer-level adjustment no kind fits
- **WHEN** a remittance carries a payer-level adjustment that no kind in the list fits
- **THEN** nothing posts for it, and a work item is raised for the biller

### Requirement: RT-03 Service categories are shipped and benign, and each catalog item maps to one
The service categories SHALL be a fixed list the product ships (ruling 125). The initial list SHALL be `office_visit`, `procedure`, `lab_work`, `lab_test`, `imaging`, `injection_and_supply`, `missed_appointment` and `other_patient_service` (design call CR-13, PROPOSED). No category SHALL name a condition, a diagnosis or a sensitive kind of care. Each catalog item SHALL map to exactly one category (SC-05). The mapping SHALL be versioned and effective-dated; a change SHALL apply from its effective date onward, and an amount already posted under the old category SHALL move only by a posted reclassification (framework §5A.2.9).

#### Scenario: An item is remapped
- **WHEN** an administrator remaps a catalog item from `procedure` to `office_visit` from the first of next month
- **THEN** charges dated before that date keep `procedure`, and charges from that date carry `office_visit`

### Requirement: RT-04 The service category reaches accounting only in summary-journal totals
An entry's service category SHALL reach an accounting destination only on `summary-journal` lines, where AI-15 already summarizes by benign service category and no line identifies a person. It SHALL NOT appear on any `customer-detail` posting, whose lines carry only neutral labels that never reveal the type of service (AI-16). The accounting contract does not yet carry a service-category field; proposed amendment PA-1 in `design.md` adds it (ruling 125). Until that amendment is adopted, no summary-journal batch holding revenue or deduction lines from this change SHALL be released, and no category SHALL be folded into `reason_category` in its place (design call CR-14, PROPOSED).

#### Scenario: A customer-detail invoice line
- **WHEN** a self-pay visit is posted to a destination in `customer-detail` mode
- **THEN** the line carries the neutral label `patient_services_revenue` and no service category

#### Scenario: The amendment has not landed
- **WHEN** a summary-journal batch with revenue lines is ready, and the accounting contract still has no service-category field
- **THEN** release is refused and the reason names the missing field

### Requirement: RT-05 Payer adjustment codes are kept as received and normalized by a versioned table
Every adjustment a payer reports SHALL keep its codes exactly as received: the group code, the claim adjustment reason code and any remark codes. Each SHALL also be normalized to a kind (RT-02) and a plain-language label by a versioned normalization table that the product ships from license-free code sets (FD-11). A code the table does not cover SHALL become a work item, and that adjustment SHALL NOT post until a person classifies it. A change to the table SHALL apply only to adjustments received after its effective date.

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
- a **price concession**, an amount the practice did not expect to collect, which reduces revenue;
- a **credit loss**, an amount the practice did expect to collect and did not, which is an expense kept apart from revenue;
- **charity care**, which is not revenue.

The practice's accountant SHALL confirm how each maps to the books under D8 (ruling 30) before accounting goes live.

#### Scenario: A balance expected to be collected is not paid
- **WHEN** a patient balance that was expected to be collected becomes uncollectible
- **THEN** it posts as a credit loss, not as a price concession, and revenue does not change

### Requirement: RT-08 The list is versioned, and what has posted is never remapped in place
The reason list SHALL be versioned. A new kind or category SHALL be an additive version. A kind or category that any entry has used SHALL never be removed, renamed or given a different bucket pair or label in place. Each destination SHALL map the reason codes to its own accounts through a mapping versioned per destination and posting mode (BAB-06), and a batch holding a line with no mapping SHALL be refused release (BAB-06).

#### Scenario: A kind's buckets change
- **WHEN** a new version proposes that an existing, used kind credit a different bucket
- **THEN** the version is refused, and a new kind is proposed instead
