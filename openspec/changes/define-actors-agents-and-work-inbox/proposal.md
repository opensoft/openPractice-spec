Status: draft

## Why

On 2026-11-14 Brett Heap, the product owner, set out how openPractice will be used. Five kinds of users work through one governed API:
- MedxFactory's Hermes agents, which will try to do every practice-management task;
- office staff and remote virtual assistants (VAs) at a workstation;
- the medical director and the controller or office manager, mostly on a phone;
- patients, through the HealthLinc app;
- HealthLinc's in-app AI, which ruling 51 makes a coach that gives general health information only, not a doctor. Clinical AI answering belongs to medXfactory's medical AI, which the patient reaches through HealthLinc.

`define-mvp-practice-foundation` (PR opensoft/openPractice-spec#2) governs the patient record, the EHR boundary and accounting. It does not yet say who the actors are, how an agent may act, or where work waits for a person.

He ruled on eleven questions the same day (rulings 34 to 44), and later that day made rulings 45 to 54. All are recorded in `design.md`. This change encodes the parts about actors, agents and work. Its sibling `define-patient-gateway-and-escalation` encodes the patient gateway, escalation routing, the payment hold and the mobile approver. **Rulings are not ratification:** this change stays draft until the owner completes `owner-ratification.md`.

Paths are relative to the root of `opensoft/openPractice-spec`. This change is stacked on branch `change/define-mvp-practice-foundation`, so its references to that change resolve. Neither change is on main yet.

## What Changes

- **`actor-model`** (AM-01 to AM-06):
  - declared actor types;
  - five new role recipes on top of the roster brainstorm's seven, granting nothing until the owner approves their grant sets;
  - attribution of actor, actor type and principal on every action;
  - AI agents as named roster entries with an openWallet ID and a sponsoring human;
  - one same-day offboarding switch for people, VAs and agents;
  - every owner a roster entry.
- **`agent-operations`** (AO-01 to AO-12): contract `openpractice-agent-operations/1.0` with a canonical schema and digest. It provides:
  - parity between screen and API;
  - explicit, revocable scopes per action type;
  - the autonomy ladder (observe → propose → act with notice → act after a delay), capped by each action's FD-5 tier and reconciled with BAB-17;
  - dry run, idempotency, and evidence and reasoning references;
  - rate and blast-radius limits;
  - a practice-wide pause;
  - a daily agent digest signed in the day close.

  The MVP builds the patient-registry operations and the read-only answer types through which Hermes answers routine administrative questions (rulings 45 and 46).
- **`work-inbox`** (WI-01 to WI-07):
  - one queue with an owner, clock, tier and proposed action on every item;
  - nothing left unowned;
  - approve, change or reject under the tier;
  - agent-to-human handoff;
  - absence cover;
  - the existing gates of `define-mvp-practice-foundation` carried by reference, not redefined.
- **`remote-va-safeguards`** (VA-01 to VA-05): a verified BAA with the VA company first; own queue only; no export; masked fields; device, session and location rules; access that expires; per-VA accountability.
- **`ai-data-handling`** (AI data handling, AH-01 to AH-05). These rules gate every agent grant at any rung, and a lapse suspends all agent PHI access:
  - verified BAAs with the agent operator and with each model provider;
  - minimum-necessary PHI through AI-23 recipient profiles;
  - no PHI in agent logs;
  - untrusted content treated as data, never instructions, with untrusted contribution derived by openPractice from provenance. This is the prompt-injection defence;
  - one numbered exception (ruling 45): read-only answers to the signed-in patient, assembled on the server from approved templates, at `act_after_delay` only.
- **Framing-decision amendments** (in a separate commit on this branch): `ideation/review/openpractice-framing-decisions.md` gains an "Amendments (2026-11-14)" section. It records FD-1 as amended by ruling 38 and FD-10(b) as amended by ruling 36. The original text stays above it.
- **Brainstorm pointer.** Rulings 36 to 38 supersede part of the "Post-beta by default" list in `ideation/brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md`, which covers the companion app and AI intake:
  - the HealthLinc integration contract and escalation routing are MVP scope;
  - agent operation through the governed API is MVP scope.

  HealthLinc's own app features stay on HealthLinc's roadmap.

## Required companion changes (rulings 40 and 47 to 49; being drafted)

Ruling 40 required three companion changes. Rulings 47 to 49 asked for them to be drafted, and they are being drafted in their own repositories, not in this change. The sibling change lists their full minimum contents.

| Repository | Change id | It must contain, at minimum |
|---|---|---|
| `MedxSoft/medXfactory` | `define-care-organization-hermes-openpractice-operations` | Agent identity through openWallet; consumption of `openpractice-agent-operations/1.0`, including the read-only answer types; honoring the autonomy ladder, the caps, the per-patient scope and the pause switch; the operator BAA of AH-02 |
| `opensoft/openChart-spec` | `define-clinical-message-inbox` | A clinical message inbox for routed escalations, with acknowledgment, response clock and the clinician's reply path to HealthLinc |
| `opensoft/HealthLinc` (private) | `define-practice-gateway-integration` | The patient app side of the gateway; cited here by repository and change id only |

## Non-goals

- Drafting the companion changes in this change, or editing any other repository. The companions are being drafted separately (rulings 47 to 49).
- Agent operations beyond the patient registry in the MVP; each later feature adds its own operations.
- Changing any requirement of `define-mvp-practice-foundation`. This change references that change's gates and does not redefine them.
- Claims about any vendor, VA company, model provider, openWallet internals or Hermes internals.

## Capabilities

### New Capabilities
- `actor-model` (AM-01 to AM-06)
- `agent-operations` (AO-01 to AO-12)
- `work-inbox` (WI-01 to WI-07)
- `remote-va-safeguards` (VA-01 to VA-05)
- `ai-data-handling` (AH-01 to AH-05)

### Modified Capabilities

None. This change depends on `define-mvp-practice-foundation` (PR #2) and references `define-customer-financial-data-boundaries` (PR #3). It modifies neither.

## Impact

- **New contract:** `openpractice-agent-operations/1.0`, with its canonical schema and `SHA256SUMS` in `contracts/`.
- **PHI exposure:** no agent receives a grant at any rung, and no PHI reaches an agent layer, until verified BAAs with the agent operator and each model provider are recorded, and then only within recipient profiles and for the work item's patient. A lapse suspends all agent PHI access. Every grant and opt-in needs a security review under Principle II.
- **Ratified-text tensions:** this change departs from FD-1 and FD-10(b) through rulings 38 and 36, keeps FD-1's "no approval ladders" as a conscious exception under ruling 37 (T-6), and departs from FD-5's approval-free stance through ruling 37's propose-first default. Each is recorded in `design.md`, with T-7 for ruling 45's exception to design call D-12.
- **Runtime:** none. No runtime code, dependencies or vendor claims are added.
