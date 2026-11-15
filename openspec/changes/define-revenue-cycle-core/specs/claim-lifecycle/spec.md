## ADDED Requirements

### Requirement: CL-01 A claim asks one payer to pay for one visit
A claim SHALL be a request to one payer for payment for one visit thread, under the coverage that holds one position (primary, secondary or tertiary) in the patient's coverage order on the date of service (PR-07). Each claim line SHALL reference the charge line it bills (CG-01). A visit SHALL have at most one open claim per coverage position; a correction SHALL be a new version of that claim (CL-08), never a second claim.

#### Scenario: A primary claim
- **WHEN** a Medicare patient's visit with two final charge lines is claimed
- **THEN** one claim to the Medicare coverage in the primary position holds two claim lines, each referencing its charge line

### Requirement: CL-02 Claims come in a fixed payer order, and the data shape allows every payer
Self-pay visits SHALL produce no claim. This change SHALL build claims first for Medicare Part B as the primary payer (ruling 113). A claim to any other payer, and every secondary claim, SHALL be recorded through the manual path (CP-04) until a later feature builds it, so that nothing owed to the practice goes unrecorded (ruling 166). Nothing in a claim record SHALL assume one payer or one coverage position: each claim SHALL record its payer, its coverage version and its position, and a later payer's claim SHALL be able to carry what earlier payers decided, which remittance posting keeps per service line (RM-06).

#### Scenario: A commercial primary claim in the MVP
- **WHEN** a visit's primary coverage is a commercial plan
- **THEN** openPractice builds no electronic claim, a work item asks the biller to send it outside and record it through the manual path, and the receivable stays tracked

### Requirement: CL-03 A claim is built from final charge lines, and its coverage version is frozen on it
A claim SHALL be built only from final charge lines (CG-04). It SHALL carry the billing and rendering providers, the place of service, the diagnosis pointers of the charge set, the line amounts and the total, and SHALL reference the exact coverage version it bills, which SHALL stay retrievable unchanged (PR-07). A charge line on a submitted claim, including a claim recorded through the manual path (CP-04), SHALL NOT be built into a second claim to the same coverage position. An agent MAY build claims at most at `act_with_notice` (ruling 132).

#### Scenario: The member identifier is corrected later
- **WHEN** a member identifier is corrected after a claim used the earlier coverage version
- **THEN** the claim still references the earlier version, and any resubmission is a replacement that uses the new one

### Requirement: CL-04 A claim is scrubbed before it can be released
Before a claim can join a released batch, openPractice SHALL check it against its readiness: final documentation (EB-14); that no fact behind any of its lines is retracted or superseded (EB-15); participation (EG-02); authorization (EV-02); any open identity review (EB-18); the offering route (BAB-16); that no same-person reconciliation is open for the service date (CG-05); and the checklist its claim-pathway adapter fills in (CP-02). A claim that fails SHALL be held with the hold reason that names the failure (TH-06), or SHALL become owned work where no hold reason applies. The result of each scrub SHALL be recorded on the claim. Where a recorded deadline escape has already released the claim from a hold, the scrub SHALL record that check's failure and SHALL NOT hold the claim again for it, so that an escape is never undone by the scrub (design call CR-73, PROPOSED).

#### Scenario: A missing authorization
- **WHEN** a claim for a service needing an authorization has none recorded
- **THEN** the scrub holds it with reason `waiting_on_authorization`, and it cannot join a batch

#### Scenario: A retracted fact behind a final line
- **WHEN** an agent builds a claim from final lines, one of which uses a diagnosis the EHR has since retracted
- **THEN** the scrub holds the claim with reason `clinical_fact_retracted`, and it cannot join a batch or be released by any escape

### Requirement: CL-05 A named signer releases each claim batch
Claims SHALL leave openPractice only in batches, each released by an explicit release decision of a named signer (ruling 119). The signer SHALL be a named human: an agent SHALL stay at `propose` for `claim_batch.release` (ruling 132). The release SHALL be a sign-off under BAB-17 recording the role used (`define-practice-readiness` SR-02). A claim in the batch on a record where the signer's own party holds a role SHALL follow PR-15's rule for a staff member acting on their own record (design call F-7 of `define-mvp-practice-foundation`). There SHALL be no scheduled automatic release, except the deadline escape of CL-06. Release SHALL freeze each claim version as submitted (CL-07).

#### Scenario: The biller releases the day's claims
- **WHEN** a biller reviews a batch of twelve scrubbed claims and releases it
- **THEN** the release records the biller, the role and the time, and each claim is frozen as submitted

#### Scenario: An agent tries to release
- **WHEN** an agent that built a batch tries to release it
- **THEN** the release is refused, and the batch waits for a named human

