# Owner Ratification Record

Status: **PENDING — SPECKIT FEATURE CREATION IS NOT AUTHORIZED**

This record is the mandatory, durable owner gate for OpenSpec change `define-patient-gateway-and-escalation`. Creating any Speckit feature before this record is completed exactly as required is prohibited.

The rulings this change encodes (34 to 54, with 68 to 71) were made by Brett Heap on 2026-11-14, and rulings 93 and 97, which govern this change's follow-up, on 2026-11-15, followed the same day by rulings 101, 102 and 104 to 106, which govern its corrections. They are recorded in `design.md` under "Owner rulings (2026-11-14)", the follow-up's edits under "Follow-up of 2026-11-15", and the corrections under "Corrections from the 2026-11-15 spec critique". Making those rulings is not ratification of this artifact. The change was drafted by an agent, and only the owner can ratify the exact revision identified below.

The 68 design calls in `design.md` under "Design calls for owner review" have not been ruled; DC-46 to DC-53 come from the 2026-11-15 follow-up and its review, DC-54 to DC-59, DC-61 and DC-62 are design calls under rulings 101, 104 to 107, 134 and 135, DC-60 under ruling 137, DC-63 comes from the review of the corrections, DC-64 and DC-65 are design calls under rulings 143 and 149, DC-66 and DC-67 come from the reviews of this pull request and of `define-practice-readiness`, and DC-68 from the review of `define-revenue-cycle-core`. Ratification records the owner's disposition of each.

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

Ruling 40 requires three companion changes, and rulings 47 to 49 asked for them to be drafted. Ruling 93 splits the medXfactory entry across two medXfactory changes, so four are compared below. A mismatch with an existing companion blocks feature creation. A companion that does not exist yet blocks only live interoperation.

- **`opensoft/openChart-spec`, change `define-clinical-message-inbox`**
  - Companion change and revision compared: _pending_
  - Clinical-handoff schema digest matches (`contracts/SHA256SUMS` here against the digest the companion cites): _pending_
  - Contract identifier matches (`openpractice-clinical-handoff/1.0`): _pending_
  - Namespaces match (`healthlinc`, `openpractice`, and the EHR's own): _pending_
  - Issuer handling matches (EB-02): _pending_
  - `clinician_acknowledged` sent once, at the owner's first explicit acknowledgment while the item is open (a `clinician` or `nurse` owner), with no identity, role or note (ER-19): _pending_
- **`opensoft/HealthLinc`, change `define-practice-gateway-integration`** (private; cited by repository and change id only)
  - Companion change and revision compared: _pending_
  - Gateway schema digest matches: _pending_
  - Contract identifier matches (`openpractice-patient-gateway/1.0`): _pending_
  - Namespace `healthlinc` and issuer handling match: _pending_
  - Entitlement semantics match (three always-granted capabilities; no derived restrictions): _pending_
  - The entitlement assertion is requested for a relying party and passed on unchanged (PG-16); a representative's relationship and authority are taken from openPractice (PG-06); `clinician_acknowledged` is reported only when `inquiry.status` carries it (ER-19): _pending_
  - A late split's administrative part resubmitted as the same question, with its own `inquiry_ref`, `recategorizes_routing_id`, and no second notice to the patient (ER-12): _pending_
- **`MedxSoft/medXfactory`, change `define-care-organization-hermes-openpractice-operations`** (agent operations only)
  - Companion change and revision compared: _pending_
  - Read-only answer types and work-item operations in agent-operations 1.0, and the MINOR carrying `gateway.administrative_reply`, `payment_hold.place` and `payment_hold.lift`: _pending_
  - openPractice's consent check relied on for patient answers (change A AH-06); Hermes's administrative messages separated from clinical ones (ruling 68); no clinical answering: _pending_
  - Ladder from `propose`, the AH-05 cap and the pause switch honored: _pending_
  - Model providers under verified zero data retention, and the operator's deletion of misrouted copies confirmed within the deadline (ER-12; change A AH-02): _pending_
- **`MedxSoft/medXfactory`, change `define-patient-medical-ai-connection`** (the medical AI; ruling 93)
  - Companion change and revision compared: _pending_
  - Clinical AI answering and its clinical-safety governance owned there; `medxfactory` conversation references; the medical AI's records reaching openChart through MedxEHR, outside openPractice (rulings 53 and 70): _pending_
  - The entitlement assertion verified before any non-urgent consult with every PG-16 check, `sub` compared only with a link the relying party bound itself, per enrolment and per link, and urgent need never withheld: _pending_

## Required Owner Decision

- Owner identity: _pending_
- Ratification timestamp: _pending_
- Handoff shape: _pending; must be exactly `A — ordered multi-feature handoff` or `B — split into per-capability changes` (design call DC-21 proposes `A`; no ruling states a shape)_
- Design-call dispositions (DC-1 to DC-68, each accepted, changed or rejected; DC-39 is marked for the owner, DC-46 to DC-53 are PROPOSED by the 2026-11-15 follow-up and its review, DC-54 to DC-59, DC-61 and DC-62 are PROPOSED under rulings 101, 104 to 107, 134 and 135, DC-60 under ruling 137, DC-63 by the review of the corrections, DC-64 under ruling 143, DC-65, the counsel-record fields, under ruling 149's check, DC-66, the confirmer's identity check, DC-67, who receives an escalation while no medical director assignment is in effect, and DC-68, a hold re-evaluated when a same-person reconciliation opens): _pending_
- Open Questions 22 (the on-call alert for a missing clinician acknowledgment) and 23 (a split that openChart asks for) dispositioned; Open Questions 24, 25 and 26 are closed by rulings 134, 135 and 136: _pending_
- Tension dispositions (T-1 to T-12; T-9, T-10 and T-12 are recorded as resolved by rulings 50, 52 and 71, and T-11 proposes a follow-up): _pending_
- Decision: _pending; must be exactly `APPROVED UNCONDITIONALLY`_

Required owner statement:

> I reviewed the exact OpenSpec artifact revision identified above and ratify `define-patient-gateway-and-escalation` unconditionally, with the handoff shape, design-call dispositions and tension dispositions recorded above. Under shape A, I authorize the ordered Speckit features listed in `tasks.md` section 2 as a stated exception to the workflow protocol's one-feature handoff, each created only when its own gate is met. Under shape B, I authorize no feature from this change and direct that it be split first. I understand that each feature's plan phase requires a meaningful Constitution Check against constitution v1.0.0 and a documented security review; that an urgent escalation is never blocked, delayed by a hold, or dropped; that no payment hold may be placed until the medical director approves a counsel-reviewed policy version; that no representative link may be created until a proxy policy is approved; that agents start at `propose` for every action type in this change, and answer a patient without a human only through change A's AH-05 exception 1; that runtime work is confined to openPractice unless separately governed elsewhere; and that this change cannot be archived until the implementing work and the assembly pin bump are merged and accepted.

## Gate Semantics

- An agent, a delegate, an inferred preference, a conditional approval, a blank field, failed strict validation, an unratified stacked dependency, a mismatch with an existing companion change, a missing handoff-shape choice, an undispositioned design call, or ratification of a different artifact revision does not satisfy this gate.
- Editing this template does not itself constitute ratification; the identified owner must provide the completed, durable record.
- Until every required field is complete and the decision is exactly `APPROVED UNCONDITIONALLY`, no Speckit feature may be created for this change.
- The proposal's controlled `Status` must remain `draft` until the owner completes this record. Only the owner may change it to `ratified`, in the same durable revision as this completed record.
