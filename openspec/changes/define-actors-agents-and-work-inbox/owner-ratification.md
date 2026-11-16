# Owner Ratification Record

Status: **PENDING — SPECKIT FEATURE CREATION IS NOT AUTHORIZED**

This record is the mandatory, durable owner gate for OpenSpec change `define-actors-agents-and-work-inbox`. Creating any Speckit feature before this record is completed exactly as required is prohibited.

Brett Heap made rulings 34 to 54 on 2026-11-14, and on 2026-11-15 accepted rulings 93 and 97, which govern this change's follow-up (`design.md`, "Follow-up of 2026-11-15"), and rulings 99, 100, 102 and 104, which govern its corrections (`design.md`, "Corrections from the 2026-11-15 spec critique"). All are recorded in `design.md`. Making those rulings is not ratification of this artifact. The change was drafted by an agent, and only the owner can ratify the exact revision identified below.

## Required Validation Evidence

- OpenSpec version: _pending_
- Validation command: `openspec validate define-actors-agents-and-work-inbox --strict --no-interactive`
- Validation timestamp: _pending_
- Exact ratified artifact revision or checksums: _pending_
- Validation exit code: _pending_
- Validation output or durable evidence reference: _pending_

## Required Companion-Change Consistency Evidence

- Base change: `define-mvp-practice-foundation`. Its revision compared: _pending_
- Cited requirement IDs exist at that revision (PR, EB, AI, BAB, including BAB-17 and AI-23): _pending_
- Sibling change `define-patient-gateway-and-escalation` cites this change's IDs correctly: _pending_
- Canonical schema digest matches `contracts/SHA256SUMS`: _pending_
- Required companion changes are recorded with their status (rulings 47 to 49): `define-care-organization-hermes-openpractice-operations` in `MedxSoft/medXfactory`, `define-clinical-message-inbox` in `opensoft/openChart-spec`, and `define-practice-gateway-integration` in `opensoft/HealthLinc`: _pending_

## Required Owner Decision

- Owner identity: _pending_
- Ratification timestamp: _pending_
- Handoff shape: _pending owner confirmation. `tasks.md` §2 proposes folding the actor model and inbox into the registry feature or the feature right after it, followed by AI data handling, the agent contract and VA safeguards._
- Design calls D-1 to D-42 accepted or amended (D-18 to D-29, from the 2026-11-15 follow-up and its review, D-30 to D-32, under rulings 104 and 102, D-33, made for the sibling change's DC-60, D-34, under ruling 133, D-35, under ruling 138, D-36 to D-40, under rulings 136, 141, 146, 144 and 148, D-41, conforming to `define-revenue-cycle-core`, a draft not yet on `main`, and D-42, what offboarding keeps readable, are PROPOSED; ruling 99 confirms the operation D-18 describes; rulings 184, 191 and 205 rule D-36's main lines, ruling 198 rules the main lines of D-33's bar and first-device rule, ruling 197 rules D-37's bank-debit confirmation, and ruling 192 rules D-39's, and only their details stay PROPOSED): _pending_
- Corrections from the 2026-11-15 spec critique (SC-1, SC-2's AM-01 sentence, SC-4 under ruling 104, ruling 102's go-live condition, SC-7's schema hygiene) accepted or amended: _pending_
- Open Questions 11 (messages for the operator's deletion request and receipt) and 15 (two channels in the wrong hands at a last-resort recovery) dispositioned; Open Questions 10, 12, 13 and 14 are closed by rulings 133, 144, 141 and 205: _pending_
- Tensions T-1 to T-7 dispositioned: _pending_
- Decision: _pending; must be exactly `APPROVED UNCONDITIONALLY`_

Required owner statement:

> I reviewed the exact OpenSpec artifact revision identified above and ratify `define-actors-agents-and-work-inbox` unconditionally, with the handoff shape and design-call dispositions recorded above. I understand that:
> - every agent action type starts at `propose`;
> - no agent acts with less friction than the action's FD-5 tier;
> - tier-4 actions stay human-only, and D8 approvals stay accountant-only;
> - no agent receives a grant at any rung, and no PHI reaches an agent layer, without verified BAAs with the agent operator and each model provider, each model provider's verified zero-data-retention arrangement and, for any path that uses a model provider, counsel's recorded confirmation of ruling 97's answer, and a lapse suspends all agent PHI access;
> - grants, rungs, limits and the pause are changed only by named humans, and an agent reads only its work item's patient;
> - an agent answers a patient without a human only through AH-05 exception 1's read-only answers, and only while the patient's consent is recorded (AH-06);
> - the work-item operations of AO-13 never rise above `propose`;
> - no remote VA reaches PHI without the verified agreement this change requires;
> - this change cannot be archived until the implementing work and the assembly pin bump are merged and accepted.

## Gate Semantics

- None of the following satisfies this gate: an agent, a delegate, an inferred preference, a conditional approval, a blank field, failed strict validation, a base-change mismatch, a missing handoff-shape choice, or ratification of a different artifact revision.
- Editing this template does not itself constitute ratification; the identified owner must provide the completed, durable record.
- Until every required field is complete and the decision is exactly `APPROVED UNCONDITIONALLY`, no Speckit feature may be created for this change.
- The proposal's `Status` must remain `draft` until the owner completes this record. Only the owner may change it to `ratified`, in the same durable revision as this completed record.
