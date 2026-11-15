# Owner Ratification Record

Status: **PENDING — SPECKIT FEATURE CREATION IS NOT AUTHORIZED**

This record is the mandatory, durable owner gate for OpenSpec change `define-practice-readiness`. Creating any Speckit feature before this record is completed exactly as required is prohibited.

Brett Heap made ruling 107 on 2026-11-15, and it is recorded in `design.md` with the other rulings that govern this change. Making that ruling is not ratification of this artifact. The change was drafted by an agent, and only the owner can ratify the exact revision identified below.

The 19 design calls in `design.md` under "Design calls for owner review" have not been ruled. Ratification records the owner's disposition of each.

## Required Validation Evidence

- OpenSpec version: _pending_
- Validation command: `openspec validate define-practice-readiness --strict --no-interactive`
- Validation timestamp: _pending_
- Exact ratified artifact revision or checksums: _pending_
- Validation exit code: _pending_
- Validation output or durable evidence reference: _pending_

## Required Dependency Evidence

- Draft PR opensoft/openPractice-spec#7 (`change/follow-up-ruling-93-contract-gaps`) merged, with revision: _pending_
- The corrections change stacked on it, draft PR opensoft/openPractice-spec#8 (`change/critique-2026-11-15-corrections`), merged, with revision: _pending_
- Every requirement ID this change cites exists at those revisions (cross-reference check): _pending_
- Rows RI-46, RI-47, RI-49 and RI-50 traced to AH-01, AH-02, AH-06, AM-04 and MA-04 as merged from the corrections change, and the ER-12 lapse on RI-17, RI-18 and RI-46: _pending_

## Required Amendment Dispositions

Each amendment belongs to the owning change. Its disposition is recorded here; this change edits no other change.

- RA-1 (AO-10), RA-2 (ER-09, a record only), RA-3 (AO-03), RA-4 (AH-05 exception 1, condition 5) and RA-6 (PR-15, the practice's ownership), each accepted, changed or rejected, with the change and revision that carries it; RA-5a to RA-5d (PG-05, PG-06, PH-04, BAB-17) only if the corrections change's design call F-7 is rejected: _pending_
- CA-1 to CA-4 and CA-9, each accepted, changed or rejected, with the change and revision that carries it; CA-5 to CA-8 only if their owners come to record counsel's part: _pending_

## Required Owner Decision

- Owner identity: _pending_
- Ratification timestamp: _pending_
- Handoff shape: _pending owner confirmation; design call RD-15 proposes exactly one Speckit feature, with SR-02 and SR-04 built into the shared sign-off record before any sign-off is recorded in production_
- Design-call dispositions (RD-1 to RD-19, each accepted, changed or rejected): _pending_
- Tension dispositions (T-1 to T-10): _pending_
- Answers to Open Questions 14 (an employed accountant) and 15 (recording ownership), or a record that each stays open and SR-04 fails closed meanwhile: _pending_
- Decision: _pending; must be exactly `APPROVED UNCONDITIONALLY`_

Required owner statement:

> I reviewed the exact OpenSpec artifact revision identified above and ratify `define-practice-readiness` unconditionally, with the handoff shape, design-call dispositions and tension dispositions recorded above. I authorize the Speckit handoff that shape describes. I understand that:
> - the readiness checklist reads and displays only, and never satisfies, replaces or relaxes a gate, each of which keeps its own fail-closed enforcement;
> - the checklist follows the requirements, and a ruling that no requirement encodes has no row;
> - a starter template is inert until the authority its requirement names signs a policy made from it, and nothing is adopted by default or in bulk;
> - one person may hold several roles, every sign-off records the role under which it was given, and a rule that needs two people fails closed until its owning change names a one-person alternative;
> - an accountant's sign-off, and a counsel sign-off that a requirement records, can never be self-approved, and this change adds no counsel sign-off where a requirement only describes counsel's part;
> - a use of a one-person alternative whose subject is a patient-side record appears on the checklist only as a count;
> - every amendment this change proposes takes effect only when the owning change adopts it;
> - this change cannot be archived until the implementing work and the assembly pin bump are merged and accepted.

## Gate Semantics

- None of the following satisfies this gate: an agent, a delegate, an inferred preference, a conditional approval, a blank field, failed strict validation, an unmerged dependency, a missing handoff-shape choice, an undispositioned design call, or ratification of a different artifact revision.
- Editing this template does not itself constitute ratification; the identified owner must provide the completed, durable record.
- Until every required field is complete and the decision is exactly `APPROVED UNCONDITIONALLY`, no Speckit feature may be created for this change.
- The proposal's controlled `Status` must remain `draft` until the owner completes this record. Only the owner may change it to `ratified`, in the same durable revision as this completed record.
