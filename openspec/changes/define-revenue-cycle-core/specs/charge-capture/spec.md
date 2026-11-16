## ADDED Requirements

### Requirement: CG-01 A charge line is a priced, payer-agnostic statement that a service was given
openPractice SHALL record each billable service as a charge line in the visit's one charge set (one charge set per visit thread). A charge line SHALL carry:
- its catalog item (SC-01), and the procedure code and any modifiers it bills under, each as a code system and a code only;
- the units and the date of service;
- the rendering provider and the billing entity, identified by TIN and NPI;
- the location, and the place of service: the one the EHR's final facts state for the encounter, or one a named human sets, and otherwise the one the location's record gives (design call CR-76, PROPOSED);
- the price, which is the catalog item's price on the date of service times the units (SC-03);
- pointers to the visit's ranked diagnosis references, held once on the charge set;
- its source: references to the EHR facts and their versions, or the manual-entry record (CG-02);
- its thread.

A charge line SHALL carry no payer, no allowed amount and no patient share; those belong to claims and remittances.

#### Scenario: A charge line is recorded
- **WHEN** an office visit and a lab draw are given to Pat Example on one day
- **THEN** the visit's charge set holds two charge lines, each with its item, code, units, date, providers, place of service, price and source, and neither names a payer

### Requirement: CG-02 Codes come from the EHR's final facts or from staff, never from openPractice
A charge line's procedure and diagnosis codes SHALL come from one of two sources (ruling 116):
- the EHR's final, coded clinical facts for the encounter, read under `openpractice-ehr-clinical-facts/1.0` (EB-13, EB-14), which publishes only confirmed diagnoses (ruling 87, EB-13);
- manual entry by authorized staff in the cases EB-17 allows, marked manual, with its provenance (ruling 21).

Modifiers and diagnosis pointers SHALL come from the same two sources: the EHR's coded facts, or a named human in the manual case. A code SHALL come from no other source (design call CR-76, PROPOSED). openPractice SHALL NOT infer, suggest, derive, prefill or default a code: not from text, not from the booked catalog item, not from an earlier visit, not from a template or favorite list, and not from an agent's choice. It SHALL NOT change a code the EHR supplied; a disagreement SHALL go back to the EHR as a question, outside this contract. In the manual case, only a named human SHALL enter or select a code, from the superbill or the documentation in front of them. A charge line's catalog item SHALL be found from its code, by an exact match on code system and code, and never its code from its item; a code that matches no active item SHALL become a work item. Units MAY start from the item's default, and the place of service SHALL come from CG-01's sources: the EHR's facts where they state the encounter's place, such as a telehealth, home or facility visit, or a named human, and otherwise the location's record; neither is a code under this requirement. An adapter's code edits SHALL only flag or hold a claim, never change a code (CP-02).

#### Scenario: openChart cannot yet supply facts
- **WHEN** a patient is linked to openChart and openChart reports no billable-facts support
- **THEN** staff enter the codes from the superbill, and each charge line is marked manual with that reason (EB-17)

#### Scenario: A booked item does not supply a code
- **WHEN** staff open a manual charge entry for a visit booked as an office visit
- **THEN** no code is filled in from the booking, an earlier visit or a template, and the staff member enters each code from the superbill

#### Scenario: A fact code with no catalog item
- **WHEN** final facts carry a procedure code that no active catalog item uses
- **THEN** no charge line is created for it, and a work item names the code

### Requirement: CG-03 A charge set starts as a draft, and a draft posts nothing
A charge set SHALL start as a draft. Staff MAY prepare a draft. An agent MAY prepare one at most at `act_with_notice` (ruling 132, TH-13), and only from final EHR facts, carrying exactly the codes, modifiers and diagnosis pointers those facts supply; an agent SHALL assign none of them, SHALL prepare no draft in the manual case, and SHALL prepare no draft from a fact that PR-15's same-person safeguard has sent to reconciliation (CG-05; CG-02, CR-76). A draft SHALL post no ledger entry, SHALL NOT appear on a claim or a statement, and MAY be changed freely until it is finalized.

#### Scenario: An agent drafts charges from facts
- **WHEN** an agent opted up to `act_with_notice` drafts a charge set from newly final facts
- **THEN** the draft exists, the biller is notified, and nothing posts until a person finalizes it

