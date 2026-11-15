## Context

openPractice is an assembly root whose code leg is empty. Constitution v1.0.0 (`.specify/memory/constitution.md` in `opensoft/openPractice`, ratified 2026-08-24) is in force. Its principles are:

- I: HIPAA by design
- II: security review for PHI, money, authorization and credentials
- III: test-first development, with contract tests at every integration boundary
- IV: clinical boundary and original implementation
- V: versioned integration contracts only
- VI: deployment portability

The openPractice `AGENTS.md` adds: "Do not enable autonomous financial or patient-impacting actions by default."

On 2026-11-14 the product owner, Brett Heap, made rulings 34 to 44 on the patient app, escalation and agent operation. They are recorded below. **Rulings are not ratification.** This change stays `Status: draft`, and `owner-ratification.md` stays PENDING until the owner completes it.

Requirement IDs (`PG-nn`, `ER-nn`, `PH-nn`, `MA-nn`) prefix every requirement name in the four spec deltas.

HealthLinc is the owner's working name for the ecosystem patient app. The launch-archetype brainstorm notes that the name still needs clearance. Hermes is the agent that ruling 34 names; the MedxFactory/Hermes companion change governs its side. This change states nothing about the internals of HealthLinc, openChart, Hermes, MedxFactory or openWallet. It makes contract-level statements only, and writes "decided in planning" wherever something is unknown.

### Changes this one stacks on

This change references these changes by requirement ID and does not redefine them:

