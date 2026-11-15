# Owner Ratification Record

Status: **PENDING — SPECKIT FEATURE CREATION IS NOT AUTHORIZED**

This record is the mandatory, durable owner gate for OpenSpec change `define-customer-financial-data-boundaries`. Creating any Speckit feature before this record is completed exactly as required is prohibited.

Owner rulings 25 and 26 (Brett Heap, 2026-11-14) are reconciled into this change and logged in `review-and-validation.md`. Making those rulings is not ratification of this artifact. Only the owner can ratify the exact revision identified below.

## Required Validation Evidence

- OpenSpec version: _pending_
- Validation command: `openspec validate define-customer-financial-data-boundaries --strict --no-interactive`
- Validation timestamp: _pending_
- Exact ratified artifact revision or checksums: _pending_
- Validation exit code: _pending_
- Validation output or durable evidence reference: _pending_

## Required Companion-Change Consistency Evidence

- Companion change: `opensoft/openPractice-spec`, change `define-mvp-practice-foundation`
- Companion artifact revision compared: _pending_
- Posting mechanics are referenced, not redefined (ruling 26): _pending_
- Posting-mode names match (`summary-journal`, `customer-detail`): _pending_
- The recipient-profile shape is the companion's AI-23 definition: _pending_
- The BAA-gate record is the companion's AI-14 accounting destination register: _pending_
- openPractice is the single poster of each deposit it explains, and the offering-route check precedes charge finalization (companion BAB-14, BAB-16): _pending_

## Required Owner Decision

- Owner identity: _pending_
- Ratification timestamp: _pending_
- Handoff shape: _pending owner confirmation; `tasks.md` §2.1 proposes exactly one Speckit feature covering the three capability deltas, and the owner either confirms it or directs a split_
- Decision: _pending; must be exactly `APPROVED UNCONDITIONALLY`_

Required owner statement:

> I reviewed the exact OpenSpec artifact revision identified above and ratify `define-customer-financial-data-boundaries` unconditionally, with the handoff shape recorded above. I authorize the Speckit handoff that shape describes, created only after strict validation passes on this revision. I understand that this change references the posting mechanics, the recipient-profile shape and the BAA-gate record of `define-mvp-practice-foundation` and does not redefine them; that no PHI-bearing customer record reaches an accounting destination without that destination's verified BAA, or for a practice-operated destination a verified operator attestation; and that this change cannot be archived until the implementing work and the assembly pin bump are merged and accepted.

## Gate Semantics

- An agent, a delegate, an inferred preference, a conditional approval, a blank field, failed strict validation, a companion-change mismatch, a missing handoff-shape choice, or ratification of a different artifact revision does not satisfy this gate.
- Editing this template does not itself constitute ratification; the identified owner must provide the completed, durable record.
- Until every required field is complete and the decision is exactly `APPROVED UNCONDITIONALLY`, no Speckit feature may be created for this change.
- The proposal's `Status` must remain `draft` until the owner completes this record. Only the owner may change it to `ratified`, in the same durable revision as this completed record.