### Requirement: CG-04 A named human biller confirms the codes before a charge set is final
A charge set SHALL become final only when a named human who holds the biller role confirms its codes (ruling 116). The confirmation SHALL be a sign-off under BAB-17 that records the role used (`define-practice-readiness` SR-02), and SHALL NOT be opted in for agent signing: an agent stays at `propose` for `charge.finalize` (ruling 132). Where the biller's own party holds a role on the patient's record, the confirmation SHALL follow PR-15's rule for a staff member acting on their own record and its one-person alternative (design call F-7 of `define-mvp-practice-foundation`). Finalization SHALL move the thread from `seen` to `charged` (TH-04).

#### Scenario: An agent tries to finalize
- **WHEN** an agent tries to finalize a charge set it drafted
- **THEN** the finalization is refused, and the draft waits for a named human biller

#### Scenario: The biller confirms
- **WHEN** a biller reviews the draft against the final facts and confirms it
- **THEN** the charge set is final, the sign-off records the biller role, and the thread moves to `charged`

### Requirement: CG-05 Finalization is refused while any of its checks fails
Before a charge set becomes final, openPractice SHALL check, and SHALL refuse finalization while any check fails:
- the offering route and the patient's membership entitlement (BAB-16, SC-06). An unresolved route SHALL hold the thread with reason `offering_route_unresolved`, released at tier 3 as medical billing at the payer's filing threshold under BAB-16;
- the date of service is on or before any deactivation date of the patient's record (PR-11);
- every fact the charge set uses is final (EB-14), and none has been retracted (EB-15). A retraction SHALL hold the thread with reason `clinical_fact_retracted`;
- for a manual entry under identity review, the attestation EB-17 requires is recorded;
- no same-person reconciliation is open for the visit's service date. Where PR-15's rules for a same-person group send a fact to reconciliation, because another record of the group holds a charge for that service date, or hold a staying record's charge until a duplicate's charge is voided or credited, the thread SHALL be held with reason `same_person_reconciliation` until a named human closes that work (TH-06; design call CR-97, PROPOSED).

Charges already final when PR-15 records a same-person link are checked then. openPractice SHALL hold with reason `same_person_reconciliation` every thread of the group that holds a final charge not yet on a submitted claim for a service date on which another record of the group also holds a charge, and SHALL raise the reconciliation work for it; a charge already on a submitted claim SHALL raise that work without a hold, and any correction of it SHALL go through CL-08 (design call CR-97, PROPOSED). CL-04's scrub refuses those claims while the reconciliation is open, and no statement from either record shows a charge for that service date as owed until it is closed (PS-07).

These rules SHALL keep applying between each pair of records whose parties were in the group after the link ends, and their holds SHALL stay, until PR-15's end-of-link review releases that pair on a finding that the two parties are different people: the link's reason `mistaken`, which lets it release every pair in one decision, or a finding the review records. A release closes the open reconciliation work on those records, recording its reason. Without such a finding the rules stay for that pair until a link joins their parties again or one of the records is deactivated as a duplicate (PR-15; design call CR-97, PROPOSED).

#### Scenario: A retracted diagnosis
- **WHEN** the EHR retracts a diagnosis that an unfinalized charge set points to
- **THEN** finalization is refused, the thread is held with reason `clinical_fact_retracted`, and the biller is given the work

#### Scenario: A link is recorded after both charges are final
- **WHEN** two records of one person each hold a final charge for the same service date, neither yet on a submitted claim, and a person records that they are the same person
- **THEN** both threads are held with reason `same_person_reconciliation`, the scrub refuses both claims, the filing-deadline escape cannot send them, and the reconciliation work is raised

#### Scenario: A link is ended by mistake while two charges are held
- **WHEN** two threads are held with reason `same_person_reconciliation` and the link between their records is ended with reason `mistaken`
- **THEN** both threads stay held, a new fact on either record for that service date still goes to reconciliation, and the end-of-link review may release both holds in one decision on that finding, closing the reconciliation work with that reason, after which both claims can be scrubbed and released

#### Scenario: A correct link is ended in error while two charges are held
- **WHEN** two threads are held with reason `same_person_reconciliation` and the link between their records, which are one person's, is ended in error with a reason other than `mistaken`
- **THEN** the end-of-link review cannot release the holds without recording a finding that the parties are different people; the holds end only when the reconciliation work is decided, and the safeguard stays until a link joins the parties again or one record is deactivated as a duplicate

#### Scenario: A member's included service
- **WHEN** a member's visit is a service that the membership's approved terms include
- **THEN** no medical charge is finalized for it (BAB-16)

