## ADDED Requirements

### Requirement: GF-01 openPractice issues single-provider estimates for scheduled self-pay care
openPractice SHALL issue Good Faith Estimates for care the practice itself gives to a patient who will pay for it themselves, and SHALL issue, replace, deliver with proof, retain, watch the variance of, and handle disputes about them (ruling 121). An estimate SHALL cover only the practice's own items and services. Where the service also needs another provider or facility whose items an estimate would have to include, openPractice SHALL NOT issue a partial estimate; it SHALL raise a work item for a person to handle outside the system (design call CR-29, PROPOSED). Estimates for insured patients (quotes) are not part of this change.

#### Scenario: A visit that needs an outside lab's estimate
- **WHEN** a self-pay visit is booked for a service the practice gives together with an outside facility
- **THEN** no estimate is issued by openPractice, and a work item explains why

### Requirement: GF-02 An estimate is needed when no claim will be filed for the service
openPractice SHALL treat a booked service as needing a Good Faith Estimate when no claim will be filed for it, and a requested service only where the parameter set makes a patient's request a trigger (design call CR-81, PROPOSED): the patient has no coverage for the service, or the patient has coverage and has chosen, in a recorded election, not to use it for this service. Whether an estimate is required in a given case, and by when, SHALL follow the rules of the approved parameter set (GF-03), never rules written into the software. The determination SHALL be made at booking and again on any reschedule that changes the service, the date or the patient's coverage (AP-03).

#### Scenario: An insured patient elects to pay
- **WHEN** a patient with coverage records an election to pay for a cosmetic service themselves and books it
- **THEN** the service is treated as needing an estimate, under the parameter set's rules

### Requirement: GF-03 The legal parameters come from a counsel-verified, versioned set, and none is issued without it
The triggers, deadlines, required contents, dispute threshold, dispute window and retention period that govern Good Faith Estimates SHALL be held in a versioned parameter set, each value citing its source (design call CR-30, PROPOSED). A version SHALL take effect only after the practice manager, the role ruling 142 gives an authorized person where a rule names no other (`define-practice-readiness` SR-02), records counsel's verification of it, naming the counsel who gave it. That counsel's admission SHALL be checked against the state bar's public record and their name matched against the practice's staff and owners (ruling 149); a check that fails refuses the verification, as `define-practice-readiness` SR-04 states for every review or confirmation recorded as counsel's, and the verification SHALL never be self-approved (ruling 107, SR-04). This change asserts no legal rule: the values the brainstorm proposes (issuance within one or three business days by booking lead time, a 400.00 threshold, six years' retention) are inputs for counsel, not requirements. Until a verified version is in force, openPractice SHALL issue no estimate, and the practice issues estimates outside the system (ruling 172); every booking that would need one SHALL raise a work item that says so, and the visit itself SHALL NOT be held.

#### Scenario: No verified parameter set
- **WHEN** a self-pay visit is booked before any parameter set has counsel's recorded verification
- **THEN** openPractice issues no estimate, a work item says the practice issues it outside the system, and the booking stands

### Requirement: GF-04 The issuance deadline is the thread's clock
When an estimate is needed, its issuance deadline under the parameter set SHALL become a clock on the visit thread (TH-03), counted from the booking, or from the patient's request where the parameter set makes a request a trigger (GF-02). An estimate not issued as the deadline approaches SHALL appear as owned work (TH-12).

#### Scenario: A deadline approaches
- **WHEN** an estimate is needed and its issuance deadline is tomorrow
- **THEN** the thread's clock shows one day left and the work is owned by a named person

### Requirement: GF-05 An estimate is built from the catalog and the price list, and frozen when issued
An estimate's amounts SHALL come from the catalog items expected for the service and the price list in force on the scheduled date, less the self-pay discount (SC-03, SC-04). Its other contents SHALL be the elements the parameter set requires, drawn from openPractice's records: the patient's identity, the provider's name, NPI, TIN and location, any expected service and diagnosis codes, and the notices the parameter set names. Expected codes SHALL be entered by a named human for that estimate and SHALL never be derived from the catalog, an earlier visit or a template (CG-02). At issuance the estimate SHALL be frozen as issued (TH-14) and SHALL never be edited.

#### Scenario: An estimate is issued
- **WHEN** an estimate is issued for a self-pay visit with two expected services
- **THEN** it lists both services at the scheduled date's prices less the discount, with the required elements, and the issued document cannot be changed

### Requirement: GF-06 A changed estimate is replaced, never edited
When a change to the service, price, provider or date alters an issued estimate's contents, openPractice SHALL issue a new estimate that replaces the earlier one (design call CR-31, PROPOSED). Both SHALL be kept and linked. The variance watch and any dispute SHALL measure against the most recent estimate issued before the service was given.

#### Scenario: The provider changes
- **WHEN** a self-pay visit is moved to a different provider after its estimate was issued
- **THEN** a replacement estimate is issued naming the new provider, and the first is kept as replaced