### Requirement: CL-06 A claim nearing its filing deadline is released by the escape, with notice and a cancel window
A claim is ready when no hold is in force on it and its last scrub found no failure other than one a recorded escape has already released (CL-04), or when every hold in force on it is one whose defining requirement names this escape: `participation_not_effective` while the answer is `not_yet` (EG-06), `offering_route_unresolved` (BAB-16), and `identity_review` once EB-17's attestation is recorded (EB-18) (design call CR-73, PROPOSED). Any other hold keeps the claim from being ready. A claim that is ready but unreleased when its filing clock reaches the filing threshold SHALL be released on its own at FD-5 tier 3: staff are notified, a cancel window runs, and the release records its provenance (ruling 119, EB-18). The filing threshold SHALL be a number of days before the filing deadline that the practice sets, defaulting to 14 (ruling 167), and the cancel window SHALL end before the deadline (design call CR-47, PROPOSED). The escape SHALL NOT release a claim held for `clinical_fact_retracted`, `waiting_on_clinical_documentation`, `waiting_on_authorization` or `same_person_reconciliation`, nor one whose participation answer is `ended`, `never` or `unknown` (EG-06); those SHALL be escalated as work instead, and a claim held for authorization MAY then be released only by a named human with a recorded reason (EV-02; ruling 181).

#### Scenario: Nobody released the batch
- **WHEN** a ready claim is still unreleased 14 days before its filing deadline
- **THEN** staff are notified, a cancel window runs, and if nobody cancels the claim is released with the escape recorded as its provenance

#### Scenario: A pending enrollment at the deadline
- **WHEN** a claim held only because the provider's application is pending (`not_yet`) reaches the filing threshold
- **THEN** it counts as ready, the escape releases it with notice and a cancel window, and the scrub records the participation failure without holding it again

#### Scenario: A retracted fact at the deadline
- **WHEN** a claim held because a diagnosis was retracted reaches the filing threshold
- **THEN** it is not released, and the work is escalated

### Requirement: CL-07 The claim as submitted is frozen
Each claim version SHALL be frozen exactly as submitted, with the time and the batch it left in (EB-16, TH-14). openPractice SHALL never change a submitted version, and SHALL never present it as current clinical truth (EB-16). The payer's claim control number SHALL be recorded on the version when the payer gives it.

#### Scenario: A corrected diagnosis after submission
- **WHEN** a diagnosis is corrected after a claim was submitted
- **THEN** the submitted version still shows the original diagnosis, and the correction is shown separately (EB-16)

### Requirement: CL-08 A replacement or a void is a new version that names the original
A correction to a submitted claim SHALL be a replacement version or a void version that references the version it corrects and carries the payer's claim control number of the original (EB-15). openPractice SHALL refuse a replacement or void that lacks the original's control number, because without it the payer would treat it as a duplicate (design call CR-48, PROPOSED). A replacement or void SHALL leave through a released batch (CL-05). The thread SHALL move `claimed` to `claimed` or `remitted` to `claimed` (TH-04).

#### Scenario: A replacement without a control number
- **WHEN** a biller tries to send a replacement for a claim whose payer control number was never recorded
- **THEN** the replacement is refused until the control number is recorded

### Requirement: CL-09 Rejections, denials and silence are told apart
openPractice SHALL record each status the payer or clearinghouse reports as a dated status event. A **rejection**, which means the claim failed before the payer decided anything, SHALL move the thread from `claimed` back to `charged` for correction, and SHALL have no accounting effect beyond reversing an acceptance already posted for that claim (CL-12; framework §4.2). A person MAY record, with a reason, that a rejected claim is dropped, meaning it will not be corrected and sent again. The drop SHALL raise work owned by the biller (AM-06) for the decision the amount it asked for then needs: a claim to another coverage; a conversion to self-pay, only where EG-05 allows one, or the self-pay billing GF-11 requires where the patient turns out to have had no coverage; or a write-off (PS-11). For a claim to a later coverage position, RM-07 reverses what remains of the transfer and the decision is made again (design call CR-100, PROPOSED). A **denial** is a decision, and SHALL arrive as a remittance (RM-01). When no acknowledgment or status arrives within the window the claim-pathway adapter declares, openPractice SHALL raise a work item, because silence is not success (design call CR-49, PROPOSED).

#### Scenario: A rejected claim
- **WHEN** the clearinghouse rejects a claim for a missing identifier
- **THEN** the thread moves back to `charged`, nothing posts, and the biller corrects and resubmits it in a later batch

