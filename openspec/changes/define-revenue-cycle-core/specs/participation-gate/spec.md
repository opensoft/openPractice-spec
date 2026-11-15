## ADDED Requirements

### Requirement: EG-01 Participation is a dated record per provider, payer and place
openPractice SHALL record a provider's participation with a payer as a dated record for one billing entity (TIN), one rendering provider (NPI), one payer, one payer product and one location, with its effective date, any end date, its status (`applied`, `effective`, `ended` or `denied`) and the evidence the payer gave (design call CR-43, PROPOSED). A participation record SHALL never be edited; a corrected date SHALL be a dated reversal and a new record, so that claims released under the old one stay explainable. An application in progress SHALL be tracked as an `enrollment_case` thread (TH-08).

#### Scenario: A payer backdates an approval
- **WHEN** a payer approves a provider effective from a date six weeks ago
- **THEN** a participation record is added with that effective date, citing the approval, and the enrollment case moves to `remitted`

### Requirement: EG-02 One check at two moments, with five answers
openPractice SHALL check participation for a visit's billing entity, rendering provider, payer product, location and date of service, and SHALL answer one of five values: `effective`, `not_yet` (an application is pending), `ended`, `never` (denied, or the practice does not bill this payer for this provider) or `unknown` (no record or case exists). The same check SHALL run at booking, where any answer but `effective` warns and never refuses (AP-03), and when a claim is built, where it decides (EG-03).

#### Scenario: A booking with a provider not yet approved
- **WHEN** staff book a visit with a provider whose application to the patient's payer is pending
- **THEN** the booking warns `not_yet`, naming the expected date where known, and is allowed

### Requirement: EG-03 A claim that fails the check is held, never rejected
When a claim is built for a visit whose check is not `effective`, openPractice SHALL hold it, and SHALL neither reject it nor submit it (ruling 128). The visit thread SHALL stay at `charged` with the condition `held`, the reason `participation_not_effective`, and a structured release condition naming the participation tuple and the date of service (TH-06). An `unknown` answer SHALL also mint an `enrollment_case` thread, linked `held_by` from the held visit (TH-09).

#### Scenario: No case exists
- **WHEN** a claim is built for a provider and payer with no participation record and no case
- **THEN** the claim is held, an enrollment case is minted for the pair, and the held visit links to it

### Requirement: EG-04 Every eligible held claim is released in one action once the provider is enrolled
When a participation record makes the tuple effective on or before a held claim's date of service, openPractice SHALL release every such held claim in one action, re-check each against the claim scrub (CL-04), and notify staff with the count and total (ruling 128; FD-5 tier 2). Released claims SHALL join the claim batch and SHALL still need the batch's explicit release (CL-05); release from the hold is not submission.

#### Scenario: Seven claims wait on one approval
- **WHEN** an approval effective from the first of last month arrives for a provider with seven held claims, all dated after that day
- **THEN** all seven are released in one action and re-scrubbed, staff see the count and total, and they wait for the next batch release

### Requirement: EG-05 Held claims the approval does not cover need a named decision
A held claim whose date of service falls before the effective date, or whose answer is `ended` or `never`, SHALL become owned work that needs a named person's decision (design call CR-44, PROPOSED): correcting the rendering provider where it was recorded wrongly, converting the visit to self-pay, or writing it off under PS-11. Converting a visit to self-pay tells the patient an amount for the practice's own enrollment gap, so it SHALL be done only by a named human, and the patient SHALL NOT be billed before the self-pay discount is applied (GF-11). Substitute and supervising billing arrangements, such as a locum or reciprocal billing, are not part of this change.

#### Scenario: Two claims fall before the effective date
- **WHEN** an approval is effective from the 10th and two held claims are dated the 3rd and the 7th
- **THEN** those two stay held as work, and a named person decides what happens to each

### Requirement: EG-06 The deadline escape releases only a pending case's claims
Every hold this capability places SHALL name its FD-5 deadline escape. When a held claim reaches the filing threshold (CL-06) while its answer is `not_yet`, the claim SHALL be released into the claim batch at tier 3, with notification and provenance, and SHALL still need the batch's explicit release under CL-05 and CL-06. When the answer is `ended`, `never` or `unknown`, the escape SHALL NOT submit the claim; it SHALL escalate the work instead (design call CR-45, PROPOSED, narrowing FD-5's escape as the enrollment-gated-billing brainstorm proposes).

#### Scenario: A denied application at the deadline
- **WHEN** a claim held because the provider's application was denied reaches the filing threshold
- **THEN** the claim is not submitted, and the work is escalated to the practice manager

### Requirement: EG-07 Deposit enrollment never holds a claim
A payer's deposit enrollment for EFT (BAB-11) SHALL NOT hold any claim. Only participation SHALL gate a claim (BAB-11).

#### Scenario: EFT enrollment is pending
- **WHEN** a payer's EFT enrollment for the practice is pending and a claim to that payer is ready
- **THEN** the claim is not held for it, and the payer's money is expected by check
