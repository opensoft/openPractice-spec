# Owner Ratification Record

Status: **PENDING — SPECKIT FEATURE CREATION IS NOT AUTHORIZED**

This record is the mandatory, durable owner gate for OpenSpec change `define-revenue-cycle-core`. Creating any Speckit feature before this record is completed exactly as required is prohibited.

Brett Heap made rulings 112 to 132 on 2026-11-15, and they are recorded in `design.md` under "Owner rulings", with the earlier rulings this change relies on. Making those rulings is not ratification of this artifact. The change was drafted by an agent, and only the owner can ratify the exact revision identified below. Under rulings 109 and 110, nothing from this change is handed to build before then.

The 99 design calls in `design.md` under "Design calls for owner review" (CR-1 to CR-99) have not been ruled. Ratification records the owner's disposition of each.

## Required Validation Evidence

- OpenSpec version: _pending_
- Validation command: `openspec validate define-revenue-cycle-core --strict --no-interactive`
- Validation timestamp: _pending_
- Exact ratified artifact revision or checksums: _pending_
- Validation exit code: _pending_
- Validation output or durable evidence reference: _pending_

## Required Contract Evidence

- Command: `sha256sum -c contracts/SHA256SUMS`, run in this change directory: _pending_
- Digest of `contracts/openpractice-thread-1.0.schema.json` (expected `c861dd82841c1c3f38826868684e9b0ce927fced5c03b623b40aecfba4f2185b`): _pending_

## Required Dependency Evidence

- Draft PR opensoft/openPractice-spec#7 (`change/follow-up-ruling-93-contract-gaps`, read at `e746f58`) merged, with revision: _pending_
- Draft PR opensoft/openPractice-spec#8 (`change/critique-2026-11-15-corrections`, read at `4bce39a` and re-checked at `050bdfd`, `f96ee1b` and `f3bed0a` while still being written) merged, with revision: _pending_
- Draft PR opensoft/openPractice-spec#10 (`change/define-practice-readiness`, read at `e9748d7` and re-checked at `d4724e1`) merged, with revision: _pending_
- Every requirement and design-call ID this change cites from them exists at those revisions and still means what `design.md` "Dependencies" describes (EB-13, EB-15, PR-15, F-7, BAB-18, F-8 to F-10, AM-01, AO-04, AO-07, SR-02, SR-04, RD-11, I-1, RI-51): _pending_

## Required Amendment Dispositions

Each amendment belongs to the owning change. Its disposition is recorded here; this change edits no other change.

- PA-1, the service-category field, the labels `write_off_against_allowance`, `bank_debit_clearing_release` and `payer_responsibility_transfer`, the AI-20 bank-debit record and the AI-13 customer-detail postings in `openpractice-accounting-integration/1.0` (`define-mvp-practice-foundation`; ruling 125), accepted, changed or rejected, with the change and revision that carries it and the new schema digest. Until it is adopted, RT-04 refuses every summary-journal batch carrying this change's revenue or deduction lines, or a line with either new label: _pending_
- PA-2 (`define-actors-agents-and-work-inbox` WI-07), PA-3 (`define-mvp-practice-foundation` `tasks.md` 2.3 and 2.4), PA-4 (`define-customer-financial-data-boundaries`, offering routing), PA-5 (`define-practice-readiness` inventory rows), PA-6 (`add-reimbursement-retention-control` record families), PA-7 (`define-mvp-practice-foundation` AI-15, claim acceptance), PA-8 (`define-patient-gateway-and-escalation` `tasks.md` 2.3, the payment-hold gate), PA-9 (`define-actors-agents-and-work-inbox` AO-11 and D-15) and PA-10 (`define-patient-gateway-and-escalation` PH-01, walk-ins), each accepted, changed or rejected, with the change and revision that carries it: _pending_

## Required Owner Decision

