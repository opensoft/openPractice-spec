## ADDED Requirements

### Requirement: EV-01 Eligibility is recorded by hand as evidence on a coverage version
openPractice SHALL let authorized staff record an eligibility check by hand as evidence (ruling 127). Each record SHALL name the coverage version it checked (PR-07), the dates of service it covers, when it was checked, how (payer portal, telephone or the payer's card), the result (`active`, `inactive` or `unknown`), the plan's stated effective dates, any copay or deductible the payer stated as structured amounts, an optional reference to an attached document, and who recorded it (design call CR-42, PROPOSED). Recording a check SHALL be FD-5 tier 1 (design call CR-85, PROPOSED).

#### Scenario: Staff check a payer portal
- **WHEN** a staff member checks a patient's Medicare coverage on the payer's portal before a visit
- **THEN** they record the coverage version, the visit date, the portal as source, the result `active` and themselves as recorder

### Requirement: EV-02 Authorizations are recorded with units and expiry, and finalized charges use them up
openPractice SHALL let staff record a prior authorization as evidence: the payer and coverage version, the catalog items it covers, the authorization number, the approved units, and the dates it is valid (ruling 127). It SHALL be a record only, with no request workflow (framework §9.1). Each finalized charge line for a covered item SHALL use up its units. A charge line reversed under CG-08 SHALL release, in the same act, the units it used, and its replacement SHALL use units again under this requirement, so that a correction never counts a service twice and never holds a replacement for units its own original used; the release SHALL be recorded beside the use it undoes, never by editing it (EV-06) (design call CR-102, PROPOSED). A charge for a service that needs an authorization with no units left, or outside its dates, SHALL hold its thread with reason `waiting_on_authorization` (TH-06), and SHALL NOT hold the patient. The authorization's expiry SHALL be a clock on the thread (TH-03). The hold's deadline escape SHALL be escalation, never release by itself (ruling 181): at the filing threshold (CL-06) the work SHALL escalate to a named human, who MAY release the claim into the batch with a recorded reason, for example that the payer confirmed no authorization was needed or will accept a retroactive one (ruling 181; details CR-74, PROPOSED). Because the release comes at the filing threshold, the claim is then ready, and CL-06's escape sends it after its cancel window unless a named signer releases the batch first or someone cancels it.

#### Scenario: Units run out
- **WHEN** an authorization for six visits has been used six times and a seventh visit is charged
- **THEN** the seventh visit's claim is held with reason `waiting_on_authorization`, and the visit itself was not blocked

#### Scenario: The filing threshold arrives with no authorization
- **WHEN** a claim held for authorization reaches the filing threshold
- **THEN** it is not released by itself; the work escalates to a named human, who may release it with a recorded reason or leave it held

#### Scenario: A corrected charge used the last authorized unit
- **WHEN** an authorization approved 10 units, a final charge line used the tenth, and the biller corrects that line's diagnosis pointers under CG-08
- **THEN** the reversal releases the unit and the replacement uses it again, so the authorization still shows 10 used, and the replacement is not held as `waiting_on_authorization`

### Requirement: EV-03 Electronic eligibility arrives with the clearinghouse
Electronic eligibility checks SHALL come with the live clearinghouse feature (feature 5), and only where the claim-pathway adapter declares that capability (CP-01) (ruling 127). An electronic response SHALL be kept exactly as received, together with its normalized result, as evidence of the same kind as EV-01.

#### Scenario: The adapter does not offer eligibility
- **WHEN** the configured adapter does not declare an eligibility capability
- **THEN** openPractice offers no electronic check, and staff record checks by hand

### Requirement: EV-04 Readiness holds the claim, never the patient
openPractice SHALL compute a visit's readiness as what a claim for the visit will need, less what is known to be true now: coverage, eligibility evidence, authorization, participation (EG-02) and final documentation. Readiness SHALL have two severities only: `blocks_billing` and `informational`. It SHALL never block a booking, an arrival or a visit. A visit thread SHALL move to `ready` when readiness finds nothing missing, and back to `booked` when something becomes missing (TH-04). A missing item that blocks billing SHALL hold the claim later, at `charged`.

#### Scenario: Coverage ends before the visit
- **WHEN** a booked patient's eligibility evidence shows the plan ended last week
- **THEN** the thread drops from `ready` to `booked`, the reason is shown to staff, and the patient can still arrive and be seen

### Requirement: EV-05 A coverage change re-checks what depended on the old coverage
When the registry emits a coverage-changed event (PR-07), openPractice SHALL re-evaluate, for every affected date: the readiness of open visits (EV-04), the need for a Good Faith Estimate (GF-02), the payer and payer class of unclaimed charges, and the estimate of open charges, which changes only by a true-up (RE-05). Eligibility evidence for a changed coverage version SHALL stay attached to the version it checked and SHALL NOT be moved to the new one. A claim already submitted for an affected date SHALL NOT be changed, and SHALL raise work for the biller to decide whether a replacement, a void or a claim to another payer is needed (CL-08), for example after a retroactive termination or a change in which payer is primary.

#### Scenario: A secondary plan is added
- **WHEN** staff add a secondary coverage effective last month
- **THEN** readiness and the payer order of affected unclaimed visits are re-evaluated, and nothing already claimed is changed

#### Scenario: Coverage is terminated retroactively
- **WHEN** a payer reports that a coverage ended two months ago, and claims for visits since then were already submitted to it
- **THEN** those claims are unchanged, and a work item for each asks the biller to decide on a void, a replacement or another payer

### Requirement: EV-06 Evidence is never edited
An eligibility or authorization record SHALL never be edited once recorded. A correction SHALL be a new record that names the one it corrects. Every coverage version that evidence, an estimate or a claim used SHALL stay retrievable unchanged (PR-07).

#### Scenario: A mistyped authorization number
- **WHEN** staff find a mistyped authorization number
- **THEN** they record a corrected authorization that names the first, and the first stays in the history
