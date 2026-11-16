## ADDED Requirements

### Requirement: PY-01 openPractice records patient payments and never moves money
openPractice SHALL record each patient payment the practice receives: its amount and currency, the date received, the guarantor account, the method (cash, check, card on a standalone terminal, or another method on a shipped list), the person who recorded it, and an external reference such as a check number or a terminal approval code (ruling 122). It SHALL NOT store a full card number, a card security code, or a full bank account or routing number (BAB-10). Recording a payment SHALL post `patient_payment`, `patient_payment_unapplied` or `prepayment` (PY-03), and SHALL NOT instruct any bank, processor or person to move money (BAB-07). Recording a payment SHALL be FD-5 tier 1, done by the person who took it.

#### Scenario: A copay at the desk
- **WHEN** a patient pays a 25.00 copay in cash at checkout for today's insured visit
- **THEN** the payment is recorded against the guarantor account with the method, date and recorder, as a credit because the visit's charge is not yet final (PY-03); because the visit is insured, PY-06 counts that credit as awaiting the payer's answer, and it is allocated to the visit's charge line once the guarantor account owes on it (PY-04)

#### Scenario: Someone types a card number
- **WHEN** a user tries to record a full card number as the payment's reference
- **THEN** it is refused, and only a terminal approval code may be recorded

### Requirement: PY-02 Every payment gets one frozen receipt
Every recorded payment SHALL produce one receipt that states the amount, the date, the method and what it was applied to, frozen as issued (TH-14). A correction SHALL produce a new dated receipt that replaces the first; the first SHALL be kept and never reissued as if new. A receipt SHALL be handed over or sent only through a channel the patient's communication instructions allow (PR-05).

#### Scenario: A receipt is corrected
- **WHEN** a payment is moved to a different visit after its receipt was printed
- **THEN** a new receipt is issued that replaces the first, and both are kept

### Requirement: PY-03 Money received before the service is a liability, and money not yet applied is a credit
A payment received before the service it pays for is given SHALL post as `prepayment` to `contract_liability` (framework §5A.2.7; design call CR-11, PROPOSED). When that service's charge is finalized, it SHALL be applied with `prepayment_applied` only up to what the guarantor account then owes on the charge, and the rest SHALL move to `unapplied_credit` with `prepayment_to_credit` in the same finalization (design call CR-92, PROPOSED). For an insured visit the guarantor owes nothing until the payer answers (RM-07), so the whole prepayment moves to the credit at finalization and is allocated from there once the patient's share is known (PY-04); PY-06 counts that credit as awaiting application until its wait ends. A payment received after the service and not yet allocated, or received beyond what the guarantor account owes, SHALL post to `unapplied_credit` (AI-13). No payment SHALL be recorded as revenue when it is received (AI-12).

Money already received SHALL reach `unapplied_credit` without the payment being reversed or recorded again (design call CR-56, PROPOSED):
- a prepayment that will not be applied SHALL move from `contract_liability` as `prepayment_to_credit`, posted with the act that makes it unappliable and under that act's governance: the cancellation, no-show or left-unseen disposition of the visit it paid for (AP-04), a disposition at `seen` with nothing to bill (TH-04), or the finalization of a charge smaller than the prepayment (CG-09). A prepayment whose visit's charge is still not finalized when the maximum waiting period of PY-06 ends, counted from the date of service, SHALL raise work then, owned by the visit's owner, to move it; because no act causes that move, a person SHALL post it at FD-5 tier 2, with a notification (design call CR-66, PROPOSED); the work SHALL be due at once; the resulting credit counts as unapplied from the end of that waiting period, whenever the move is posted, and never waits; and a charge finalized later is paid from the credit under PY-04 (design call CR-92, PROPOSED). PY-06 SHALL watch every credit this bullet creates, which never waits and, but for that case, counts as unapplied from the act that moves it;
- whenever any posting leaves a charge line's `patient_receivable` below zero, whatever its cause, judged once the act that makes it is complete, so that for an act of several entries, such as a PY-09 reversal, the act's net result is what counts, such as a correction, a void, a retraction or a payer's corrected remittance (RM-10), the amount below zero SHALL move to `unapplied_credit` as `patient_overpayment_to_credit`, posted with that posting and under its governance (design call CR-92, PROPOSED).

