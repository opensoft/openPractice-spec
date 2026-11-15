Status: draft

Ratification is pending. The owner has not ratified this change; see `owner-ratification.md`.

## Why

openPractice has no governed answer to four questions about the patient app:

- how the patient app talks to openPractice, and what a patient may do through it;
- where a patient's question goes, and who answers it;
- what happens when a patient owes money;
- how approvers work away from a desk.

FD-10(b) kept the companion app out of the beta. On 2026-11-14 the product owner, Brett Heap, amended that (ruling 36): the HealthLinc integration contract and escalation routing come into the MVP. HealthLinc is the owner's working name for the ecosystem patient app. The owner also made openPractice the **gateway and router** (ruling 34): an active patient who is not on a payment hold gets access, clinical questions go to openChart, and administrative questions are answered by Hermes or by staff. openPractice keeps a routing record only, with no clinical content.

The owner made rulings 34 to 44 that day, and later that day rulings 45 to 54. They are recorded in `design.md` under "Owner rulings (2026-11-14)". On 2026-11-15 he accepted ruling 93, a follow-up to this merged draft that closes gaps its consumers found, and ruling 97 on consent; both are recorded there too, and `design.md` lists the follow-up's edits under "Follow-up of 2026-11-15". This change records them as governed requirements before any runtime exists. **Rulings are not ratification:** the change stays draft until the owner completes `owner-ratification.md`.

Ideation paths are relative to the root of `opensoft/openPractice-spec`. FD-1 to FD-11 in [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md) were ratified on 2026-08-27. This change records twelve tensions, T-1 to T-12 in `design.md`. Two rest on owner amendments carried by change `define-actors-agents-and-work-inbox`: FD-10(b) by ruling 36 (T-1) and FD-1 by ruling 38 (T-2). Three are resolved by owner rulings: T-9 by ruling 50, T-10 by ruling 52 and T-12 by ruling 71. T-11 records a gap in sibling changes, with a proposed follow-up. The rest are proposed conscious exceptions or design calls for the owner.

## What Changes

- **`patient-app-gateway` (PG-01 to PG-16).** Adds `openpractice-patient-gateway/1.0` between HealthLinc and openPractice, with a canonical schema and digest. It covers:
  - an entitlement check that returns coded capabilities, where urgent escalation, billing contact and own-records access are always granted to a linked patient, and non-urgent medXfactory medical-AI consults are gated like non-urgent clinical messaging (ruling 52); HealthLinc's coach is never gated;
  - safeguards for cut-offs other than a hold: patient notice, a grace period and clinician continuity (ruling 50);
  - "active" as an active registry record plus, where the practice sells memberships, a current membership (ruling 42);
  - account and representative links, one per backend, with no master identity (ruling 39), after verified identity and a named human's confirmation; a linked representative's relationship and authority reference come back in `link.result` (ruling 93);
  - a verifiable entitlement assertion for a relying party other than HealthLinc, such as medXfactory's medical AI: a signed JSON Web Token (JWS, EdDSA over Ed25519) issued by openPractice for one gateway link and one audience, with issue and expiry times, a unique id and the relying party's nonce, carrying no PHI and no master identity. It proves what openPractice granted, not who is using a session. Urgent access never depends on it (PG-16, ruling 93);
  - patient-submitted demographic and coverage changes as proposals, reviewed before they apply;
  - question and escalation intake from a conversation HealthLinc holds or a medical-AI conversation, named by source and reference (ruling 51), with the proposer named, including the red-flag checklist for a direct message to the doctor (ruling 71), and reply relay, where a clinical question carries no text;
  - patient notices (`patient.notice`, `notice.ack`) for hold countdowns and lifts and for ruling 50's grace periods, where a countdown or grace period starts only after delivery is confirmed or recorded;
  - no gateway capability for a HealthLinc deployment until the agreement counsel requires with its operator is verified;
  - no PHI in logs, notifications, keys or diagnostics, and contract tests.

  Appointments and payments come as later MINOR versions.
- **`escalation-routing` (ER-01 to ER-19).** Adds:
  - a routing record with no clinical content;
  - fixed routing rules that apply the category and urgency proposed by HealthLinc's coach or medXfactory's medical AI, and never lower urgency (rulings 44 and 51);
  - `openpractice-clinical-handoff/1.0` to openChart, reference-only, with a canonical schema and digest;
  - acknowledgment and a response clock owned by openChart, with alerts to the office manager (ruling 43);
  - an urgent fallback, including on an expired or silent clock, with tracked on-call alerts escalated to the medical director, that tells HealthLinc to show its own emergency guidance;
  - Hermes answers under change A's ladder: read-only answers under change A's AH-05 exception 1 (rulings 45 and 46), and drafts at `propose`; staff answers by lane;
  - a clinician's clinical-need marker from openChart (ER-18);
  - re-routing and splitting, so no clinical part stays in openPractice; a mixed question found late comes back as the same question, with a reminder on the lane until it does (ruling 106); the agent operator deletes its copies of removed text and confirms, and model providers keep none (ruling 104);
  - after-hours cover, ownership at all times, and the pause switch;
  - an identity state on every handoff, so urgent and priority items are offered even on a link in review or with no chart link;
  - clinician urgency raises from openChart, which get the urgent path;
  - a clinician's acknowledgment in openChart, reported to openPractice as `handoff.status` `clinician_acknowledged` and passed to HealthLinc as `inquiry.status` `clinician_acknowledged`, changing no safety path (ER-19, ruling 93); and an alert to the office manager when an urgent item has no clinician acknowledgment in time, which changes no clinical path (ER-20, ruling 101).