#### Scenario: No answer
- **WHEN** a submitted claim has no acknowledgment after the adapter's declared window
- **THEN** a work item is raised for the biller

### Requirement: CL-10 Every claim carries a filing clock from a recorded payer window
Each claim SHALL carry a timely-filing clock: the date of service plus the payer's filing window. Each payer's window SHALL be a recorded parameter citing its source, stated in the unit the payer uses (calendar days, months or years) and counted from the date the payer counts from. The deadline SHALL be the end of its last day in the practice's time zone, and the clock's instant SHALL carry that zone's offset (design call CR-79, PROPOSED). A payer with no recorded window SHALL use the shortest window the practice has recorded for any payer, until its own is recorded (design call CR-50, PROPOSED). Where the practice has recorded no window for any payer, the clock SHALL use the conservative default window the practice's policy sets, with a shipped starting value of 60 calendar days (design call CR-79, PROPOSED). The clock SHALL be the visit thread's clock while it is the nearest money-costing deadline (TH-03).

#### Scenario: A payer with no recorded window
- **WHEN** a claim is built for a payer whose filing window was never recorded
- **THEN** it uses the shortest recorded window, and a work item asks for the payer's own

#### Scenario: A deadline in the practice's time zone
- **WHEN** a practice in a zone five hours behind UTC has a claim whose 90-day window ends on March 31
- **THEN** the clock's deadline is March 31 at 23:59:59 local time, recorded with the -05:00 offset, not midnight UTC

### Requirement: CL-11 Submitting a claim is a protected operation through the retention change's outbox
Every claim submission, replacement and void SHALL be authorized before it leaves under `add-reimbursement-retention-control` ("Protected reimbursement operations are authorized before execution"), and SHALL leave through its idempotent outbox ("External effects use an idempotent outbox"). Its outcome SHALL be recorded as a separate linked event of success, failure or indeterminate ("External reconciliation creates separate linked outcome events"), and an indeterminate outcome SHALL never be reported as success or sent again before it is reconciled ("Uncertain external effects become indeterminate and require reconciliation").

#### Scenario: The submission times out
- **WHEN** a batch submission times out with no answer
- **THEN** its outcome is indeterminate, the claims are not sent again, and reconciliation asks the adapter before anything else happens

### Requirement: CL-12 A claim moves the receivable from unbilled to billed when it is accepted
When the payer or clearinghouse accepts a claim, openPractice SHALL post `claim_accepted` for its amount, moving it from `unbilled_receivable` to `payer_receivable` (RT-02, AI-15). Sending alone SHALL post nothing (ruling 165: a claim counts as billed when it is accepted, a rejection after acceptance reverses the move, and the practice's accountant confirms), and the rejection of a claim never accepted SHALL post nothing (design call CR-15, PROPOSED). A claim's lines SHALL move to the payer receivable at most once while an acceptance of that claim stands (design call CR-15, PROPOSED, apart from the reversal that ruling 165 rules):
- a later rejection of the claim, including the payer's rejection after the clearinghouse accepted it, SHALL reverse that `claim_accepted` under SL-05, so that the corrected claim's acceptance moves the amount once again;
- the acceptance of a replacement version SHALL post only the difference between what it bills and what the claim has already moved, net of reversals, in either direction;
- a void, once accepted, SHALL reverse what the claim has moved, net of reversals.

A charge line's correction under CG-08 SHALL NOT reverse a `claim_accepted` entry; only these rules move it.

For a claim recorded through the manual path, these entries SHALL post when staff record its acceptance or rejection (CP-04). A claim to a later coverage position SHALL post nothing on acceptance or rejection, because the amount it asks for already moved to that coverage's payer receivable as `payer_share_transfer` (RM-07). None of these events SHALL create revenue (AI-12).

#### Scenario: A claim is accepted
- **WHEN** the clearinghouse accepts a 200.00 claim
- **THEN** 200.00 moves from `unbilled_receivable` to `payer_receivable`, and revenue does not change

#### Scenario: The payer rejects a claim the clearinghouse accepted
- **WHEN** the clearinghouse accepts a 200.00 claim, the payer then rejects it for an invalid member identifier, and the biller corrects it and resubmits it, and it is accepted again
- **THEN** the payer's rejection reverses the first `claim_accepted`, the second acceptance moves 200.00 once, and for that charge `payer_receivable` holds 200.00 and `unbilled_receivable` holds nothing

### Requirement: CL-13 A payment hold never stalls a claim
A payment hold SHALL NOT hold charge finalization, claim building, scrubbing, release, submission or correction (PH-08).

#### Scenario: A held patient's claim
- **WHEN** a patient on a payment hold has a ready claim
- **THEN** the claim is released with the next batch as usual
