# Owner Ratification Record

Status: **PENDING — SPECKIT FEATURE CREATION IS NOT AUTHORIZED**

This record is the mandatory, durable owner gate for OpenSpec change `define-revenue-cycle-core`. Creating any Speckit feature before this record is completed exactly as required is prohibited.

Brett Heap made rulings 112 to 132 on 2026-11-15, and later that day answered decisions 136 to 150 and decisions 152 to 194; rulings 162 to 182 answer this change's 21 open questions for the owner. Rulings 112 to 132, the later rulings this change relies on, and the earlier rulings it relies on are recorded in `design.md` under "Owner rulings". Making those rulings is not ratification of this artifact. The change was drafted by an agent, and only the owner can ratify the exact revision identified below. Under rulings 109 and 110, nothing from this change is handed to build before then.

The 101 design calls in `design.md` under "Design calls for owner review" (CR-1 to CR-101) have not been ruled, except where rulings 162 to 182 and 193 rule a call or its main lines, as each call's row says; a detail beyond a ruling's words stays PROPOSED. Ratification records the owner's disposition of each.

## Required Validation Evidence

- OpenSpec version: _pending_
- Validation command: `openspec validate define-revenue-cycle-core --strict --no-interactive`
- Validation timestamp: _pending_
- Exact ratified artifact revision or checksums: _pending_
- Validation exit code: _pending_
- Validation output or durable evidence reference: _pending_

## Required Contract Evidence

- Command: `sha256sum -c contracts/SHA256SUMS`, run in this change directory: _pending_
- Digest of `contracts/openpractice-thread-1.0.schema.json` (expected `14e29232ff7899b689a3a20e0fe16d5f8e9c53650f42ccbaed4b42d65c67fec8`): _pending_

## Required Dependency Evidence

- Draft PR opensoft/openPractice-spec#7 (`change/follow-up-ruling-93-contract-gaps`, read at `e746f58`) merged, with revision: _pending_
- Draft PR opensoft/openPractice-spec#8 (`change/critique-2026-11-15-corrections`, read at `4bce39a` and re-checked at `050bdfd`, `f96ee1b`, `f3bed0a`, `93566f8`, `0439321`, `f87a6f0`, `664fee8`, `89b97e3`, `c90b9a6`, `5805856`, `ad6f4be`, `ec9962d`, `5c85231`, `a03c575` and `b4b382e` while still being written) merged, with revision: _pending_
- Draft PR opensoft/openPractice-spec#10 (`change/define-practice-readiness`, read at `e9748d7` and re-checked at `d4724e1`, `ac091cb`, `d215ee4`, `2823f9f`, `75e1484`, `d1e1de1` and `0f8e4a4`) merged, with revision: _pending_
- Every requirement and design-call ID this change cites from them exists at those revisions and still means what `design.md` "Dependencies" describes (EB-13, EB-15, PR-15, F-6, F-7, F-12, F-13, F-18, BAB-17, BAB-18, PH-01, PH-04, PH-06 and PH-09 as amended, DC-68, F-8 to F-10, AM-01, AM-02, AO-04, AO-07, WI-03, SR-02, SR-04, RD-11, I-1), and the ruling-88 retraction bullet of `define-mvp-practice-foundation` task 2.4 that PA-3 keeps: _pending_
- Rulings 139 to 142, 144, 145, 149, 150, 164, 165, 177, 178, 185, 192 and 193 are encoded in those merged texts as `design.md` "Owner rulings" relies on them, and this change cites the requirement IDs that carry them: _pending_

## Required Amendment Dispositions

Each amendment belongs to the owning change. Its disposition is recorded here; this change edits no other change.