- **`payment-hold` (PH-01 to PH-09).** A hold blocks only routine clinical messaging, new non-urgent scheduling and non-urgent medical-AI consults, and never urgent help, billing contact or the patient's own records (rulings 41 and 52). No hold is placed before the medical director approves a versioned, counsel-reviewed policy. Placing is tier 3 and lifting tier 2, both proposed. The patient is told. Disputed balances and GFE dispute windows never count until the policy states their treatment. A hold stalls no claim.
- **`mobile-approver` (MA-01 to MA-07).** A thin client on the same versioned API, for approvals, administrative escalations, the day-close signature and alerts (rulings 35 and 38). No PHI in notifications or on the device; a face, fingerprint or passkey verification on an enrolled device, or its unlock code where neither face nor fingerprint is available, never a typed password, to open an item (skipped within 60 seconds of the last one) and for every sign-off (rulings 105, 134 and 135); lost-device revocation through AM-05. The physician's clinical work stays in openChart.
- **Design calls.** Sixty-seven items that go beyond the rulings are listed in `design.md` under "Design calls for owner review". Each either fails closed in binding text or is cited there as proposed.

## Required companion changes

Ruling 40 requires three companion changes. Rulings 47 to 49 asked for them to be drafted, and they are being drafted in their own repositories, not here. Ruling 93 splits the medXfactory entry across two medXfactory changes, so four entries follow. `design.md` lists what each must contain.

- **`opensoft/openChart-spec`, change `define-clinical-message-inbox`:** a clinical message inbox that receives routed escalations under `openpractice-clinical-handoff/1.0`, acknowledges each one, reports a clinician's acknowledgment as `clinician_acknowledged`, owns the response clock, fetches the conversation by reference from HealthLinc or medXfactory through `openchart-conversation-retrieval/1.0`, the retrieval contract it owns (ruling 69), gives the clinician a reply path to HealthLinc, and sends a clinician's clinical-need marker.
- **`opensoft/HealthLinc`, change `define-practice-gateway-integration`** (a private repository, cited by repository and change id only): the three patient paths of ruling 51 (coach, connect to my doctor, connect to medXfactory's medical AI), category and urgency proposals with the conservative defaults, an entitlement-check consumer that passes openPractice's entitlement assertion on unchanged, account links to each backend through that backend's own contract, and emergency guidance that stays in the app.
- **`MedxSoft/medXfactory`, change `define-care-organization-hermes-openpractice-operations`:** agent operations only: agent identity through openWallet, consumption of the agent-operations contract, including its work-item operations and openPractice's consent check, honoring the ladder and the pause switch, and Hermes's administrative messages kept separate from clinical ones (ruling 68). It authorizes no clinical answering.
- **`MedxSoft/medXfactory`, change `define-patient-medical-ai-connection`:** clinical AI answering and its clinical-safety governance, verification of openPractice's entitlement assertion before a non-urgent consult, and the medical AI's records reaching openChart through MedxEHR, which relays them with receipts (rulings 53 and 70). Ruling 93 corrects the earlier draft, which placed these in the Hermes change.

## Dependencies

- **`define-mvp-practice-foundation`** (merged on `main`): the registry (PR-01 to PR-14), the EHR link (EB-01, EB-02, EB-04, EB-05, EB-06, EB-12), AI-22, AI-23 and BAB-17.
- **`define-actors-agents-and-work-inbox`** (change A): AM-01 to AM-06, AO-01 to AO-12, WI-01 to WI-07, VA-01 to VA-05 and AH-01 to AH-05, and its amendments of FD-1 (ruling 38) and FD-10(b) (ruling 36), all merged on `main`. This change builds on change A.
- **`define-customer-financial-data-boundaries`** (merged on `main`): membership entitlement rules (PG-04) and obligation ownership (PH-07).

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
- `patient-app-gateway` (PG-01 to PG-16): the gateway contract and schema, the operator-agreement gate, patient notices, entitlement and "active", the verifiable entitlement assertion, account and representative links, revocation, patient proposals, inquiry intake, reply relay, 1.0 scope, no-PHI rules and contract tests.
- `escalation-routing` (ER-01 to ER-20): the routing record, rules and urgency, the clinical handoff contract and its exact payload, the link precondition, acknowledgment and clocks, the urgent fallback with tracked alerts, Hermes and staff lanes, re-routing and splitting, on-call cover, ownership, the pause switch, no-PHI rules, contract tests, the clinical-need marker and the clinician-acknowledged status.
- `payment-hold` (PH-01 to PH-09): what a hold blocks (three non-urgent capabilities) and never blocks, the policy gate, tiers, patient notice, disputes and GFE windows, memberships, no claim stall, and lifting.
- `mobile-approver` (MA-01 to MA-07): the thin client, its scope, the clinical boundary, notifications, no PHI on the device, lost-device revocation and sign-offs.

### Modified Capabilities

None. This change depends on, and does not modify, the three changes named under Dependencies.

## Impact

- **New openPractice data contracts:** gateway link, representative link, proxy policy, patient proposal, routing record, administrative communication record, routing-clock policy, on-call roster, payment hold and hold policy, and mobile device session.
- **External contracts:** two are fixed, with canonical schemas in `contracts/` (`SHA256SUMS` included): `openpractice-patient-gateway/1.0` and `openpractice-clinical-handoff/1.0`.
- **PHI exposure:** the gateway carries administrative PHI (questions, replies, proposals). The clinical handoff carries references only. Each feature needs a security review and a risk-analysis update under Principle II.
- **Contract tests:** required at both boundaries, with synthetic fixtures only.
- **Runtime:** no runtime code, dependencies or vendor claims are added.