### Requirement: GF-07 Delivery is proved, and only through permitted channels
An estimate SHALL count as issued only when its delivery is recorded: handed over at the desk with the patient's signature, or sent through a channel that produces a delivery record, or mailed with the mailing recorded. Every delivery SHALL honor the patient's confidential-communication instructions (PR-05), including every restriction PR-15 applies across the patient's party and same-person group, and SHALL never use an alternate channel recorded on another party of that group (PR-15). A delivery to a representative SHALL be made only where the representative's granted scope includes `billing_contact` under the proxy policy (PG-06). A patient notice through the gateway (`patient.notice`, PG-12) SHALL count as delivery only where the parameter set accepts that channel and the notice's acknowledgment (`notice.ack`) is recorded; otherwise it MAY say that an estimate is ready but SHALL NOT count (design call CR-81, PROPOSED). Where a restriction that crossed a same-person link bars the channel a delivery would use, and the patient's party has no permitted channel of its own, openPractice SHALL send nothing: it SHALL hold the delivery and raise work, owned by a roster entry, for a named human to choose how to reach the patient (PR-15). A restriction that crossed a link SHALL keep applying after the link ends until PR-15's end-of-link review decides it. A held delivery SHALL NOT count as issued; the estimate SHALL stay on GF-04's clock, and its work SHALL escalate before the deadline (design call CR-96, PROPOSED). An estimate with no recorded delivery SHALL appear as work and SHALL NOT count as issued.

#### Scenario: A delivery fails
- **WHEN** an emailed estimate bounces and nothing else is recorded
- **THEN** the estimate does not count as issued, and the work is raised before its deadline

### Requirement: GF-08 Estimates are retained under the retention controls
Every issued estimate, its delivery record and every replacement SHALL be retained under the retention obligations of `add-reimbursement-retention-control` ("Applicable policies create traceable obligation instances"). openPractice SHALL NOT dispose of an estimate except through that change's disposition gate ("Disposition requires a complete gate evaluation and approval").

#### Scenario: A request to delete an old estimate
- **WHEN** a user asks to delete an issued estimate
- **THEN** it is refused, and only the retention change's disposition gate can ever dispose of it

### Requirement: GF-09 The variance between billed and estimated is watched per estimate
When a visit with an issued estimate is charged, openPractice SHALL compare the billed charges with the most recent estimate issued before the service. When the difference reaches the parameter set's threshold, it SHALL raise a work item naming the estimate, the billed total and the difference, before any statement for the visit is issued.

#### Scenario: Billed charges exceed the estimate
- **WHEN** billed charges exceed the estimate by at least the parameter set's threshold
- **THEN** a work item is raised before any statement is issued for the visit

### Requirement: GF-10 A dispute is a case that marks the money disputed
When a patient disputes a bill against an estimate, openPractice SHALL mint a `gfe_dispute` case thread (TH-08), linked to the visit thread, with the clock the parameter set states. The visit thread SHALL be marked `disputed` for the disputed difference (TH-05): no statement for that difference SHALL be issued (PS-07), and the amount SHALL count as disputed for every payment hold (PH-06, DC-8). The outcome SHALL be recorded on the case, and any reduction it requires SHALL post as a price concession (RT-07).

#### Scenario: A patient disputes
- **WHEN** a patient disputes the difference between their bill and the estimate
- **THEN** a dispute case is minted, the visit's disputed difference leaves every statement, and no payment hold can count it

### Requirement: GF-11 Coverage that turns up late changes the bill, never the estimate
When a patient billed as self-pay is found to have had coverage for the service, the issued estimate SHALL stand as issued and its variance watch SHALL end; the self-pay discount SHALL be reversed with a dated reason (RT-06); money the patient paid SHALL be held as a credit and applied to the patient's share once the payer answers (PY-03); and the thread SHALL move forward from where it is to `charged` so a claim can be built (TH-04). When an insured patient is found to have had no coverage, openPractice SHALL apply the self-pay discount before any statement is issued, and no statement SHALL show the full charge first (design call CR-32, PROPOSED). Whether an estimate is owed in that case SHALL follow the parameter set.

#### Scenario: A self-pay patient finds a card
- **WHEN** a self-pay patient who paid 140.00 later shows coverage for the visit
- **THEN** the estimate stands, the discount is reversed, the 140.00 is held as a credit, and a claim can be built

#### Scenario: Coverage ended before the visit
- **WHEN** a payer denies a visit because coverage ended the month before
- **THEN** the self-pay discount is applied before the first statement, and the statement never shows the full charge

### Requirement: GF-12 Only a named human issues an estimate
Issuing or replacing an estimate tells a patient an amount, so it SHALL be done only by a named human (design call CR-33, PROPOSED). It SHALL be FD-5 tier 2, done at once with a notification, because a countdown would work against its deadline. An agent SHALL stay at `propose` for `gfe.issue` (ruling 132): it may draft an estimate for a person to issue.

#### Scenario: An agent drafts an estimate
- **WHEN** an agent prepares an estimate for a newly booked self-pay visit
- **THEN** the draft waits in the work inbox, and only a named human can issue it