- PA-1, the service-category field, the counter payer class, the labels `write_off_against_allowance`, `bank_debit_clearing_release` and `payer_responsibility_transfer`, the AI-20 bank-debit record and the AI-13 customer-detail postings in `openpractice-accounting-integration/1.0` (`define-mvp-practice-foundation`; ruling 125), accepted, changed or rejected, with the change and revision that carries it and the new schema digest. PR #8 applies it at `664fee8` (digest `5ef51760…`, previously `b15c4a19…`), an open draft in which the field rests on ruling 125 and the rest is PROPOSED as F-14 to F-19; it counts as adopted only once that change merges. Until then, RT-04 refuses every summary-journal batch carrying this change's revenue or deduction lines, or a line that needs a new label or the counter payer class: _pending_
- PA-2 (`define-actors-agents-and-work-inbox` WI-07), PA-3 (`define-mvp-practice-foundation` `tasks.md` 2.3 and 2.4), PA-4 (`define-customer-financial-data-boundaries`, offering routing), PA-5 (`define-practice-readiness` inventory rows), PA-6 (`add-reimbursement-retention-control` record families), PA-7 (`define-mvp-practice-foundation` AI-15, claim acceptance), PA-8 (`define-patient-gateway-and-escalation` `tasks.md` 2.3, the payment-hold gate), PA-9 (`define-actors-agents-and-work-inbox` AO-11 and D-15), PA-10 (`define-patient-gateway-and-escalation` PH-01, walk-ins) and PA-11 (`define-patient-gateway-and-escalation` PH-06 and PH-09, as PR #8's design call DC-68); PR #8 has applied PA-2, PA-3, PA-4 and PA-7 to PA-10 at `c90b9a6` and PA-11 since `014aa60`, and PA-5 and PA-6 stay proposed, each accepted, changed or rejected, with the change and revision that carries it: _pending_

## Required Owner Decision

- Owner identity: _pending_
- Ratification timestamp: _pending_
- Hand-off shape: _pending owner confirmation; must be exactly `ordered six-feature hand-off (ruling 112)` or `split into separate changes` (the owner ruled for one change with its own ordered multi-feature hand-off on 2026-11-15, as `design.md` ruling 112 records, and this field still requires his confirmation)_
- Design-call dispositions (CR-1 to CR-101, each accepted, changed or rejected, a ruled call or main line being recorded as ruled): _pending_. CR-97 (PS-09 leaving a charge under open same-person reconciliation out of the published balance) and PR #8's design call DC-68 (PA-11) stand or fall together: rejecting one should mean rejecting both.
- Tension dispositions (T-1 to T-21; T-14, T-18, T-20 and T-21 are settled by rulings 164, 165, 181 and 178): _pending_
- Tiers for the twelve recording actions of ruling 182: recorded in "Recording Actions To Rule On At Ratification" below
- Decision: _pending; must be exactly `APPROVED UNCONDITIONALLY`_

Required owner statement:

> I reviewed the exact OpenSpec artifact revision identified above and ratify `define-revenue-cycle-core` unconditionally, with the hand-off shape, design-call dispositions and tension dispositions recorded above and the tiers of the twelve recording actions of ruling 182 recorded below. Under the ordered six-feature hand-off, I authorize the Speckit features listed in `tasks.md` section 2, in that order, as a stated exception to the workflow protocol's one-feature hand-off, each created only when its own gate is met. If I chose to split the change, I authorize no feature from it and direct that it be split first. I understand that:
> - each feature's plan phase requires a meaningful Constitution Check against constitution v1.0.0 and a documented security review;
> - openPractice records money and never moves it; refunds leaving the practice, every write-off until the practice has set its material amount and write-offs at or above it after that (ruling 173), and transfers between guarantor accounts (ruling 174), stay with a named human;
> - openPractice sends no claim batch without a named signer's release, except by the filing-deadline escape, which never releases a claim held for a retracted fact, for documentation or for authorization, or one whose participation answer is ended, denied or unknown; a claim sent outside openPractice is recorded only by a named human, scrubbed, and marked and escalated if it was sent while held;
> - no Good Faith Estimate is issued until counsel's verification of its parameter set is recorded, and until then the practice issues estimates outside the system (ruling 172);
> - no estimator version, policy record or material amount is approved except by the practice's outside accountant (rulings 139 to 141), never self-approved, so accounting cannot go live until the roster holds that accountant;
> - no summary-journal batch carries this change's revenue until the foundation change carrying the service-category field merges (PA-1);
> - every amendment this change proposes takes effect only when the owning change adopts it;
> - runtime work is confined to openPractice unless separately governed elsewhere;
> - nothing is promoted to canonical specs before the features land, and this change cannot be archived until the implementing work and the assembly pin bump are merged and accepted.

## Recording Actions To Rule On At Ratification (ruling 182)

Ruling 182: until the owner rules on each of these twelve recording actions at ratification, a named human does each, with a notification; snoozing a stuck thread needs no notification; agents only propose. `tasks.md` 3.5 and `design.md` "Governance tiers" carry the same list.

1. Recording a no-show or a patient who left unseen (AP-04): _pending_
2. Recording a disposition at `seen` with nothing to bill (TH-04): _pending_
3. Undoing a thread's minting (TH-04, TH-07): _pending_
4. Recording a claim sent outside the system, and its acceptance (CP-04): _pending_
5. Recording that a rejected claim is dropped (CL-09): _pending_
6. Closing same-person reconciliation work (TH-06, CG-05): _pending_
7. Recording a participation record, which can release held claims (EG-01, EG-04): _pending_
8. Recording an authorization (EV-02): _pending_
9. Releasing an authorization hold at the filing threshold, with a recorded reason (EV-02; ruling 181): _pending_
10. Deciding whether a later payer is billed first (RM-07): _pending_
11. Recording that a signed refund was issued (PY-07): _pending_
12. Snoozing a stuck thread (TH-12): _pending_

## Amendments After Ratification

This change will need governed amendments after it is ratified: the claim-pathway, remittance, clearinghouse and payment-processor contracts (CR-24), and any correction a feature's planning finds. Each amendment SHALL be a revision of this change directory on its own pull request, SHALL pass `openspec validate define-revenue-cycle-core --strict --no-interactive`, SHALL record any new or changed canonical schema in `contracts/SHA256SUMS`, and SHALL carry its own completed amendment record below before the feature that needs it starts. An amendment record has the same required fields and gate semantics as this record, with the decision exactly `AMENDMENT APPROVED UNCONDITIONALLY`. An unratified amendment changes nothing that has been ratified.

- Amendment records: _none yet_

## Gate Semantics

- None of the following satisfies this gate: an agent, a delegate, an inferred preference, a conditional approval, a blank field, failed strict validation, a checksum mismatch, an unmerged dependency, a cited ID missing from a merged dependency, a missing hand-off-shape choice, an undispositioned design call, one of ruling 182's twelve recording actions left unruled, or ratification of a different artifact revision.
- Editing this template does not itself constitute ratification; the identified owner must provide the completed, durable record.
- Until every required field is complete and the decision is exactly `APPROVED UNCONDITIONALLY`, no Speckit feature may be created for this change.
- The proposal's controlled `Status` must remain `draft` until the owner completes this record. Only the owner may change it to `ratified`, in the same durable revision as this completed record.
