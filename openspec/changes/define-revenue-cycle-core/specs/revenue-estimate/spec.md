## ADDED Requirements

### Requirement: RE-01 Every final charge line carries an estimate record
When a charge line is finalized (CG-04), openPractice SHALL create an estimate record for it (ruling 123; one record per charge line, design call CR-16, PROPOSED). The record SHALL hold:
- the gross charge;
- the constrained expected entitlement: what the practice expects to be entitled to, reduced so that a significant later reversal is not probable (AI-12, ruling 30);
- the estimator version, the cohort, the input window and references to the evidence used;
- the payer class and service category;
- the time it was computed.

The difference between the gross charge and the constrained expected entitlement SHALL post as a `gross_to_net_estimate` entry (RT-02), or, for a self-pay discount, as the `self_pay_discount` entry the charge already carries (CG-09). Where no approved estimator version exists yet, the charge SHALL still finalize, the estimate SHALL be recorded as awaiting an estimator, and its deduction SHALL post when the first version is approved, dated under AI-11. A revenue line SHALL never reach an accounting destination without its deduction (AI-12).

#### Scenario: An insured visit is finalized
- **WHEN** a 200.00 Medicare-class office visit is finalized under an approved estimator version that expects 114.00
- **THEN** an estimate record holds 200.00, 114.00, the version, cohort and evidence, and an entry of 86.00 posts as `gross_to_net_estimate`

#### Scenario: No estimator is approved yet
- **WHEN** a charge is finalized before the accountant has approved any estimator version
- **THEN** the charge is final and billable, its estimate is awaiting an estimator, and no batch can carry its revenue line until the deduction exists

### Requirement: RE-02 Only the practice's accountant approves an estimator version
An estimator version SHALL apply only after the practice's accountant approves it, and only from a stated effective date forward (AI-12, BAB-17, ruling 30). The approval SHALL never be opted in for agent signing, SHALL record the role used (`define-practice-readiness` SR-02), and SHALL never be self-approved (ruling 107). The practice's accountant SHALL be the outside accountant that rulings 139 to 141 define: engaged from outside the practice and neither an owner nor an employee, as `define-practice-readiness` SR-04 decides who counts as either (that change is a draft not yet on `main`); a licensed CPA whose licence is checked against the state board's public record and who attests in their own name; and holding a roster entry of the outside-accountant type, with only that type's recipe, which can read the books and approve accounting policy, estimator versions and the write-off threshold, and nothing else (`define-actors-agents-and-work-inbox` AM-01 and AM-02, BAB-17; ruling 141). Who may hold that entry, and the checks whose failure refuses an accountant's sign-off, are `define-practice-readiness` SR-04 (rulings 139 and 140). An agent MAY draft a proposed version at `propose` (TH-13). Until the roster holds such an accountant, no version SHALL be approved, no estimate SHALL be produced, and no accounting destination can go live (AI-20).

#### Scenario: An agent proposes a version
- **WHEN** an agent drafts a new estimator version and tries to approve it
- **THEN** the approval is refused whatever the opt-in settings, and the draft waits for the accountant

#### Scenario: The owner tries to approve
- **WHEN** the practice's physician-owner tries to approve an estimator version
- **THEN** the approval is refused, because the owner is not the practice's accountant

#### Scenario: An employed accountant tries to approve
- **WHEN** a licensed CPA whom the practice employs tries to approve an estimator version
- **THEN** the approval is refused, because an employee is not the outside accountant (ruling 139)

### Requirement: RE-03 The first method is simple and the accountant sets its numbers
The first estimator method SHALL be (ruling 123; design call CR-17, PROPOSED):
- **self-pay:** the price less the self-pay discount, less a collectibility constraint for the self-pay cohort. For a charge paid in full by the time it is finalized, the constraint SHALL be the one the approved version states for that case. The shipped starting value is zero; the accountant decides whether exposure that remains after payment, such as a Good Faith Estimate dispute that could force a refund, needs a constraint;
- **insured:** the gross charge multiplied by an expected-allowed ratio for the payer class and service category, less a constraint for denials and short payments.

The accountant SHALL set every ratio and constraint in the version they approve. No loaded payer fee schedule SHALL be an input (ruling 126). Each computed amount SHALL be rounded once, half away from zero, to the cent (BAB-18). Later methods, such as one learned from the practice's own remittances (FD-2), SHALL each be a new version under RE-02.

#### Scenario: A self-pay visit paid at checkout
- **WHEN** a self-pay visit priced 200.00 with a 60.00 discount is paid in full at checkout, under a version that keeps the shipped paid-in-full constraint of zero
- **THEN** its expected entitlement is 140.00

#### Scenario: An insured estimate rounds
- **WHEN** the version's ratios produce an expected entitlement of 101.235 for a charge
- **THEN** it is rounded once to 101.24, and every total adds the rounded amount

