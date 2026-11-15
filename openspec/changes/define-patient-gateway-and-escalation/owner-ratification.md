# Owner Ratification Record

Status: **PENDING — SPECKIT FEATURE CREATION IS NOT AUTHORIZED**

This record is the mandatory, durable owner gate for OpenSpec change `define-patient-gateway-and-escalation`. Creating any Speckit feature before this record is completed exactly as required is prohibited.

The rulings this change encodes (34 to 54) were made by Brett Heap on 2026-11-14 and are recorded in `design.md` under "Owner rulings (2026-11-14)". Making those rulings is not ratification of this artifact. The change was drafted by an agent, and only the owner can ratify the exact revision identified below.

The 38 design calls in `design.md` under "Design calls for owner review" have not been ruled. Ratification records the owner's disposition of each.

## Required Validation Evidence

- OpenSpec version: _pending_
- Validation command: `openspec validate define-patient-gateway-and-escalation --strict --no-interactive`
- Validation timestamp: _pending_
- Exact ratified artifact revision or checksums: _pending_
- Validation exit code: _pending_
- Validation output or durable evidence reference: _pending_

## Required Stacked-Dependency Evidence

- `define-mvp-practice-foundation` ratified revision: _pending_
- `define-actors-agents-and-work-inbox` ratified revision: _pending_
- FD-1 amendment (ruling 38) and FD-10(b) amendment (ruling 36) landed in `ideation/review/openpractice-framing-decisions.md`, with revision: _pending_
- `define-customer-financial-data-boundaries` status (its entitlement rules gate only PG-04's membership condition and PH-07): _pending_

## Required Companion-Change Consistency Evidence

Ruling 40 requires three companion changes, and rulings 47 to 49 asked for them to be drafted. A mismatch with an existing companion blocks feature creation. A companion that does not exist yet blocks only live interoperation.

- **`opensoft/openChart-spec`, change `define-clinical-message-inbox`**
  - Companion change and revision compared: _pending_
  - Clinical-handoff schema digest matches (`contracts/SHA256SUMS` here against the digest the companion cites): _pending_
  - Contract identifier matches (`openpractice-clinical-handoff/1.0`): _pending_
  - Namespaces match (`healthlinc`, `openpractice`, and the EHR's own): _pending_
  - Issuer handling matches (EB-02): _pending_
- **`opensoft/HealthLinc`, change `define-practice-gateway-integration`** (private; cited by repository and change id only)
  - Companion change and revision compared: _pending_
  - Gateway schema digest matches: _pending_
  - Contract identifier matches (`openpractice-patient-gateway/1.0`): _pending_
  - Namespace `healthlinc` and issuer handling match: _pending_
  - Entitlement semantics match (three always-granted capabilities; no derived restrictions): _pending_
- **`MedxSoft/medXfactory`, change `define-care-organization-hermes-openpractice-operations`**
  - Companion change and revision compared: _pending_
  - Read-only answer types in agent-operations 1.0, and the MINOR carrying `gateway.administrative_reply`, `payment_hold.place` and `payment_hold.lift`: _pending_
  - `medxfactory` conversation references, and the medical AI's chart path outside openPractice (ruling 53): _pending_
  - Ladder from `propose`, the AH-05 cap and the pause switch honored: _pending_

## Required Owner Decision

- Owner identity: _pending_
- Ratification timestamp: _pending_
- Handoff shape: _pending; must be exactly `A — ordered multi-feature handoff` or `B — split into per-capability changes` (design call DC-21 proposes `A`; no ruling states a shape)_
- Design-call dispositions (DC-1 to DC-38, each accepted, changed or rejected): _pending_
- Tension dispositions (T-1 to T-10; T-9 and T-10 are recorded as resolved by rulings 50 and 52): _pending_
- Decision: _pending; must be exactly `APPROVED UNCONDITIONALLY`_

Required owner statement:

> I reviewed the exact OpenSpec artifact revision identified above and ratify `define-patient-gateway-and-escalation` unconditionally, with the handoff shape, design-call dispositions and tension dispositions recorded above. Under shape A, I authorize the ordered Speckit features listed in `tasks.md` section 2 as a stated exception to the workflow protocol's one-feature handoff, each created only when its own gate is met. Under shape B, I authorize no feature from this change and direct that it be split first. I understand that each feature's plan phase requires a meaningful Constitution Check against constitution v1.0.0 and a documented security review; that an urgent escalation is never blocked, delayed by a hold, or dropped; that no payment hold may be placed until the medical director approves a counsel-reviewed policy version; that no representative link may be created until a proxy policy is approved; that agents start at `propose` for every action type in this change, and answer a patient without a human only through change A's AH-05 exception 1; that runtime work is confined to openPractice unless separately governed elsewhere; and that this change cannot be archived until the implementing work and the assembly pin bump are merged and accepted.

## Gate Semantics

- An agent, a delegate, an inferred preference, a conditional approval, a blank field, failed strict validation, an unratified stacked dependency, a mismatch with an existing companion change, a missing handoff-shape choice, an undispositioned design call, or ratification of a different artifact revision does not satisfy this gate.
- Editing this template does not itself constitute ratification; the identified owner must provide the completed, durable record.
- Until every required field is complete and the decision is exactly `APPROVED UNCONDITIONALLY`, no Speckit feature may be created for this change.
- The proposal's controlled `Status` must remain `draft` until the owner completes this record. Only the owner may change it to `ratified`, in the same durable revision as this completed record.