The credit is then applied, moved to `refund_liability` (PY-07) or worked under PY-06.

#### Scenario: A deposit before a procedure
- **WHEN** a patient pays 100.00 a week before a scheduled self-pay procedure
- **THEN** it posts as a prepayment, and it is applied to the procedure's charge when the charge is finalized

#### Scenario: The prepayment is larger than the final charge
- **WHEN** a patient prepaid 150.00 and the procedure's final self-pay charge, after the discount, is 120.00
- **THEN** 120.00 is applied with `prepayment_applied`, and the other 30.00 moves to `unapplied_credit` with `prepayment_to_credit` in the same finalization

#### Scenario: A prepayment before an insured visit
- **WHEN** a patient prepays 50.00 before an insured visit, the charge is finalized, and Medicare later assigns a 24.00 share
- **THEN** the 50.00 moves to `unapplied_credit` at finalization, 24.00 of it is allocated to the patient's share when the remittance posts, and the other 26.00 stays a credit that PY-06 watches; no charge line's receivable goes below zero

#### Scenario: An insured visit's note is never finished
- **WHEN** a patient prepaid 50.00 before an insured visit, the visit was seen, and its charge is held under CG-07 because the note is never finished
- **THEN** when the maximum waiting period ends, 60 days after the date of service, work due at once is raised for the visit's owner, and a person moves the 50.00 from `contract_liability` to `unapplied_credit` as `prepayment_to_credit`; the credit counts as unapplied from that 60th day even if the move is posted later, PY-06 watches it, and if the charge is finalized later, the patient's share is paid from that credit

#### Scenario: A paid charge is retracted
- **WHEN** a visit's charge was paid by the payer and by the patient, and a retraction voids the charge line (EB-15)
- **THEN** the patient's 24.00 moves to `unapplied_credit` as `patient_overpayment_to_credit` with the void, the payer's excess becomes a `payer_overpayment` (RM-09), and no payment is reversed

### Requirement: PY-04 Payments are allocated when they are taken, and unapplied money is owned work
A payment SHALL be allocated to charge lines when it is recorded wherever the person taking it knows what it pays for (SL-07). Otherwise it SHALL be allocated to the guarantor account's oldest undisputed charge lines first, unless the guarantor directs otherwise (design call CR-40, PROPOSED). Money left unapplied SHALL appear as owned work (TH-12) and SHALL never be left unowned. An allocation SHALL apply a guarantor account's money only to that account's charge lines (SL-07). An agent MAY allocate payments at most at `act_with_notice` (ruling 132).

#### Scenario: An unexplained check
- **WHEN** a guarantor mails a 50.00 check with no note and owes on two visits
- **THEN** it is allocated to the older undisputed visit first, and the guarantor's direction, if any arrives, overrides it by reallocation

### Requirement: PY-05 openPractice generates the deposit slip
openPractice SHALL generate a deposit slip for the cash and checks recorded and not yet deposited, per practice bank account in the register (BAB-09) (ruling 130). The slip SHALL list each item by its external reference and amount, carry its total, and show only the masked account number (BAB-10). Each slip SHALL be one deposit, and its total SHALL be the amount that deposit explanation matches to the bank line (BAB-14), so that desk money reconciles by construction. Card settlements SHALL NOT appear on a slip. The cash-drawer count and the day's close sheet are not part of this change (ruling 130).

#### Scenario: The day's checks and cash are deposited
- **WHEN** the front desk prepares the bank deposit for 85.00 in cash and two checks of 30.00 each
- **THEN** openPractice prints one slip for 145.00 listing the three items, and the deposit is later matched to its bank line by that total

