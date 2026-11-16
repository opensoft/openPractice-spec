# Owner Ratification Record

Status: **PENDING — SPECKIT FEATURE CREATION IS NOT AUTHORIZED**

This record is the mandatory, durable owner gate for OpenSpec change `define-mvp-practice-foundation`. Creating any Speckit feature before this record is completed exactly as required is prohibited.

The decisions this change encodes were made by Brett Heap on 2026-11-14. Making those decisions is not ratification of this artifact. The change was drafted by an agent, and only the owner can ratify the exact revision identified below.

The ⚑ design calls were ruled by Brett Heap on 2026-11-14 and are recorded in `design.md` under "Owner rulings (2026-11-14)". Rulings 87 to 89 and 93, accepted on 2026-11-15, are recorded there too, and govern the follow-up listed under "Follow-up of 2026-11-15". Rulings 103 and 107, accepted later on 2026-11-15, govern the corrections listed under "Corrections from the 2026-11-15 spec critique". Ruling 125, accepted on 2026-11-15 with the revenue-cycle core's rulings, governs the edit listed under "Accounting-contract amendment of 2026-11-15 (PA-1)". Rulings 164, 165, 177, 185, 192 and 193, accepted on 2026-11-15 with decisions 152 to 194, are recorded there too; ruling 164 is what the presence of that amendment rests on, and the others govern the edits listed under "Rulings of 2026-11-15 on decisions 152 to 194". Ruling 197, accepted on 2026-11-15 with decisions 195 to 214, is recorded there too, and governs the edits listed under "Rulings of 2026-11-15 on decisions 195 to 214". Ratification still requires the owner to complete this record.

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
- Proposed calls F-6 to F-13 from the 2026-11-15 spec-critique corrections (PR-15's one-party wording and the same-person link under ruling 150; the own-record rule under rulings 107 and 144; the USD-only limit, rounding and one-currency rules under ruling 103; the customer-detail address rule F-11; the owner links under ruling 145, recorded by the practice manager; F-13, an owner link grants no access or authority, and the practice's owner in a requirement is the holder of the practice manager recipe; ruling 193 rules F-13's main lines, and rulings 185 and 192 rule parts of F-7) accepted, changed or rejected: _pending_
- Proposed calls F-20, under ruling 165 (a claim billed on acceptance: the replacement, void and later-coverage details, the accountant's confirmation read as part of the receivable classification, and the customer-detail move at acceptance with its go-live condition), and F-21, conforming to `define-revenue-cycle-core`, a draft not yet on `main` (the rest of its PA-3: the money rules adopted from its feature 1, and Feature 4 folded into it), accepted, changed or rejected: _pending_
- Proposed calls F-14 to F-19 under rulings 125 and 164 (ruling 164 rules that the contract carries the second payer class of F-15 and the three labels of F-16), from the accounting-contract amendment of PA-1 (the service category's details and the estimate reference, the counter payer class, the three labels, the payer class on eight labels, the record of who books explained bank debits, and the AI-13 postings) accepted, changed or rejected: _pending_
- Proposed call F-22, a correction request through a link in identity review neither accepted nor declined until the review confirms the link, its age counted from its receipt, and closed unapplied if its link ends revoked or superseded before it is decided, with or without a review, relying on the EHR to treat it as closed and re-send it on the next established link (EB-09), accepted, changed or rejected: _pending_
- Proposed call F-23, conforming to `define-revenue-cycle-core` CR-58, a draft not yet on `main` (an unidentified receipt identified as a payer's payment: the remittance's own postings and the release of its payment total from one or more receipts, at its posting or at a later link, with any shortfall left in clearing or made work, AI-13), accepted, changed or rejected: _pending_
- Proposed call F-24, a republish of the current snapshot with reason `republish_after_review` after any confirmed review that applied an older version (EB-08), accepted, changed or rejected: _pending_
- Proposed call F-25, conforming to `define-revenue-cycle-core` CR-101, a draft not yet on `main` (a write-off of an amount no accepted claim billed reaches the unbilled control account in summary-journal mode, and needs no posting of its own in customer-detail mode; AI-15, AI-13), accepted, changed or rejected: _pending_
- Decision: _pending; must be exactly `APPROVED UNCONDITIONALLY`_

Required owner statement:

> I reviewed the exact OpenSpec artifact revision identified above and ratify `define-mvp-practice-foundation` unconditionally, with the handoff shape recorded above. Under shape A, I authorize the ordered Speckit features listed in `tasks.md` section 2 as a stated exception to the workflow protocol's one-feature handoff, each created only when its own gate is met. Under shape B, I authorize no feature from this change and direct that it be split first. I understand that each feature's plan phase requires a meaningful Constitution Check against constitution v1.0.0 and a documented security review; that customer-detail accounting postings stay disabled for every destination until a verified BAA, or for a practice-operated destination a verified operator attestation (ruling 6), is recorded for it; that runtime work is confined to openPractice unless separately governed elsewhere; and that this change cannot be archived until the implementing work and the assembly pin bump are merged and accepted.

## Gate Semantics

- An agent, a delegate, an inferred preference, a conditional approval, a blank field, failed strict validation, a companion-change mismatch, a missing handoff-shape choice, or ratification of a different artifact revision does not satisfy this gate.
- Editing this template does not itself constitute ratification; the identified owner must provide the completed, durable record.
- Until every required field is complete and the decision is exactly `APPROVED UNCONDITIONALLY`, no Speckit feature may be created for this change.
- The proposal's controlled `Status` must remain `draft` until the owner completes this record. Only the owner may change it to `ratified`, in the same durable revision as this completed record.