- **`define-mvp-practice-foundation`** (PR opensoft/openPractice-spec#2). Registry PR-01 to PR-14 (notably PR-04 contacts, PR-05 confidential communication, PR-07 coverages, PR-08 exact match, PR-09 versioned API, PR-10 audit, PR-11 deactivation). EHR boundary EB-01, EB-02, EB-04, EB-05, EB-06, EB-12. Accounting AI-22 and AI-23. Sign-offs BAB-17.
- **`define-actors-agents-and-work-inbox`** (change A, a sibling draft by the same author). This change's branch is stacked on it. It uses `actor-model` AM-01 to AM-06, `agent-operations` AO-01 to AO-12 (contract `openpractice-agent-operations/1.0`), `work-inbox` WI-01 to WI-07, `remote-va-safeguards` VA-01 to VA-05 and `ai-data-handling` AH-01 to AH-05. Change A's branch also amends `ideation/review/openpractice-framing-decisions.md`: FD-1 by ruling 38, and FD-10(b) by ruling 36. This change relies on those amendments and does not edit that file.
- **`define-customer-financial-data-boundaries`** (PR opensoft/openPractice-spec#3, on branch `define-customer-financial-data-boundaries`, not yet on main). It owns customer and party roles, offering routes, obligation ownership and membership entitlements. PG-04 and PH-07 cite its entitlement rules and obligation ownership.

### Sources

Ideation paths are relative to the root of `opensoft/openPractice-spec`. All are `Status: brainstorm` except FD-1 to FD-11, ratified 2026-08-27.

- [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md): FD-1 (frame), FD-4 (self-pay and GFE money path), FD-5 (four tiers and deadline escapes) and FD-10 (channels).
- [`ideation/brainstorm/openpractice-feature-list-healthlinc-app-onboarding.md`](../../../ideation/brainstorm/openpractice-feature-list-healthlinc-app-onboarding.md): verified identity, and "possession of a phone is not authority". Ruling 39 replaces its single linked identity with one link per backend.
- [`ideation/brainstorm/openpractice-feature-list-ai-intake-assistance.md`](../../../ideation/brainstorm/openpractice-feature-list-ai-intake-assistance.md): hard refusal and safe routing of clinical questions.
- [`ideation/brainstorm/openpractice-small-practice-beta-launch-archetype.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-launch-archetype.md): HealthLinc as a working name that needs clearance.

## Owner rulings (2026-11-14)

Every row has the same source: **Brett Heap, 2026-11-14, authoring session.** The requirement text matches each ruling.

| # | Ruling | Governs |
|---|---|---|
| 34 | openPractice is the **gateway and router**. If the patient is active and not on payment hold, the patient gets access to the system. openPractice routes clinical questions to openChart, the doctor's clinical interface, where the doctor reads and answers. If the question is administrative, openPractice answers with Hermes or routes it to the office manager or staff. openPractice keeps only a routing record (who, when, category, urgency, reference), with no clinical content (constitution Principle IV). | PG-03, PG-04, PG-10, ER-01, ER-04, ER-05, ER-10, ER-11 |
| 35 | For now the physician uses openChart, on mobile or workstation. The openPractice app, on workstation and mobile, covers administrative work and approvals. This is a current-phase decision and can be revisited. | MA-01, MA-02, MA-03 |
| 36 | **Amends FD-10(b).** The HealthLinc integration contract and escalation routing come into the MVP. HealthLinc's own app features stay on HealthLinc's roadmap. | PG-01, PG-12, ER-04, T-1 |
| 37 | Autonomy ladder per action type: observe → propose → act with notice → act after a delay. Every action type starts at "propose", and the practice opts each one up. Tier 4 stays human-only. It reconciles with the FD-5 tiers and BAB-17. An agent can never act with less friction than the action's FD-5 tier requires. | ER-10, PH-04, governance tiers |
| 38 | **Amends FD-1.** The agent-operated practice is the primary frame; screens are built for exceptions and approvals. The 2–4 staff mode remains for when agents are off. | ER-02, ER-15, MA-02, T-2 |
| 39 | There is no master patient identity across HealthLinc, openPractice and openChart. HealthLinc links to each backend through that backend's own link contract. | PG-05, PG-06, ER-05 |
| 40 | Packaging is two new changes: change A (`define-actors-agents-and-work-inbox`) and this one. The current four changes are ratified separately. HealthLinc, openChart and MedxFactory/Hermes each need a companion change. They are listed as required companion changes with what each must contain, and are not drafted here. | `proposal.md`, "Required companion changes", `tasks.md` §1.4 |
| 41 | A payment hold blocks non-urgent clinical messaging and new non-urgent scheduling **only**. It never blocks urgent escalations, billing contact, or access to the patient's own records. Placing or lifting a hold is a logged, tiered action, and the patient is told. The medical director approves the hold policy. The exact policy needs the practice's counsel; no legal rule is asserted as fact. | PH-01 to PH-09, PG-03 |
| 42 | "Active" means the patient's registry record is active (PR requirements in `define-mvp-practice-foundation`). Where the practice sells memberships, the membership must also be current, per the entitlement rules in `define-customer-financial-data-boundaries` (PR #3, not yet on main). | PG-04, PH-07 |
| 43 | openChart owns the queue and the response clock for routed clinical questions. openPractice gets an acknowledgment for each handoff. It alerts the office manager if openChart does not acknowledge or the clock runs out. | ER-07, ER-08, ER-09 |
| 44 | HealthLinc's AI proposes the category (clinical or administrative) and the urgency, and openPractice's routing rules apply them. Staff can re-route a misfiled item but cannot answer outside their lane. A mixed question is split. openPractice never downgrades urgency. | ER-02, ER-03, ER-11, ER-12, PG-10 |

## Goals / Non-Goals

**Goals**

- Make openPractice the patient app's gateway and router, behind one versioned contract with a canonical schema.
- Route clinical questions to openChart with no clinical content in openPractice, with an acknowledgment and a fallback for every handoff.
- Answer administrative questions with Hermes under change A's ladder, or with staff by lane.
- Define a payment hold that can never block urgent help, billing contact or the patient's own records.
- Give approvers a thin mobile client for exceptions and approvals.

**Non-Goals**

- Appointment and payment operations in gateway 1.0. They come as later MINOR versions.
- HealthLinc's own app features, openChart's clinical inbox, and Hermes's internals. Each belongs to a companion change.
- Clinical advice of any kind from openPractice.
- A master patient identity.
- Any legal rule about holds, proxies or AI disclosure. Counsel decides those.

## Ownership matrix

| Object or function | openPractice | HealthLinc | openChart |
|---|---|---|---|
| Patient app account; its link to each backend | Stores (HealthLinc issuer, `healthlinc`, value) per gateway link | **Owns** the account; stores each backend's reference | Its own link contract with HealthLinc (companion) |
| Patient message body | Administrative text only (PG-10); never clinical | **Source and store** | Fetches clinical messages from HealthLinc |
| Category and urgency proposal | Applies them by fixed rules; never downgrades | **Proposes** (AI triage) | Receives the applied urgency |
| Routing record | **Source** | — | — |
| Clinical queue, response clock, clinical reply | Shadow deadline and alerts only | Delivers the reply to the patient | **Source** |
| Administrative reply | **Source** (staff, or Hermes under the ladder) | Delivers it | — |
| Capabilities (entitlement) and payment hold | **Source** | Applies them | — |
| Emergency guidance content | Sends a code only | **Source** | — |
| Hold, routing-clock and proxy policies | **Records** versions and approvals | — | — |

## Decisions

### 1. The gateway contract: `openpractice-patient-gateway/1.0`

The canonical schema is `contracts/openpractice-patient-gateway-1.0.schema.json`, SHA-256 `5c9c31768175bc565a0a48a48ff2d7cfd459d5deed2084f18cec3a9c6e90dd8e`. Transport is versioned REST over TLS on openPractice's versioned API layer (PR-09). HealthLinc authenticates as a named integration identity; the mechanism is decided in planning.

| Message | Direction | Purpose |
|---|---|---|
| `entitlement.request` / `entitlement.result` | HealthLinc → openPractice / back | Coded capabilities and coded withheld reasons (PG-03, PG-04) |
| `link.request` / `link.result` | HealthLinc → openPractice / back | Account or representative link after verified identity (PG-05, PG-06) |
| `link.revoked` | either side | Revocation wins (PG-07) |
| `proposal.submitted` / `proposal.ack` / `proposal.resolved` | HealthLinc → openPractice / back | Demographic and coverage proposals, never auto-applied (PG-08, PG-09) |
| `inquiry.submitted` / `inquiry.ack` | HealthLinc → openPractice / back | Intake with HealthLinc's AI proposals; a clinical inquiry carries no text (PG-10) |
| `inquiry.status` / `inquiry.recategorized` | openPractice → HealthLinc | Coded progress; recategorization and resubmission requests (ER-12) |
| `reply.relay` / `reply.ack` | openPractice → HealthLinc / back | Administrative reply with attribution (PG-11) |
| `guidance.instruct` / `guidance.ack` | openPractice → HealthLinc / back | "Show your emergency guidance" (ER-09) |
| `message.ack` | either side | Acknowledges revocations, resolutions, status and recategorization |

**Closed objects and MINOR versions.** Every schema object sets `additionalProperties: false`. A receiver that validates a newer MINOR message against an older schema would reject new fields. PG-01 therefore says a sender never sends a MINOR the receiver has not declared.

**Always-granted capabilities.** The schema requires `urgent_escalation`, `billing_contact` and `own_records_access` in every `entitlement.result`, and the withheld list cannot name them.

**Free text.** The only free-text fields are the administrative question, the administrative reply and the patient-proposed demographic and coverage values. A clinical inquiry that carries text fails the schema.

**Idempotency.** `message_id` is a random UUID and is the idempotency key. No key carries PHI.

### 2. Entitlement and "active"

A linked patient is active when the registry record is active and, where the practice sells memberships, the membership is current (ruling 42). An active patient with no hold gets every capability in the link's scope. A hold withholds two capabilities. A patient who is not active keeps the three always-granted capabilities (design call DC-4). An evaluation failure withholds everything except those three. The result carries codes only: no amount, balance or reason text.

### 3. Account and representative links

One gateway link joins one HealthLinc account to one openPractice patient. There is no master identity (ruling 39). openPractice stores the HealthLinc account triple with namespace `healthlinc` (design call DC-1). A link needs a verification record and a named human's tier-2 confirmation of the record that the practice's invitation or an exact match identifies. Possession of a device, phone number or invitation code is not verified identity. An agent may propose but never sign, because the evidence is untrusted content (AH-05). A representative link also needs an approved proxy policy (design call DC-12).

### 4. Patient proposals

Patient-submitted demographic and coverage changes enter as proposals with patient provenance, become work items, and apply only after a named human accepts them. Accepted changes are ordinary registry changes under PR-04, PR-05, PR-07 and PR-10, and reach linked EHRs under EB-06 with source `patient_self_service`. A proposal that only adds confidential-communication restrictions is honored at once on an interim basis (design call DC-11).

### 5. Routing rules

The routing rules ship with openPractice as a fixed, versioned set. Practices configure only lanes, hours and the on-call roster (design call DC-17). The rules take HealthLinc's AI proposals and apply three defaults: unknown category means clinical; any clinical mark makes that part clinical; and the applied urgency is the highest proposal received from any source (design call DC-14). Urgency is never lowered (ER-03).

### 6. The clinical handoff contract: `openpractice-clinical-handoff/1.0`

The canonical schema is `contracts/openpractice-clinical-handoff-1.0.schema.json`, SHA-256 `74a02a9a6a85c5522e366231c2cd8b0b04141b66a4206c1a54a9209c53984f13`.

**Exactly what crosses (design call DC-2, reference-only).** Besides the envelope (contract, version, `message_id`, message type, issue time, issuer), a `handoff.offer` carries:

| Field | What it is |
|---|---|
| `handoff_id` | The openPractice routing id |
| `link_id` | The EB-01 patient-link id |
| `ehr_patient` | The EHR patient triple from that link |
| `healthlinc_message_ref` | HealthLinc's message reference (issuer, `healthlinc`, value) |
| `reply_path_ref` | HealthLinc's opaque reply-path reference, passed through |
| `category` | Always `clinical` |
| `urgency` | `routine`, `priority` or `urgent` |
| `submitted_at`, `routed_at`, `ack_by`, `respond_by` | Times |
| `clock_policy` | Policy id, version, and `approved` or `fail_closed_default` |
| `origin` | `intake`, `recategorized_from_administrative` or `split_from_mixed` |
| `split_group_ref` | Optional opaque reference for a split question |

Nothing else crosses: no message body, question text, reply text or attachment. The schema has no free-text field, so it cannot carry one. The openPractice patient triple does not cross, because the `link_id` already names the link. openChart fetches the body from HealthLinc over HealthLinc's own link, and replies to HealthLinc over its own reply path (companion items).

| Message | Direction | Purpose |
|---|---|---|
| `handoff.offer` | openPractice → openChart | Reference-only clinical item |
| `handoff.ack` | openChart → openPractice | `accepted` (inbox entry, clock started) or `rejected` with a code |
| `handoff.urgency_raised` | openPractice → openChart | Raise only; no message lowers urgency |
| `handoff.status` | openChart → openPractice | `responded`, `clock_expired`, `returned_not_clinical`, `content_unavailable` or `closed` |
| `message.ack` | either side | Acknowledges raises and status |

**Precondition.** A handoff is offered only on an active EB-01 link that is not in identity review (design call DC-24). Otherwise openPractice does not guess a chart; the item becomes an office-manager exception, and the urgent fallback runs if it is urgent.

### 7. Acknowledgment, clocks and alerts

openChart owns the queue and the clock (ruling 43). openPractice sets `ack_by` and a shadow `respond_by` from the routing-clock policy (design call DC-23), and alerts the office manager on a missing acknowledgment, a rejection, `clock_expired`, `content_unavailable`, or silence past `respond_by`. The medical director approves the policy. Until then the defaults apply (design call DC-3):

| Urgency | Acknowledge within | Respond within |
|---|---|---|
| `urgent` | 5 minutes | 60 minutes |
| `priority` | 15 minutes | 8 hours |
| `routine` | 60 minutes | 48 hours |

These are calendar-time alerting thresholds, chosen to alert early. They are not clinical standards.

### 8. The urgent fallback

An urgent item is never blocked, delayed or dropped. When a handoff fails in any way, openPractice at once alerts the office manager and the on-call clinician, tells HealthLinc to show its own emergency guidance, and keeps retrying. With nobody on call, the medical director is alerted instead (design call DC-27). openPractice sends a code, never advice. No agent is on this path, so the pause switch cannot affect it.

### 9. Hermes and staff lanes

Hermes's answer is the action type `gateway.administrative_reply`, at tier 3. It starts at `propose`: Hermes drafts and a human in the lane approves. Every answer is derived from the patient's message, which is untrusted content, so AO-04 and AH-05 cap it at `propose`. Design call DC-13 proposes a narrow exception for owner review; until he rules, the binding text keeps `propose`. Change A's AO-11 limits the MVP agent contract to registry operations, so the action type needs a governed MINOR version of `openpractice-agent-operations` first (design call DC-22). Until then, every administrative item goes to staff.

Staff lanes come from a fixed topic table: front desk (scheduling, demographics, forms), biller (statements, payments, coverage) and office manager (records requests, general, anything unmapped). Staff answer only in their lane; the office manager's lane covers every administrative lane (design call DC-16). Nobody in openPractice answers a clinical item.

### 10. Re-routing, splitting and removal

When an administrative item turns out to be clinical, it is handed to openChart by HealthLinc's reference and its stored text is removed (design call DC-15). A mixed question is split by HealthLinc's AI, at intake or on request. openPractice staff never see clinical content, so only openChart can return a clinical item as administrative. Audit entries reference stored text by id and never copy it, so removal does not touch the tamper-evident chain (PR-10).

### 11. The payment hold

A hold withholds non-urgent clinical messaging and new non-urgent scheduling only. No hold can be placed before the medical director approves a versioned, counsel-reviewed policy that states every required item (PH-03). Placing is tier 3 with a countdown; lifting is tier 2 (design call DC-5). The patient is told when the countdown starts, before the hold takes effect (design call DC-6). Disputed amounts, and amounts inside an open GFE dispute window, never count until the policy states their treatment (design call DC-8). A lapsed membership is not a hold (design call DC-9). A hold stalls no claim, so it needs no FD-5 deadline escape (PH-08). A hold lifts by itself when its basis ends (design call DC-7).

### 12. The mobile approver

A thin client on the same versioned API, covering approvals, administrative escalations in the user's lane, the controller's day-close signature, and alerts. It shows no clinical content. Notifications carry no PHI, deep links need re-authentication, nothing persists on the device beyond the session, and every sign-off needs re-authentication at signing (design call DC-19). A lost device is revoked at once through AM-05.

## Required companion changes (ruling 40)

These changes are required and are not drafted here. Each must cite this change's canonical schema digests.

1. **`opensoft/openChart-spec`: a clinical message inbox.** It must:
   - receive `openpractice-clinical-handoff/1.0` offers, validated against the schema at digest `74a02a9a6a85c5522e366231c2cd8b0b04141b66a4206c1a54a9209c53984f13`;
   - answer each offer with `handoff.ack`, and refuse an offer on a revoked, unknown or in-review link;
   - own the queue and the response clock, and report `responded`, `clock_expired`, `returned_not_clinical`, `content_unavailable` and `closed`;
   - fetch the message body from HealthLinc over HealthLinc's own link, and never through openPractice;
   - give the clinician a reply path to HealthLinc that bypasses openPractice;
   - apply `handoff.urgency_raised` and never lower urgency;
   - honor confidential-communication instructions for every patient communication it originates (ruling 27, EB-06);
   - say how a clinician reads a routed message for a patient with no EB-01 link (Open Question 5).
2. **HealthLinc: the patient app side of the gateway.** It must:
   - send AI triage proposals for category and urgency, with a triage reference, and never send clinical text to openPractice;
   - split mixed questions at intake, and resubmit on `inquiry.recategorized`;
   - consume `entitlement.result` as given, never remove the three always-granted capabilities, and never derive a restriction on another backend's records from it;
   - link to each backend through that backend's own link contract, with no master identity, store (openPractice issuer, `openpractice`, value), and use verified identity;
   - keep emergency guidance in the app, and show it on `guidance.instruct`, when openPractice is unreachable, and when an urgent inquiry is rejected;
   - keep push text free of PHI, and show patients when an agent wrote a reply;
   - validate against the gateway schema at digest `5c9c31768175bc565a0a48a48ff2d7cfd459d5deed2084f18cec3a9c6e90dd8e`.
3. **MedxFactory/Hermes: the agent side.** It must:
   - identify each Hermes instance by an openWallet ID, as a roster entry with a sponsoring human (AM-04);
   - consume `openpractice-agent-operations`, including the MINOR version that adds `gateway.administrative_reply`, `payment_hold.place` and `payment_hold.lift` (DC-22);
   - honor the ladder from `propose`, the AH-05 untrusted-content cap, the pause switch (AO-09), and the rate and blast-radius limits (AO-08);
   - meet AH-02 (provider BAA), AH-03 (minimum necessary) and AH-04 (no PHI in agent logs).

## Design calls for owner review

Each item below goes beyond rulings 34 to 44. Each is proposed, not ruled. Where it fails closed, the binding text already does; otherwise the binding text cites the design call so nothing is silently frozen.

| # | Design call | Where |
|---|---|---|
| DC-1 | Namespace token `healthlinc` for HealthLinc account and message references. HealthLinc is a working name that needs clearance; a rename would not change the token within `1.x`. | PG-05, both schemas |
| DC-2 | Reference-only clinical handoff, with exactly the fields in Decision 6. openChart fetches the body from HealthLinc. | ER-05, handoff schema |
| DC-3 | Fail-closed default acknowledgment and response windows until the medical director approves a policy (Decision 7). | ER-08 |
| DC-4 | A linked patient who is not active, or whose status cannot be evaluated, still gets urgent escalation, billing contact and own-records access. This **extends ruling 34 for safety**. | PG-03, PG-04 |
| DC-5 | Placing a hold is tier 3 (countdown and cancel window); lifting is tier 2. | PH-04, T-3 |
| DC-6 | The patient is told when the countdown starts, before the hold takes effect. | PH-05 |
| DC-7 | A hold lifts automatically, at tier 2 with notice, when its balance is paid or resolved, a dispute leaves the criteria unmet, or its policy version is withdrawn or expires. | PH-09, T-6 |
| DC-8 | Until the policy says otherwise, an amount inside an open GFE dispute window counts as disputed. | PH-06 |
| DC-9 | A lapsed membership is not a payment hold; it affects access only through the entitlement check. Membership dues count toward a hold only if the policy includes them. | PH-07 |
| DC-10 | Ruling 42 in a practice that sells memberships but also serves non-members: this change reads "the membership must be current" as applying to patients whose access the entitlement rules make membership-based. Until those rules are ratified, such a practice's patients resolve as `membership_entitlement_unresolved` and keep only the three always-granted capabilities (fail closed). | PG-04 |
| DC-11 | A patient proposal that only adds confidential-communication restrictions is honored at once, on an interim basis, without changing the registry record. | PG-08 |
| DC-12 | Representative links need a versioned proxy policy that the medical director approves after counsel's review. Before one exists, none can be created. | PG-06 |
| DC-13 | A narrow exception to the `propose` cap on Hermes answers: an answer drawn verbatim from a practice-approved, versioned answer library with no patient-specific data, where the patient's text only selects the entry, could be opted up to `act_after_delay` only. This needs change A's AH-05 to allow it. **Not in binding text.** | ER-10, T-7 |
| DC-14 | Urgency and clinical-category proposals from staff and agents are applied by rule (raise only; clinical wins), so a safety raise never waits for an approval. | ER-02, ER-12, T-6 |
| DC-15 | Administrative question and reply text is stored in openPractice under Principle I safeguards, referenced by id from audit entries, and removed when an item is found clinical. | PG-10, ER-12, T-5 |
| DC-16 | The office manager's lane covers every administrative lane. | ER-11 |
| DC-17 | The routing rules are fixed and versioned; practices configure only lanes, hours and the on-call roster. | ER-02, T-2 |
| DC-18 | HealthLinc shows the patient when an agent wrote a reply. Counsel confirms any disclosure duty. | PG-11 |
| DC-19 | The mobile approver keeps no PHI on the device beyond the session, and every sign-off needs re-authentication at signing. | MA-05, MA-07 |
| DC-20 | Gateway 1.0 carries no attachments, such as insurance-card images. | PG-09 |
| DC-21 | Handoff shape A (ordered features, `tasks.md` §2) for this change. Rulings 34 to 44 do not state a shape. | `tasks.md` §1.2 |
| DC-22 | Agents get no gateway action type until a governed MINOR version of `openpractice-agent-operations` carries it, because AO-11 limits the MVP to registry operations. Until then, administrative items go to staff. | ER-10 |
| DC-23 | openPractice keeps a shadow `respond_by` and treats silence past it as an expired clock. | ER-07 |
| DC-24 | A handoff is offered only on an active EB-01 link that is not in identity review; a patient with several EHR links goes to the configured EHR, or else to an exception. | ER-06 |
| DC-25 | A human-sent reply has no tier-3 countdown: the explicit send is the release decision. | PG-11, T-8 |
| DC-26 | A hold never cancels a booked appointment, and never blocks an appointment a clinician marks as needed or one arising from an urgent escalation. | PH-01 |
| DC-27 | Clinical routing cannot be enabled until the on-call roster covers every hour or names the fallback; with nobody on call, the medical director and office manager are alerted. | ER-13 |
| DC-28 | Hold, routing-clock and proxy policy approvals are never opted in for agent signing. | PH-03, ER-08, PG-06 |

## Governance tiers (FD-5)

No action in this change is tier 4, and none is a D8 approval. Tier 4 stays human-only and D8 stays accountant-only wherever the mobile approver carries them (MA-07).

| Action | Tier | Signer (BAB-17) | Agent ceiling (AO-04) |
|---|---|---|---|
| Apply routing rules; hand off; alert; run the urgent fallback | Automatic, not an action type (T-6) | No sign-off | None; no agent on this path |
| Raise urgency; recategorize an item as clinical | Automatic by rule from any proposal (DC-14, T-6) | No sign-off; the proposer is recorded | Agents propose, and the rule applies |
| Confirm a gateway account link (PG-05) | 2, explicit confirmation (T-4) | Named human only | `propose` (AH-05) |
| Confirm a representative link (PG-06) | 2, explicit confirmation (T-4) | Named human only | `propose` (AH-05) |
| Revoke a gateway link (PG-07) | 2; a patient's own request takes effect at once | Named human only, except the patient's own request | `propose` (AH-05) |
| Accept or decline a demographic or coverage proposal (PG-08, PG-09) | 2, explicit decision (T-4) | Named human only | `propose` (AH-05) |
| Re-route between administrative lanes (ER-12) | 2 | Named human | `propose` (AH-05) |
| Send a staff-written administrative reply (PG-11) | 3; the explicit send is the release (DC-25, T-8) | Named human in the item's lane | — |
| Send an agent-drafted reply, `gateway.administrative_reply` (ER-10) | 3 | At `propose`, a named human in the lane approves | `propose` (AH-05); DC-13 proposes an exception, to `act_after_delay` only |
| Place a hold, `payment_hold.place` (PH-04) | 3 (DC-5, T-3) | Named human, or an opted-in named agent | `act_after_delay` only; never `act_with_notice`, which would skip the countdown |
| Lift a hold, `payment_hold.lift` (PH-04) | 2 (DC-5) | Named human, or an opted-in named agent | `act_with_notice` or `act_after_delay` |
| Automatic lift when the basis ends (PH-09) | 2, automatic (DC-7, T-6) | No sign-off; notification | — |
| Approve a hold policy version (PH-03) | Approval (T-4) | Medical director only; counsel's review recorded; never opted in (DC-28) | None |
| Approve a routing-clock policy version (ER-08) | Approval (T-4) | Medical director only; never opted in (DC-28) | None |
| Approve a proxy policy version (PG-06) | Approval (T-4) | Medical director only; counsel's review recorded; never opted in (DC-12, DC-28) | None |
| Revoke a mobile device session (MA-06) | 2 | The user, the office manager, or AM-05 offboarding | — |
| Day-close signature of the daily agent digest (MA-02) | As AO-10 | The controller | As AO-10 |
| Any other sign-off on mobile (MA-07) | That action's tier | That action's signer; tier 4 human only; D8 accountant only | That action's ceiling |

Every sign-off records the signer's identity, including an agent's openWallet ID, with provenance (BAB-17).

**Deadline escapes (FD-5).** Every gate names its escape:
- The clinical handoff is not a gate. Its escape is the alert path and the urgent fallback (ER-07, ER-09).
- Link confirmation, representative confirmation, proposal review and Hermes draft approval stall no claim. Claims use the registry's current data (PG-09). Their escape is escalation through the work-item clock (WI-06), not release.
- A payment hold stalls no claim (PH-08), so it needs no escape and no exception.
- The hold, routing-clock and proxy policy approvals hold nothing deadline-bearing. Holds and representative links are refused meanwhile (fail closed), and the default clock windows apply.

## Constitution Check (pre-handoff, against v1.0.0)

| Principle or gate | Obligation | Status |
|---|---|---|
| I. HIPAA by design | Administrative text encrypted, access-controlled and audited on every read (PG-10). No PHI in logs, errors, notifications, keys or diagnostics (PG-13, ER-16). Mobile idle logoff, no PHI on the device, re-authentication (MA-04, MA-05). | Requirement level; a safeguard plan is owed per feature |
| II. Security review | Every feature touches PHI or authorization. Automatic routing, the urgent fallback and automatic hold lifts are on by default (T-6). Hermes answers stay at `propose` (T-7). | Owed per feature; T-6 and T-7 need the owner's decision |
| III. Test-first | Contract tests at both boundaries against the canonical schemas (PG-14, ER-17). Synthetic fixtures only. | Owed per feature |
| IV. Clinical boundary | Routing record only; no clinical text in openPractice; reference-only handoff (ER-01, ER-05). Transient misfiled content is removed (T-5). | Satisfied, with T-5 recorded |
| V. Versioned contracts | Two contracts, each with one canonical schema and digest. Agents only through the agent-operations contract. No database access. | Satisfied |
| VI. Portability | HealthLinc and openChart are optional; openPractice runs without either (PG-02). | Satisfied |
| Data gate | Synthetic fixtures; no credentials. | Requirement level |

## Conflicts and tensions

These are conflicts with ratified text: FD-1 to FD-11, constitution v1.0.0, and the openPractice `AGENTS.md` line on autonomous actions. Eight are recorded. Two rest on owner amendments (T-1, T-2); the other six need the owner's disposition at ratification.

| # | Tension | Status |
|---|---|---|
| T-1 | **FD-10(b)** keeps the companion app deferred. This change brings the HealthLinc gateway contract and escalation routing into the MVP. | **Amended by ruling 36**, recorded on change A's branch. This change can be ratified only with or after that amendment. FD-10(b)'s no-account pay link is unchanged (PG-12). |
| T-2 | **FD-1** fixes a 2–4 in-house staff frame with "no routing engines, no approval ladders". This change adds routing rules and uses change A's ladder. | **Amended by ruling 38.** Residual: the routing rules are a routing engine in FD-1's words. Mitigated by a fixed, shipped rule set with no rule authoring (DC-17). Conscious exception proposed. |
| T-3 | **FD-5** lists tier 3 for outbound actions. Placing a hold restricts access and is not itself outbound. | Design call DC-5 for owner review. Tier 3 chosen because a hold affects the patient and carries an outbound notice. |
| T-4 | **FD-5** replaces approval with reversibility below tier 4. This change keeps explicit sign-offs below tier 4: link and representative confirmation, proposal review, approval of Hermes drafts at `propose`, and the hold, routing-clock and proxy policy approvals. | **Conscious exception proposed**, like `define-mvp-practice-foundation` T-17. Signers follow BAB-17; AH-05 bars agents where the content is untrusted. |
| T-5 | **Principle IV**, read with ruling 34. When HealthLinc misfiles a clinical or mixed question as administrative, openPractice briefly holds clinical text until someone finds it. | Residual risk. Text is removed at recategorization (ER-12, DC-15); backups age out. HealthLinc's AI splits at intake (companion). |
| T-6 | **Principle II and `AGENTS.md`**: no autonomous patient-impacting or financial action by default. This change turns on by default: rule-based routing and handoff; the urgent fallback, which tells HealthLinc to show guidance; automatic urgency raises and clinical recategorization from proposals (DC-14); and automatic hold lifts (DC-7). | **Conscious exception proposed for patient safety.** Routing and the fallback only deliver to humans or show the app's own guidance, and decide nothing about care. Hold lifts only restore access. Owner to confirm. |
| T-7 | **Principle II and `AGENTS.md`**, for Hermes answering patients. | Consistent by default: answers start at `propose`, with a human approving each one (ER-10). An opt-up would need the owner to accept the per-action-type opt-in as the "explicit human approval flow". DC-13 is not in binding text. |
| T-8 | **FD-5 tier 3** runs a countdown before outbound actions. A staff member's reply is sent without a countdown. | Design call DC-25: the explicit human send is the release decision, as for batch release in `define-mvp-practice-foundation` (AI-06, T-17). |

## Risks / Trade-offs

- **[A missed urgent message]** → Never blocked by status or hold; acknowledgment windows; shadow deadlines; the fallback to the office manager, the on-call clinician and HealthLinc's guidance; retries; no agent on the path.
- **[Clinical content leaks into openPractice]** → No text on clinical intake; a reference-only handoff with no free-text field; removal on recategorization (T-5).
- **[Wrong-patient link]** → Verified identity, exact match, human confirmation, no auto-linking, revocation, and an incident assessment.
- **[A hold harms care]** → Only two capabilities; never urgent, billing or records; a fail-closed policy gate; counsel review; patient notice before effect; automatic lift.
- **[Agent overreach]** → Start at `propose`; the AH-05 cap; no gateway action type before the agent-operations MINOR; the pause switch.
- **[Prompt injection through a patient message]** → Patient text is data, never instructions (AH-05); Hermes answers wait for a human.
- **[Lost phone]** → No PHI on the device; immediate revocation through AM-05.
- **[Contract drift]** → Canonical schemas compared by digest with each companion (`tasks.md` §1.4).

## Migration Plan

1. Validate strictly. Ratify `define-mvp-practice-foundation` and change A, including the FD-1 and FD-10(b) amendments. The owner completes `owner-ratification.md`.
2. Feature 1: the gateway, tested against a HealthLinc double.
3. Feature 2: escalation routing and the clinical handoff, tested against openChart and HealthLinc doubles. Clinical routing is enabled only with full on-call cover.
4. Feature 3: the payment hold. Placing a hold stays refused until a policy is approved.
5. Feature 4: the mobile approver.
6. Live interoperation waits for each companion change to be ratified and to match by digest.
7. Rollback disables the gateway or clinical routing and keeps every routing record and audit entry. Open urgent items stay owned.
8. Archive only after the features land and `closure-evidence.md` is complete.

## Open Questions

Every question is routed in `tasks.md` §3.5 to the work it blocks.

1. **Identity verification mechanism** for gateway links (PG-05). Decided in planning.
2. **Proxy policy content** (PG-06), including minors' confidentiality and personal-representative scope. Counsel.
3. **Hold policy content** (PH-03). Counsel; the medical director approves.
4. **Hold scope across one guarantor's dependents.** Whether a guarantor's balance can place holds on several patients. The policy decides.
5. **A clinician reading a routed message for a patient with no EB-01 link** (ER-06). openChart companion; the exception path applies meanwhile.
6. **The on-call roster's source** and its relation to change A's role recipes (ER-13). Planning.
7. **Routing-clock windows** (ER-08). The medical director; defaults meanwhile.
8. **Ruling 42 in a mixed membership practice** (DC-10). The owner.
9. **AI-authorship disclosure** for agent replies (DC-18). Counsel.
10. **Retention of administrative communication records** (PG-10). Not covered by `add-reimbursement-retention-control`. The owner and counsel.
11. **The agent-operations MINOR** for gateway action types (DC-22). Change A's owner.
12. **HealthLinc name clearance** and the `healthlinc` token (DC-1). The owner.