### Requirement: PY-06 Money held that is not the practice's becomes a credit-balance case with a clock
When money sits in `unapplied_credit` or `refund_liability` and is not applied within the period the approved parameters set, or is identified as owed back, openPractice SHALL mint a `credit_balance` case thread (TH-08) recording its cause: a patient credit or a payer overpayment. Its clock SHALL come from a parameter that counsel has verified for that cause, such as a payer overpayment's report-and-return deadline (framework §5A.2.7). Until such a parameter is verified, the case SHALL be due at once and owned (design call CR-41, PROPOSED). For an unidentified receipt the case SHALL be minted when the receipt posts, so that every entry for that money names a thread (SL-03): the period for identifying it runs from receipt, and the case's clock is the end of that period until it passes, then the clock for its cause as above (design call CR-5, PROPOSED). Identifying the receipt records the case's outcome; the entry that moves the money names, for its other side, the thread the money was paid toward, or the case itself where it was paid toward none, and the case then covers that credit as a patient credit. A credit holding money paid toward an insured visit before the patient's share is known SHALL count as awaiting application, not as unapplied, while a claim for that visit can still be answered: a prepayment moved to credit at finalization (PY-03), a copay or other payment taken at checkout (PY-01), or money a self-pay patient paid before coverage turned up (GF-11). The wait SHALL end at the earliest of these (ruling 180 rules the wait, its limit of 60 days, and its end when the payer answers, the claim is dropped or the visit becomes self-pay; the other ends below are design call CR-92, PROPOSED):
- the posting of the remittance that sets the patient's share (RM-07);
- the claim being voided (CL-08) or dropped (CL-09), or its payer balance being written off (PS-11);
- the visit being converted to self-pay (EG-05), found to have had no coverage (GF-11), or given a disposition at `seen` with nothing to bill (TH-04);
- the end of the maximum waiting period that the approved parameters set, which SHALL NOT exceed 60 days (ruling 180; counsel confirms the 60 days), counted from the later of the date of service and the day the practice received the money, never from when a charge was finalized, so that the practice's own delay cannot lengthen the wait (design call CR-92, PROPOSED), with a shipped starting value of 60 days.

From then on the credit counts as unapplied, and its period, and any case for it, start then. A credit that an AP-04 disposition creates, for a cancellation, a no-show or a patient who left unseen, SHALL NOT wait (ruling 180, for a cancelled or missed visit), because no claim follows it: it counts as unapplied from the disposition. While a credit waits, it SHALL be shown as owned work naming the claim it waits on (TH-12). Identifying the owner of an unidentified receipt SHALL be done by a person at FD-5 tier 2, with a notification: it posts `unidentified_receipt_identified` for a guarantor account, or, for a payer's payment that arrived before its remittance, links the receipt to that remittance, naming the amount released from it where the remittance's receipts hold more than its payment total, and the remittance releases it with `unidentified_receipt_to_payer` under RM-01, in the remittance's posting or, where the remittance has already posted, at the link, the remittance's own entries naming the threads it pays (design call CR-66, PROPOSED). The one exception is RM-01's automatic link, made only when the remittance's trace number and payment amount both equal the receipt's (design call CR-95, PROPOSED).

#### Scenario: The insurer never answers
- **WHEN** a patient's 50.00 prepayment for an insured visit, paid before the visit, moved to credit when the charge was finalized, a week after the date of service, and 60 days after the date of service no remittance has posted and the claim is still open
- **THEN** the wait ends, counted from the date of service and not from the finalization, the credit counts as unapplied, and PY-06's period and any credit-balance case start from that day, with the open claim still shown on the thread

