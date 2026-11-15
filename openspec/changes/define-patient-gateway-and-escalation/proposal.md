Status: draft

Ratification is pending. The owner has not ratified this change; see `owner-ratification.md`.

## Why

openPractice has no governed answer to four questions about the patient app:

- how the patient app talks to openPractice, and what a patient may do through it;
- where a patient's question goes, and who answers it;
- what happens when a patient owes money;
- how approvers work away from a desk.

FD-10(b) kept the companion app out of the beta. On 2026-11-14 the product owner, Brett Heap, amended that (ruling 36): the HealthLinc integration contract and escalation routing come into the MVP. HealthLinc is the owner's working name for the ecosystem patient app. The owner also made openPractice the **gateway and router** (ruling 34): an active patient who is not on a payment hold gets access, clinical questions go to openChart, and administrative questions are answered by Hermes or by staff. openPractice keeps a routing record only, with no clinical content.

The owner made rulings 34 to 44 that day. They are recorded in `design.md` under "Owner rulings (2026-11-14)". This change records them as governed requirements before any runtime exists. **Rulings are not ratification:** the change stays draft until the owner completes `owner-ratification.md`.

Ideation paths are relative to the root of `opensoft/openPractice-spec`. FD-1 to FD-11 in [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md) were ratified on 2026-08-27. This change conflicts with ratified text in eight recorded places, tensions T-1 to T-8 in `design.md`. Two rest on owner amendments carried by change `define-actors-agents-and-work-inbox`: FD-10(b) by ruling 36 (T-1) and FD-1 by ruling 38 (T-2). The rest are proposed conscious exceptions or design calls for the owner.

## What Changes

- **`patient-app-gateway` (PG-01 to PG-14).** Adds `openpractice-patient-gateway/1.0` between HealthLinc and openPractice, with a canonical schema and digest. It covers:
  - an entitlement check that returns coded capabilities, where urgent escalation, billing contact and own-records access are always granted to a linked patient;
  - "active" as an active registry record plus, where the practice sells memberships, a current membership (ruling 42);
  - account and representative links, one per backend, with no master identity (ruling 39), after verified identity and a named human's confirmation;
  - patient-submitted demographic and coverage changes as proposals, reviewed before they apply;
  - administrative question intake and reply relay, where a clinical question carries no text;
  - no PHI in logs, notifications, keys or diagnostics, and contract tests.

  Appointments and payments come as later MINOR versions.
- **`escalation-routing` (ER-01 to ER-17).** Adds:
  - a routing record with no clinical content;
  - fixed routing rules that apply HealthLinc's AI proposals for category and urgency, and never lower urgency (ruling 44);
  - `openpractice-clinical-handoff/1.0` to openChart, reference-only, with a canonical schema and digest;
  - acknowledgment and a response clock owned by openChart, with alerts to the office manager (ruling 43);
  - an urgent fallback that alerts the office manager and the on-call clinician and tells HealthLinc to show its own emergency guidance;
  - Hermes answers under change A's ladder, starting at `propose`, and staff answers by lane;
  - re-routing and splitting, so no clinical part stays in openPractice;
  - after-hours cover, ownership at all times, and the pause switch.
- **`payment-hold` (PH-01 to PH-09).** A hold blocks only non-urgent clinical messaging and new non-urgent scheduling, and never urgent help, billing contact or the patient's own records (ruling 41). No hold is placed before the medical director approves a versioned, counsel-reviewed policy. Placing is tier 3 and lifting tier 2, both proposed. The patient is told. Disputed balances and GFE dispute windows never count until the policy states their treatment. A hold stalls no claim.
- **`mobile-approver` (MA-01 to MA-07).** A thin client on the same versioned API, for approvals, administrative escalations, the day-close signature and alerts (rulings 35 and 38). No PHI in notifications or on the device; re-authentication for deep links and sign-offs; lost-device revocation through AM-05. The physician's clinical work stays in openChart.
- **Design calls.** Twenty-eight items that go beyond the rulings are listed in `design.md` under "Design calls for owner review". Each either fails closed in binding text or is cited there as proposed.

## Required companion changes

Ruling 40 requires three companion changes. They are not drafted here. `design.md` lists what each must contain.

- **`opensoft/openChart-spec`:** a clinical message inbox that receives routed escalations under `openpractice-clinical-handoff/1.0`, acknowledges each one, owns the response clock, fetches the message from HealthLinc, and gives the clinician a reply path to HealthLinc.
- **HealthLinc:** AI triage output for category and urgency, an entitlement-check consumer, account links to each backend through that backend's own contract, and emergency guidance that stays in the app.
- **MedxFactory/Hermes:** agent identity through openWallet, consumption of the agent-operations contract, and honoring the ladder and the pause switch.

## Dependencies

- **`define-mvp-practice-foundation`** (PR opensoft/openPractice-spec#2): the registry (PR-01 to PR-14), the EHR link (EB-01, EB-02, EB-04, EB-05, EB-06, EB-12), AI-22, AI-23 and BAB-17.
- **`define-actors-agents-and-work-inbox`** (change A): AM-01 to AM-06, AO-01 to AO-12, WI-01 to WI-07, VA-01 to VA-05 and AH-01 to AH-05, and its amendments of FD-1 (ruling 38) and FD-10(b) (ruling 36). This change's branch is stacked on change A.
- **`define-customer-financial-data-boundaries`** (PR opensoft/openPractice-spec#3, on branch `define-customer-financial-data-boundaries`, not yet on main): membership entitlement rules (PG-04) and obligation ownership (PH-07).

## Non-goals

- Appointment and payment operations in gateway 1.0.
- HealthLinc's own app features, openChart's clinical inbox, and Hermes's internals.
- Clinical advice of any kind from openPractice.
- A master patient identity.
- Attachments, such as insurance-card images, in gateway 1.0.
- Any legal rule about holds, proxies or AI disclosure.
- Runtime code and other repositories.

## Capabilities

### New Capabilities
- `patient-app-gateway` (PG-01 to PG-14): the gateway contract and schema, entitlement and "active", account and representative links, revocation, patient proposals, inquiry intake, reply relay, 1.0 scope, no-PHI rules and contract tests.
- `escalation-routing` (ER-01 to ER-17): the routing record, rules and urgency, the clinical handoff contract and its exact payload, the link precondition, acknowledgment and clocks, the urgent fallback, Hermes and staff lanes, re-routing and splitting, on-call cover, ownership, the pause switch, no-PHI rules and contract tests.
- `payment-hold` (PH-01 to PH-09): what a hold blocks and never blocks, the policy gate, tiers, patient notice, disputes and GFE windows, memberships, no claim stall, and lifting.
- `mobile-approver` (MA-01 to MA-07): the thin client, its scope, the clinical boundary, notifications, no PHI on the device, lost-device revocation and sign-offs.

### Modified Capabilities

None. This change depends on, and does not modify, the three changes named under Dependencies.

## Impact

- **New openPractice data contracts:** gateway link, representative link, proxy policy, patient proposal, routing record, administrative communication record, routing-clock policy, on-call roster, payment hold and hold policy, and mobile device session.
- **External contracts:** two are fixed, with canonical schemas in `contracts/` (`SHA256SUMS` included): `openpractice-patient-gateway/1.0` and `openpractice-clinical-handoff/1.0`.
- **PHI exposure:** the gateway carries administrative PHI (questions, replies, proposals). The clinical handoff carries references only. Each feature needs a security review and a risk-analysis update under Principle II.
- **Contract tests:** required at both boundaries, with synthetic fixtures only.
- **Runtime:** no runtime code, dependencies or vendor claims are added.