- Owner identity: _pending_
- Ratification timestamp: _pending_
- Hand-off shape: _pending owner confirmation; must be exactly `ordered six-feature hand-off (ruling 112)` or `split into separate changes` (the owner ruled for one change with its own ordered multi-feature hand-off on 2026-11-15, as `design.md` ruling 112 records, and this field still requires his confirmation)_
- Design-call dispositions (CR-1 to CR-99, each accepted, changed or rejected): _pending_
- Tension dispositions (T-1 to T-21): _pending_
- Tiers for the actions `tasks.md` 3.5 lists, or a record that CR-88's defaults stand until ruled: _pending_
- Answer to Open Question 15 (who settles the accountant's place on the roster, `define-practice-readiness` RI-51), or a record that it stays open and every accountant approval this change needs fails closed meanwhile (RE-02, RE-09, PS-11): _pending_
- Decision: _pending; must be exactly `APPROVED UNCONDITIONALLY`_

Required owner statement:

> I reviewed the exact OpenSpec artifact revision identified above and ratify `define-revenue-cycle-core` unconditionally, with the hand-off shape, design-call dispositions and tension dispositions recorded above. Under the ordered six-feature hand-off, I authorize the Speckit features listed in `tasks.md` section 2, in that order, as a stated exception to the workflow protocol's one-feature hand-off, each created only when its own gate is met. If I chose to split the change, I authorize no feature from it and direct that it be split first. I understand that:
> - each feature's plan phase requires a meaningful Constitution Check against constitution v1.0.0 and a documented security review;
> - openPractice records money and never moves it; refunds leaving the practice, and write-offs at or above the material amount, stay with a named human;
> - openPractice sends no claim batch without a named signer's release, except by the filing-deadline escape, which never releases a claim held for a retracted fact, for documentation or for authorization, or one whose participation answer is ended, denied or unknown; a claim sent outside openPractice is recorded only by a named human, scrubbed, and marked and escalated if it was sent while held;
> - no Good Faith Estimate is issued until counsel's verification of its parameter set is recorded;
> - no estimator version, policy record or material amount is approved except by the practice's accountant, never self-approved, so accounting cannot go live until the roster can hold that accountant;
> - no summary-journal batch carries this change's revenue until the accounting contract carries a service-category field (PA-1);
> - every amendment this change proposes takes effect only when the owning change adopts it;
> - runtime work is confined to openPractice unless separately governed elsewhere;
> - nothing is promoted to canonical specs before the features land, and this change cannot be archived until the implementing work and the assembly pin bump are merged and accepted.

## Amendments After Ratification

This change will need governed amendments after it is ratified: the claim-pathway, remittance, clearinghouse and payment-processor contracts (CR-24), and any correction a feature's planning finds. Each amendment SHALL be a revision of this change directory on its own pull request, SHALL pass `openspec validate define-revenue-cycle-core --strict --no-interactive`, SHALL record any new or changed canonical schema in `contracts/SHA256SUMS`, and SHALL carry its own completed amendment record below before the feature that needs it starts. An amendment record has the same required fields and gate semantics as this record, with the decision exactly `AMENDMENT APPROVED UNCONDITIONALLY`. An unratified amendment changes nothing that has been ratified.

- Amendment records: _none yet_

## Gate Semantics

- None of the following satisfies this gate: an agent, a delegate, an inferred preference, a conditional approval, a blank field, failed strict validation, a checksum mismatch, an unmerged dependency, a cited ID missing from a merged dependency, a missing hand-off-shape choice, an undispositioned design call, or ratification of a different artifact revision.
- Editing this template does not itself constitute ratification; the identified owner must provide the completed, durable record.
- Until every required field is complete and the decision is exactly `APPROVED UNCONDITIONALLY`, no Speckit feature may be created for this change.
- The proposal's controlled `Status` must remain `draft` until the owner completes this record. Only the owner may change it to `ratified`, in the same durable revision as this completed record.