### Requirement: CG-06 A second identical line needs a reason
openPractice SHALL refuse a second charge line on the same thread with the same catalog item, date of service, rendering provider and modifiers, unless a person records a coded reason that the service was given more than once (design call CR-27, PROPOSED). Extra units of one service SHALL be recorded as units on one line, not as a second line.

#### Scenario: The same service entered twice
- **WHEN** staff enter the same office visit twice for one patient, provider and date
- **THEN** the second line is refused unless a coded reason is recorded

### Requirement: CG-07 A visit waiting on documentation is held, visible and on the filing clock
A visit that happened but whose documentation is not final SHALL stay at `seen` with the condition `held` and the reason `waiting_on_clinical_documentation` (EB-14, TH-06). Its clock SHALL be the date of service plus the payer's filing window, and it SHALL appear as owned work (TH-12). Where a manual entry is allowed under EB-17, the hold SHALL release when the manual entry is finalized. Its deadline escape SHALL be escalation, never release by itself (ruling 181): at the filing threshold (CL-06) the work SHALL escalate to the practice manager (design call CR-75, PROPOSED), and the hold SHALL never release into a charge or a claim by itself, because nothing may be billed from documentation that is not final (design call CR-75, PROPOSED).

#### Scenario: A note is unsigned for a week
- **WHEN** a visit's documentation is still not final seven days after the visit
- **THEN** the visit is shown as held, waiting on documentation, with the days left to the filing deadline, and its owner is named

### Requirement: CG-08 A final charge is corrected only by reversal and replacement
A final charge line SHALL never be edited (BAB-04). A correction SHALL reverse the line, which reverses every entry it posted (SL-05) except a `claim_accepted` entry, which only CL-12 moves, and SHALL add a replacement line where one is needed, both referencing the original. When a fact behind a final line is corrected or retracted by the EHR (EB-15), openPractice SHALL record the dated event, hold the thread with reason `clinical_fact_retracted` until every final line that uses the fact has been reversed or replaced, and raise work for the biller to do so (design call CR-28, PROPOSED). While that hold is in force, no claim SHALL be built or released from those lines (CL-04). When the line is already on a submitted claim, the correction SHALL be made through a replacement or void claim (CL-08), and the claim as submitted SHALL stay unchanged (EB-16). A visit converted between insured and self-pay before any claim for it is accepted, under EG-05, CL-09 or GF-11, SHALL be posted as such a reversal and replacement of each final line, with no replacement or void claim where its claim was rejected and dropped (CL-09): the replacement posts under the new route (CG-09), so the amount leaves `unbilled_receivable` or `patient_receivable` through the reversal of the entries that put it there. After a claim has been accepted, RM-07 and PS-11 apply instead (design call CR-44, PROPOSED).

#### Scenario: A wrong code on an unclaimed charge
- **WHEN** a biller finds a wrong procedure code on a final charge line that has not been claimed
- **THEN** the line and its entries are reversed, a replacement line is finalized, and both reference the original

#### Scenario: A diagnosis is retracted after the charge is final
- **WHEN** the EHR retracts a diagnosis behind a final charge line that is not yet on a claim
- **THEN** the thread is held with reason `clinical_fact_retracted`, no claim can be built or released from the line, and the hold releases only when the biller reverses or replaces it

#### Scenario: A corrected diagnosis after the claim was sent
- **WHEN** the EHR corrects a diagnosis on a visit whose claim was already submitted
- **THEN** the event is recorded, the submitted claim is unchanged, and a replacement claim is offered to the biller

### Requirement: CG-09 A final charge posts its service entries on the right dates
When a charge set becomes final, openPractice SHALL post, for each line:
- the service entry at the gross price, as `service_insured` when the visit is billed to a payer or `service_self_pay` when it is not (RT-02);
- the `self_pay_discount` entry where the self-pay discount applies (SC-04);
- the deduction from its estimate (RE-01).

Each entry SHALL carry the date of service. Its accounting date SHALL be the date of service when the destination's period is open, and otherwise the first open date with a late-charge flag (ruling 4, AI-11, BAB-15). The finalization date SHALL be recorded as the date an invoice would carry (AI-11).

#### Scenario: A charge finalized after the destination closed the month
- **WHEN** a charge for an October 28 visit is finalized after the destination closed October
- **THEN** its entries keep October 28 as the date of service, take the first open date as their accounting date, and carry the late-charge flag
