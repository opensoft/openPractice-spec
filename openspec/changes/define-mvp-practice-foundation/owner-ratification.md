# Owner Ratification Record

Status: **PENDING — SPECKIT FEATURE CREATION IS NOT AUTHORIZED**

This record is the mandatory, durable owner gate for OpenSpec change `define-mvp-practice-foundation`. Creating any Speckit feature before this record is completed exactly as required is prohibited.

The decisions this change encodes were made by Brett Heap on 2026-11-14. Making those decisions is not ratification of this artifact. The change was drafted by an agent, and only the owner can ratify the exact revision identified below.

The ⚑ design calls were ruled by Brett Heap on 2026-11-14 and are recorded in `design.md` under "Owner rulings (2026-11-14)". Rulings 87 to 89 and 93, accepted on 2026-11-15, are recorded there too, and govern the follow-up listed under "Follow-up of 2026-11-15". Rulings 103 and 107, accepted later on 2026-11-15, govern the corrections listed under "Corrections from the 2026-11-15 spec critique". Ratification still requires the owner to complete this record.

## Required Validation Evidence

- OpenSpec version: _pending_
- Validation command: `openspec validate define-mvp-practice-foundation --strict`
- Validation timestamp: _pending_
- Exact ratified artifact revision or checksums: _pending_
- Validation exit code: _pending_
- Validation output or durable evidence reference: _pending_

## Required Companion-Change Consistency Evidence

- Companion change: `opensoft/openChart-spec`, change `define-openpractice-boundary`
- Companion artifact revision compared: _pending_
- Canonical schema digests match (`contracts/SHA256SUMS` in this change against the digests cited in openChart's PL-04): _pending_
- Contract identifiers match (`openpractice-ehr-patient-link/1.0`, `openpractice-ehr-clinical-facts/1.0`): _pending_
- Namespaces match (`openpractice` in openChart, `openchart` in openPractice): _pending_
- Issuer requirements match (openPractice EB-02, openChart PL-03): _pending_

## Required Owner Decision

- Owner identity: _pending_
- Ratification timestamp: _pending_
- Handoff shape: _pending owner confirmation; must be exactly `A — ordered multi-feature handoff` or `B — split into per-capability changes` (the owner ruled `A` on 2026-11-14 as `design.md` ruling 1 records, and this field still requires his confirmation)_
- Proposed follow-up calls F-1 to F-4 (retraction reason code) accepted, changed or rejected; F-5 noted: _pending_
- Proposed calls F-6 to F-10 from the 2026-11-15 spec-critique corrections (PR-15's one-party wording and the own-record rule under ruling 107; the USD-only limit, rounding and one-currency rules under ruling 103) accepted, changed or rejected: _pending_
- Decision: _pending; must be exactly `APPROVED UNCONDITIONALLY`_

Required owner statement:

> I reviewed the exact OpenSpec artifact revision identified above and ratify `define-mvp-practice-foundation` unconditionally, with the handoff shape recorded above. Under shape A, I authorize the ordered Speckit features listed in `tasks.md` section 2 as a stated exception to the workflow protocol's one-feature handoff, each created only when its own gate is met. Under shape B, I authorize no feature from this change and direct that it be split first. I understand that each feature's plan phase requires a meaningful Constitution Check against constitution v1.0.0 and a documented security review; that customer-detail accounting postings stay disabled for every destination until a verified BAA, or for a practice-operated destination a verified operator attestation (ruling 6), is recorded for it; that runtime work is confined to openPractice unless separately governed elsewhere; and that this change cannot be archived until the implementing work and the assembly pin bump are merged and accepted.

## Gate Semantics

- An agent, a delegate, an inferred preference, a conditional approval, a blank field, failed strict validation, a companion-change mismatch, a missing handoff-shape choice, or ratification of a different artifact revision does not satisfy this gate.
- Editing this template does not itself constitute ratification; the identified owner must provide the completed, durable record.
- Until every required field is complete and the decision is exactly `APPROVED UNCONDITIONALLY`, no Speckit feature may be created for this change.
- The proposal's controlled `Status` must remain `draft` until the owner completes this record. Only the owner may change it to `ratified`, in the same durable revision as this completed record.