#### Scenario: A copay on a visit whose note is never finished
- **WHEN** a 25.00 copay is taken at checkout for an insured visit whose documentation is never finished, so its charge is never finalized and no claim is built
- **THEN** the copay's wait ends 60 days after the date of service, the credit counts as unapplied, and PY-06's period starts then

#### Scenario: A cancelled insured visit
- **WHEN** a patient who prepaid 40.00 toward an insured visit cancels it
- **THEN** the 40.00 moves to credit with the cancellation and counts as unapplied at once, because no claim will follow

#### Scenario: A payer overpays
- **WHEN** a remittance pays 20.00 more than the claim's balance
- **THEN** the excess moves to `refund_liability` as `payer_overpayment`, a credit-balance case is minted with cause payer overpayment, and its clock is the verified deadline for that cause

### Requirement: PY-07 Refunds are human-only and recorded only when issued
A refund leaving the practice SHALL be FD-5 tier 4, signed by a named human only (ruling 122, ruling 33, BAB-17). An agent MAY only propose one (`refund.request`, ruling 132). Moving a patient credit to `refund_liability` as `patient_credit_to_refund` moves no money, and SHALL be done by a person at FD-5 tier 2, with a notification (design call CR-66, PROPOSED). openPractice SHALL record a refund as issued only after it was issued outside openPractice, with its method and reference, and SHALL post `refund_issued` then (BAB-07). Each refund disbursement on the bank SHALL be matched to its issued refund by deposit explanation (BAB-14, AI-17), which posts `bank_debit_explained` (SL-13). Recording that a refund was issued, with `patient_credit_to_refund` and `refund_issued`, moves no money by itself and is built in feature 2; matching the refund to the bank statement stays in feature 4 (ruling 175).

#### Scenario: An agent proposes a refund
- **WHEN** an agent finds a 15.00 patient credit and proposes a refund
- **THEN** the proposal waits in the work inbox, and only a named human can sign it

### Requirement: PY-08 A staff member's own refund follows the own-record rule
A refund to a staff member's own guarantor account, or to an account whose party holds a role the staff member also holds, SHALL follow PR-15's rule for a staff member acting on their own record and its one-person alternative (design call F-7 of `define-mvp-practice-foundation`): another human roster entry who holds the grant decides. Only where nobody else on the roster holds the permission does the alternative apply (ruling 192): it records both roles the person acts in and notifies the outside accountant, listing everyone else who was on the roster (ruling 192), and while the roster holds no outside accountant other than the actor, the refund waits (ruling 144; PR-15, `define-actors-agents-and-work-inbox` WI-03).

#### Scenario: A signer's own refund
- **WHEN** a physician on the roster who holds the refund grant, and is also a patient, would sign a refund to their own guarantor account, and another named human holds the grant
- **THEN** that other person decides the refund

### Requirement: PY-09 A chargeback or a returned item reopens what it paid
A card chargeback or a returned check SHALL undo the payment part by part, according to where each part of it now sits, all of it in one act that posts the part still allocated first, so that PY-03's below-zero rule applies only to the act's net result (design call CR-93, PROPOSED):
- the part that still stands allocated to charge lines SHALL post as `chargeback` or `returned_item`, and that entry SHALL itself be the reversal of the payment's effect: it reopens the patient receivable on those lines, referencing the payment and its allocation (SL-03), and no other reversal of that part SHALL post;
- every other part not yet refunded, wherever it now sits (`contract_liability`, `unapplied_credit`, `refund_liability`, `unidentified_receipts` or `legacy_collections`), SHALL be undone by reversing under SL-05, newest first, every entry that moved that part from the payment to the bucket that now holds it, such as `patient_credit_to_refund`, `prepayment_to_credit`, `patient_overpayment_to_credit` or `unidentified_receipt_identified`, and then the payment's own entry for that part, each reversal recording the chargeback or return reason and the bank line. Every bucket the part passed through then returns to where it was, and no credit is left for money the patient has already taken back. Any refund of that part requested or signed but not yet recorded as issued SHALL be withdrawn, with work raised for its signer (PY-07);
- a part already refunded (`refund_issued`) has been paid out twice, so the guarantor account owes the practice that amount: it SHALL post as `chargeback` or `returned_item` to that account's `patient_receivable`, with no charge line, on the payment's thread, referencing the payment, the refund and the bank line and recording the reason, and work SHALL be raised for the practice to collect it. That receivable SHALL count as a charge line for SL-07, PY-03's below-zero rule, PY-04, PS-03 and PS-12, and MAY be written off only as a credit loss (RT-10).

