# Tasks

This checklist records governance and handoff only. This change currently
delivers proposed specifications and review documentation; executable product
implementation tasks belong to exactly one later Speckit feature.

## 1. Scope and Contract Review

- [ ] 1.1 Record acceptance or amendments to the captured owner direction and proposed elaborations, including the customer financial record, role boundaries and obligation owners; verify the decision references this proposal and all three capability deltas without treating brainstorm status as ratification.
- [ ] 1.2 Record the first deployment's connector/field profiles, selected posting routes, membership entitlement rules and release scope; verify every output has an owner, purpose, recipient and permitted fields and no dual posting route can add the same event twice.
- [ ] 1.3 Run `openspec validate define-customer-financial-data-boundaries --strict --no-interactive` on the exact revision proposed for ratification, together with `define-mvp-practice-foundation` at the revision it references; verify exit code 0 and record the OpenSpec version, command, timestamp, revision, exit code and output in `owner-ratification.md`.
- [ ] 1.4 Obtain the owner's ratification in `owner-ratification.md`; verify every field is complete and the decision is exactly `APPROVED UNCONDITIONALLY` for the validated revision before any Speckit feature exists.

## 2. Single Speckit Handoff

- [ ] 2.1 After tasks 1.3 and 1.4 pass, create exactly one Speckit feature covering the three capability deltas; verify its feature reference and traceability matrix include every requirement/scenario and keep all executable tasks in that feature.
- [ ] 2.2 Reconcile affected baseline passages and pre-existing governance references during feature planning, including the period-close wording and actual assembly constitution version; verify the feature's Constitution Check uses the current authoritative assembly file and preserves FD-3, FD-5 and existing small-practice boundaries.

## 3. Acceptance and Triad Integration

- [ ] 3.1 Accept the feature's security, integration and financial-reconciliation evidence, including denied chart access, recipient/BAA controls, mixed receipts, duplicate posting, revocation and outreach checks; verify accepted evidence maps to all scenarios and contains only synthetic or explicitly de-identified data.
- [ ] 3.2 Land the applicable leg work and the assembly pin update before archival; verify merged references, lockstep pins, strict OpenSpec validation and accepted feature evidence, without claiming a spec-leg commit alone advances the project.