### Requirement: RE-04 A payer with no history gets a conservative estimate, never gross-then-adjust
A payer or cohort with no adjudication history SHALL receive the conservative ratio and constraint the approved version states for that case. There SHALL be no fallback that books the gross charge as revenue and adjusts it later (AI-12, ruling 30).

#### Scenario: A first claim to a payer
- **WHEN** a charge is finalized for a payer the practice has never billed
- **THEN** the estimate uses the version's conservative ratio, and the gross charge is never booked as net revenue

### Requirement: RE-05 New evidence makes a new true-up record, never an edit
Each evidence event SHALL be able to change the estimate: an adjudication (RM-07), a denial or an appeal outcome, a payment, a dispute opening or closing (RE-07), a write-off decision, or a coverage change. Each change SHALL be a new true-up record that references the estimate it changes and the evidence behind it, and SHALL post as `estimate_increase` or `estimate_decrease` (RT-02). No estimate record SHALL be edited (FD-3). Billing, cash and adjudication SHALL be evidence that updates the estimate and never a trigger that creates revenue by itself (AI-12). A true-up SHALL count only the part of the new evidence that the allowance did not already hold back. A patient write-off SHALL produce no true-up, because RT-09 posts it against what the estimate held back and its excess directly as a deduction; and a credit loss SHALL never change the estimate (RT-10). Counting either again would reduce revenue twice.

#### Scenario: Adjudication confirms the estimate
- **WHEN** an estimate expected exactly 120.00, with nothing held back, and the payer allows 120.00 and denies nothing
- **THEN** the contractual adjustment is realized against the allowance, and no true-up is recorded

#### Scenario: A denial lowers the estimate
- **WHEN** a payer denies 12.00 of a claim estimated at 114.00, whose version had already held back 6.00 for denials and short payments, and the practice disputes it
- **THEN** a true-up record lowers the expected entitlement by the other 6.00, so all 12.00 is held out of the recognized amount, and the original estimate is unchanged

### Requirement: RE-06 Close re-estimates run by themselves and leave a snapshot
At the end of each accounting period of each live destination, openPractice SHALL re-estimate, with the approved estimator version, every charge line that has an open balance or a nonzero `gross_to_net_allowance`, including a line whose thread has closed, so that allowance left on a settled line is released, post each difference as a true-up dated under AI-11, and store a reproducible snapshot of the balances, estimates, versions and evidence references (ruling 30, AI-12). Where the policy record's credit-loss method is the allowance method, the close SHALL also post the change in expected credit losses as `credit_loss_provision`, or its reversal, from the version's rates (RT-10). There SHALL be no approval step at close (FD-3), and openPractice SHALL NOT lock a period (BAB-04).

#### Scenario: A period closes before settlement
- **WHEN** a period closes while 40 claims are unpaid and the approved version now expects less on 3 of them
- **THEN** the three differences post as dated true-ups referencing the snapshot, with no approval step

### Requirement: RE-07 A disputed amount stays out of the recognized amount until the constraint is met
An amount under dispute, such as a denied amount under appeal or an amount inside a Good Faith Estimate dispute, SHALL stay out of the constrained expected entitlement until including it would not make a significant reversal probable (AI-12). The estimator version SHALL state when that test is met, and the practice's accountant approves that statement with the version.

#### Scenario: An appeal is won in part
- **WHEN** an appeal of 12.00 is decided with 8.00 overturned and 4.00 upheld
- **THEN** an `estimate_increase` of 8.00 is recorded when the outcome arrives, and the upheld 4.00 is written off against the allowance by a person under PS-11

### Requirement: RE-08 Estimates are checked against outcomes, and large misses become work
At each close, openPractice SHALL compare each cohort's estimates with the final entitlements of the threads that closed in the period, leaving out the estimate of any line reversed under CG-08, so that a corrected service is counted once (design call CR-102, PROPOSED). A cohort whose difference exceeds the threshold the approved version states SHALL raise a work item, owned under AM-06 and notifying the practice's accountant (design call CR-18, PROPOSED). The check SHALL change no estimate by itself.

#### Scenario: A cohort runs high
- **WHEN** a payer-class cohort's estimates exceed what closed threads actually received by more than the version's threshold
- **THEN** a work item is raised and the accountant is notified, and no estimate changes until a new version is approved

### Requirement: RE-09 The accountant's policy record gates accounting go-live
openPractice SHALL keep one record of the practice's revenue-recognition policy, approved by the practice's accountant under the same rules as RE-02: the reporting basis, the estimator, the receivable classification, the materiality approach and the reopening policy (D8, ruling 30), together with the classification of price concessions, credit losses and charity care, including whether charity care is presented as a deduction from revenue (RT-07); the credit-loss method (RT-10); and the material write-off amount and the rule that tests it (PS-11, ruling 129). No accounting destination SHALL go live until it is recorded (AI-20). A new version of the record SHALL apply from its effective date forward.

#### Scenario: Accounting is otherwise ready
- **WHEN** an accounting destination passes every check except that no approved policy record exists
- **THEN** go-live is refused