No other receivable SHALL be raised for any part. Any fee SHALL post separately as `chargeback_fee` or `bank_fee` (AI-13). A bank debit for it SHALL clear under SL-13. Recording a chargeback or a returned item SHALL be done by a person at FD-5 tier 2, with a notification (design call CR-66, PROPOSED). A thread the payment had closed SHALL move forward again (TH-07).

#### Scenario: A deposit check bounces
- **WHEN** a 300.00 deposit check for a procedure not yet given is returned unpaid
- **THEN** the 300.00 prepayment is reversed out of `contract_liability` under SL-05 with the return reason and the bank line, no patient receivable rises, and no credit remains to refund if the procedure is later cancelled

#### Scenario: A deposit check bounces after the visit was cancelled
- **WHEN** a 300.00 deposit check was moved to credit when the procedure was cancelled, the credit was moved to `refund_liability`, and the check is returned unpaid before the refund is issued
- **THEN** the move to `refund_liability` is reversed, then the move to credit, then the prepayment, newest first; `contract_liability`, `unapplied_credit` and `refund_liability` all return to where they were, the unissued refund is withdrawn, and no credit is left

#### Scenario: A corrected charge, then a bounced check
- **WHEN** a 100.00 charge was paid in full by check, the charge was corrected to 60.00 so 40.00 moved to credit as `patient_overpayment_to_credit`, and the check is then returned unpaid
- **THEN** one act posts `returned_item` for the 60.00 still allocated, then reverses the move to credit and the payment's entry for the 40.00; the line owes 60.00, no credit is left, and the below-zero rule does not fire, because the line never ends the act below zero

#### Scenario: A refunded credit is charged back
- **WHEN** a patient's 80.00 card payment for a visit that was later cancelled was refunded from credit, and the card issuer then charges back the original 80.00
- **THEN** 80.00 posts as `chargeback` to the guarantor account's `patient_receivable` with no charge line, citing the payment, the refund and the bank line, and work is raised to collect it; no credit is reversed, because none is left

#### Scenario: A returned check
- **WHEN** a 60.00 check is returned unpaid with a 10.00 bank fee
- **THEN** one `returned_item` entry reopens the receivable by 60.00 with no separate reversal of the allocation, the 10.00 fee posts as `bank_fee`, both bank debits clear under SL-13, and the visit's thread reopens

### Requirement: PY-10 The payment processor and the pay link come last, through a governed contract
Taking card payments through a processor, refunding to the original card, and the no-account pay link of FD-10(b) SHALL come in the payment-processor feature (feature 6), through an adapter whose contract is defined before that feature starts (design call CR-24, PROPOSED). The pay link SHALL NOT depend on the patient-app gateway (PG-12), SHALL honor the patient's communication instructions (PR-05), and SHALL keep card data out of openPractice entirely.

#### Scenario: A pay link before feature 6
- **WHEN** a feature before feature 6 proposes sending pay links
- **THEN** it is out of that feature's scope

### Requirement: PY-11 A payment hold never blocks a payment
A payment hold SHALL NOT block or delay recording a payment, issuing a receipt, or any way of paying (PH-02).

#### Scenario: A held patient pays
- **WHEN** a patient on a payment hold pays at the desk
- **THEN** the payment is recorded and receipted as usual, and the hold is re-evaluated (PS-09)
