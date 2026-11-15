## Context

openPractice is an assembly root whose code leg is empty. Constitution v1.0.0 (`.specify/memory/constitution.md` in `opensoft/openPractice`, ratified 2026-08-24) is in force. Its principles are:

- I: HIPAA by design
- II: security review for PHI, money, authorization and credentials
- III: test-first development, with contract tests at every integration boundary
- IV: clinical boundary and original implementation
- V: versioned integration contracts only
- VI: deployment portability

The openPractice `AGENTS.md` adds: "Do not enable autonomous financial or patient-impacting actions by default."

On 2026-11-14 the product owner, Brett Heap, made rulings 34 to 44 on the patient app, escalation and agent operation, and later that day rulings 45 to 54. They are recorded below. **Rulings are not ratification.** This change stays `Status: draft`, and `owner-ratification.md` stays PENDING until the owner completes it.

Requirement IDs (`PG-nn`, `ER-nn`, `PH-nn`, `MA-nn`) prefix every requirement name in the four spec deltas.

HealthLinc is the owner's working name for the ecosystem patient app. The launch-archetype brainstorm notes that the name still needs clearance. Hermes is the agent that ruling 34 names; the medXfactory companion change governs its side. HealthLinc's in-app AI is a coach, not a doctor, and clinical AI answering belongs to medXfactory's medical AI, which the patient reaches through HealthLinc (ruling 51). This change states nothing about the internals of HealthLinc, its coach, openChart, Hermes, medXfactory, its medical AI or openWallet. It makes contract-level statements only, and writes "decided in planning" wherever something is unknown.

### Changes this one stacks on

This change references these changes by requirement ID and does not redefine them:

- **`define-mvp-practice-foundation`** (PR opensoft/openPractice-spec#2). Registry PR-01 to PR-14 (notably PR-04 contacts, PR-05 confidential communication, PR-07 coverages, PR-08 exact match, PR-09 versioned API, PR-10 audit, PR-11 deactivation). EHR boundary EB-01, EB-02, EB-04, EB-05, EB-06, EB-12. Accounting AI-22 and AI-23. Sign-offs BAB-17.
- **`define-actors-agents-and-work-inbox`** (change A, a sibling draft by the same author). This change's branch is stacked on it. It uses `actor-model` AM-01 to AM-06, `agent-operations` AO-01 to AO-12 (contract `openpractice-agent-operations/1.0`), `work-inbox` WI-01 to WI-07, `remote-va-safeguards` VA-01 to VA-05 and `ai-data-handling` AH-01 to AH-05, including AH-05's exception 1 for read-only answers (ruling 45). Change A's branch also amends `ideation/review/openpractice-framing-decisions.md`: FD-1 by ruling 38, and FD-10(b) by ruling 36. This change relies on those amendments and does not edit that file.
- **`define-customer-financial-data-boundaries`** (PR opensoft/openPractice-spec#3, on branch `define-customer-financial-data-boundaries`, not yet on main). It owns customer and party roles, offering routes, obligation ownership and membership entitlements. PG-04 and PH-07 cite its entitlement rules and obligation ownership.

### Sources

Ideation paths are relative to the root of `opensoft/openPractice-spec`. All are `Status: brainstorm` except FD-1 to FD-11, ratified 2026-08-27.

- [`ideation/review/openpractice-framing-decisions.md`](../../../ideation/review/openpractice-framing-decisions.md): FD-1 (frame), FD-4 (self-pay and GFE money path), FD-5 (four tiers and deadline escapes) and FD-10 (channels).
- [`ideation/brainstorm/openpractice-feature-list-healthlinc-app-onboarding.md`](../../../ideation/brainstorm/openpractice-feature-list-healthlinc-app-onboarding.md): verified identity, and "possession of a phone is not authority". Ruling 39 replaces its single linked identity with one link per backend.
- [`ideation/brainstorm/openpractice-feature-list-ai-intake-assistance.md`](../../../ideation/brainstorm/openpractice-feature-list-ai-intake-assistance.md): hard refusal and safe routing of clinical questions.
- [`ideation/brainstorm/openpractice-small-practice-beta-launch-archetype.md`](../../../ideation/brainstorm/openpractice-small-practice-beta-launch-archetype.md): HealthLinc as a working name that needs clearance.

## Owner rulings (2026-11-14)

Rows 34 to 71 have the same source: **Brett Heap, 2026-11-14, authoring session.** Rows marked (2026-11-15) have the source **Brett Heap, 2026-11-15, accepting the recommendations on decisions 79 to 98 except 80**; each quotes the question and the accepted wording. The requirement text matches each ruling.

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
| 44 | HealthLinc's AI proposes the category (clinical or administrative) and the urgency, and openPractice's routing rules apply them. Staff can re-route a misfiled item but cannot answer outside their lane. A mixed question is split. openPractice never downgrades urgency. **Amended by ruling 51.** | ER-02, ER-03, ER-11, ER-12, PG-10 |
| 45 | Hermes may reply to a patient without a human only with read-only answers. Such an answer changes nothing and tells the signed-in patient only their own administrative information (appointment time, balance, coverage on file) or practice-approved information (hours, policies). During a conversation the agent can read only that one patient's data; each answer type is switched on by the practice; no clinical content, and anything uncertain goes to staff; the patient can always ask for a human. Anything that changes something stays a proposal or follows the ladder. | Change A AH-05 exception 1; ER-10, ER-14, PG-11, DC-13, T-7 |
| 46 | The read-only answer types are in the MVP agent operations contract (1.0), alongside the registry operations. In the MVP, Hermes answers routine administrative questions. | ER-10, DC-22 |
| 47 | Draft the openChart companion: `define-clinical-message-inbox` in `opensoft/openChart-spec`. | "Required companion changes" |
| 48 | Draft the HealthLinc companion: `define-practice-gateway-integration` in `opensoft/HealthLinc`, a private repository cited by repository and change id only. | "Required companion changes" |
| 49 | Draft the medXfactory companion: `define-care-organization-hermes-openpractice-operations` in `MedxSoft/medXfactory`. | "Required companion changes" |
| 50 | Cut-offs other than a payment hold get safeguards too. If the access check is unavailable, routine clinical messages still route to openChart. A lapsed membership or a deactivation stops messaging only after the patient is told and a policy-set grace period has passed. A clinician can keep access open for continuity of care. | PG-04, ER-18, DC-31, T-9 |
| 51 | HealthLinc's in-app AI is a **coach**, not a doctor: it gives general health information with a clear not-medical-advice statement. Clinical AI doctors live in `MedxSoft/medXfactory`, and HealthLinc talks to medXfactory. HealthLinc is a gateway to the patient's doctor: from it the patient connects to their human doctor (through openPractice to openChart) or to medXfactory's medical AI. **Amends ruling 44:** category and urgency may be proposed by HealthLinc's coach or by medXfactory's medical AI. Both use the conservative defaults: an uncertain category is clinical, an uncertain urgency is urgent, and urgency is never lowered. openPractice's rules still apply the proposal and never downgrade it. An escalation to the human doctor may originate from either source. | PG-10, ER-01, ER-02, ER-05, both schemas, DC-37 |
| 52 | Non-urgent medXfactory medical-AI consults sit behind the practice's access check. A payment hold may withhold them, with the same safeguards as non-urgent clinical messaging. Urgent is never withheld. HealthLinc's coach is HealthLinc's own and is never gated by the practice. | PG-03, PG-04, PH-01, DC-38, T-10 |
| 53 | The medical AI's exchanges with a patient are recorded in the chart through medXfactory's own governed path (MedxEHR and openChart). They never pass through openPractice, which holds no clinical content. **Refined by ruling 70.** | PG-10, ER-05, "Required companion changes" |
| 68 | Hermes's administrative patient messages are separated from clinical ones in medXfactory's local policy. Administrative messages follow ruling 45's guardrails with no clinical review. Consent and data-sharing conditions still apply. | ER-10, DC-13, "Required companion changes" |
| 69 | openChart owns one neutral conversation-retrieval contract, `openchart-conversation-retrieval/1.0`, in openChart-spec change `define-clinical-message-inbox`. HealthLinc and medXfactory implement it. | ER-05, both schemas' descriptions, "Required companion changes" |
| 70 | The medical AI's records reach openChart through MedxEHR, which relays them with receipts. medXfactory does not write to openChart directly. openPractice is never in that path. | PG-10, "Required companion changes" |
| 71 | When a patient writes straight to the doctor with no AI triage, HealthLinc asks a short red-flag checklist. Any red flag proposes `urgent`, with emergency guidance; no red flags, `routine`; a skipped checklist, `priority`. The patient can raise urgency but never lower it. | PG-10, ER-02, ER-03, both schemas (`patient_checklist`), DC-45, T-12 |
| 54 | HealthLinc's coach gives general information only. Anything individualized is handed to medXfactory's medical AI or to the doctor. The content boundary is the rule; the not-medical-advice statement is required in addition. | "Required companion changes" (HealthLinc) |
| 93 | (2026-11-15) Question: follow-up change to the merged openPractice contracts. Accepted: "Yes, one draft PR carrying: the agent operations Hermes lacks and the consent check; a verifiable entitlement; a clinician-acknowledged status; the representative's relationship and authority; the retraction reason code; the #38/#39 erratum." | PG-03, PG-06, PG-14, PG-16 (verifiable entitlement; representative's relationship and authority); ER-01, ER-17, ER-19 (clinician-acknowledged status); ER-10 (work-item operations and consent check, carried by change A); both schemas; "Required companion changes" (the erratum); "Follow-up of 2026-11-15" below |
| 97 | (2026-11-15) Question: does Hermes need each patient's consent before a model provider processes their administrative data? Accepted: "No; the provider agreement is enough for practice operations, with counsel to confirm. Consent applies to AI answering the patient directly." | ER-10 (openPractice's consent check for agent answers, change A AH-06) |

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
| Patient message body | Administrative text only (PG-10); never clinical | **Source and store** of the conversations it holds: coach conversations, direct messages to the doctor and administrative threads | Retrieves clinical messages from HealthLinc through `openchart-conversation-retrieval/1.0` (ruling 69) |
| Medical-AI conversation (ruling 51) | Conversation source and reference only | Connects the patient to medXfactory | Retrieves it by reference from medXfactory through the same contract; the medical AI's records reach openChart through MedxEHR, which relays them with receipts (rulings 53 and 70) |
| Category and urgency proposal | Applies them by fixed rules; never downgrades | **Proposes** through its coach or, for a direct message to the doctor, its red-flag checklist, or passes on medXfactory's medical-AI proposal (rulings 51 and 71) | Receives the applied urgency |
| Routing record | **Source** | — | — |
| Clinical queue, response clock, clinical reply | Shadow deadline and alerts only | Delivers the reply to the patient | **Source** |
| Administrative reply | **Source**: staff; Hermes drafts at `propose`; read-only answers assembled by openPractice from approved templates | Delivers it | — |
| Capabilities (entitlement) and payment hold | **Source** | Applies them, including to medical-AI consults; never to its own coach | — |
| HealthLinc's coach | Never gates it | **Owns** it; general information only (ruling 54) | — |
| Clinical-need marker | Records and honors it | — | **Source**: a clinician records it |
| Emergency guidance content | Sends a code only | **Source** | — |
| Hold, routing-clock and proxy policies | **Records** versions and approvals | — | — |

## Decisions

### 1. The gateway contract: `openpractice-patient-gateway/1.0`

The canonical schema is `contracts/openpractice-patient-gateway-1.0.schema.json`, SHA-256 `dd6ea44d981feb59caa3a1399384ec3d4045b8715f4ecb3c39b15bc01efbec41` (it was `f2596a9daa997d4d88142fd425bae4a6a0a70656856c6434de843553a2c8123f` before the follow-up of 2026-11-15). Transport is versioned REST over TLS on openPractice's versioned API layer (PR-09). HealthLinc authenticates as a named integration identity; the mechanism is decided in planning.

| Message | Direction | Purpose |
|---|---|---|
| `entitlement.request` / `entitlement.result` | HealthLinc → openPractice / back | Coded capabilities and coded withheld reasons (PG-03, PG-04), and, for a registered relying party, a verifiable assertion (PG-16) |
| `link.request` / `link.result` | HealthLinc → openPractice / back | Account or representative link after verified identity; a linked representative's relationship and authority reference (PG-05, PG-06) |
| `link.revoked` | either side | Revocation wins (PG-07) |
| `proposal.submitted` / `proposal.ack` / `proposal.resolved` | HealthLinc → openPractice / back | Demographic and coverage proposals, never auto-applied (PG-08, PG-09) |
| `inquiry.submitted` / `inquiry.ack` | HealthLinc → openPractice / back | Intake with the proposals of HealthLinc's coach or medXfactory's medical AI, and the conversation source; a clinical inquiry carries no text (PG-10) |
| `inquiry.status` / `inquiry.recategorized` | openPractice → HealthLinc | Coded progress, including `clinician_acknowledged` (ER-19); recategorization and resubmission requests (ER-12) |
| `reply.relay` / `reply.ack` | openPractice → HealthLinc / back | Administrative reply with attribution (PG-11) |
| `guidance.instruct` / `guidance.ack` | openPractice → HealthLinc / back | "Show your emergency guidance", keyed by routing id or by a refused inquiry's `message_id` (ER-09, PG-10) |
| `patient.notice` / `notice.ack` | openPractice → HealthLinc / back | Hold countdown, hold lift, grace start and access restoration, with an approved template and no amount or reason; HealthLinc confirms delivery (PH-05, PG-04) |
| `message.ack` | either side | Acknowledges revocations, resolutions, status and recategorization |

**Closed objects and MINOR versions.** Every schema object sets `additionalProperties: false`. A receiver that validates a newer MINOR message against an older schema would reject new fields. PG-01 therefore says a sender never sends a MINOR the receiver has not declared.

**Always-granted capabilities.** The schema requires `urgent_escalation`, `billing_contact` and `own_records_access` in every `entitlement.result`, and the withheld list cannot name them. The reason `payment_hold` may appear only on the three capabilities a hold may withhold. For a representative without billing scope, the schema refuses a `payment_hold` reason and a hold-policy reference (PG-06).

**Verifiable assertion (PG-16).** A relying party other than HealthLinc, such as medXfactory's medical AI before a non-urgent consult, cannot trust an entitlement HealthLinc relays. When `entitlement.request` names a registered `assertion_audience`, `entitlement.result` carries an `assertion`: the issuer, the audience, the `gateway_link_id`, the link kind and, for a representative, the relationship and authority reference, the granted capabilities that audience may rely on (always including `urgent_escalation`), `issued_at`, `expires_at`, a unique `assertion_id`, an optional echoed `audience_nonce`, a `key_ref` and a signature over the RFC 8785 canonical JSON of the rest (design calls DC-46 to DC-48). It carries no PHI, no withheld reason, no hold and no master identity. Urgent access never depends on it.

**Operator agreement.** No gateway capability is enabled for a HealthLinc deployment until the agreement counsel requires with its operator is verified; until then every message is refused with `operator_agreement_unverified` (PG-15, design call DC-43).

**Conversation source.** Every inquiry names where its conversation lives, `healthlinc` for HealthLinc's coach or `medxfactory` for medXfactory's medical AI, with a reference in that system whose namespace matches (ruling 51, design call DC-37). No conversation content enters openPractice.

**Free text.** The only free-text fields are the administrative question, the administrative reply and the patient-proposed demographic and coverage values. A clinical inquiry that carries text fails the schema.

**Idempotency.** `message_id` is a random UUID and is the idempotency key. No key carries PHI.

### 2. Entitlement and "active"

A linked patient is active when the registry record is active and, where the practice sells memberships, the membership is current (ruling 42). An active patient with no hold gets every capability in the link's scope. A hold withholds three capabilities: routine clinical messaging, new non-urgent scheduling and non-urgent medical-AI consults (rulings 41 and 52). "Non-urgent" means applied urgency `routine` only (DC-30). A patient who is not active keeps the three always-granted capabilities (design call DC-4). Ruling 50 also keeps routine clinical messaging and medical-AI consults open until the patient has been told and a grace period has passed, or while a clinician's continuity marker is valid. A grace period, like a hold countdown, starts only after the notice is delivered or its delivery is recorded (DC-41). An evaluation failure withholds everything except those five. While clinical routing is not enabled, routine clinical messaging is withheld with `clinical_routing_unavailable` (DC-33). A representative without billing scope sees `representative_scope`, never a hold (DC-34). The result carries codes only: no amount, balance or reason text.

### 3. Account and representative links

One gateway link joins one HealthLinc account to one openPractice patient. There is no master identity (ruling 39). openPractice stores the HealthLinc account triple with namespace `healthlinc` (design call DC-1). A link needs a verification record and a named human's tier-2 confirmation of the record that the practice's invitation or an exact match identifies. Possession of a device, phone number or invitation code is not verified identity. An agent may propose but never sign, because the evidence is untrusted content (AH-05). A representative link also needs an approved proxy policy (design call DC-12). A linked representative's `link.result` carries the relationship and the authority reference recorded at confirmation, and the entitlement assertion carries the same pair, verifiable (PG-06, PG-16; design call DC-49). The registry's guarantor relationship (PR-06) is a billing fact and never stands in for either.

### 4. Patient proposals

Patient-submitted demographic and coverage changes enter as proposals with patient provenance, become work items, and apply only after a named human accepts them. Accepted changes are ordinary registry changes under PR-04, PR-05, PR-07 and PR-10, and reach linked EHRs under EB-06 with source `patient_self_service`. A proposal that only adds confidential-communication restrictions is honored at once on an interim basis (design call DC-11).

### 5. Routing rules

The routing rules ship with openPractice as a fixed, versioned set. Practices configure only lanes, hours and the on-call roster (design call DC-17). The rules take the proposals of HealthLinc's coach, medXfactory's medical AI or HealthLinc's red-flag checklist (rulings 51 and 71), named by `proposal_source`, and apply four defaults: unknown category means clinical; unknown urgency means urgent; any clinical mark makes that part clinical; and the applied urgency is the highest proposal received from any source (design call DC-14). Urgency is never lowered (ER-03).

### 6. The clinical handoff contract: `openpractice-clinical-handoff/1.0`

The canonical schema is `contracts/openpractice-clinical-handoff-1.0.schema.json`, SHA-256 `2cc843f30c824bb48336d29090b15be0f6d12732cd1230a7dd0008e2d5e6021f` (it was `19dff20f95cd31afdef02270119a123a7c4662a4fcc5b20ef09d5b9cd5d91e2d` before the follow-up of 2026-11-15).

**Exactly what crosses (design call DC-2, reference-only).** Besides the envelope (contract, version, `message_id`, message type, issue time, issuer), a `handoff.offer` carries:

| Field | What it is |
|---|---|
| `handoff_id` | The openPractice routing id |
| `identity_state` | `linked`, `link_in_review` (identity review or a demographic hold) or `unlinked` (ER-06) |
| `link_id` | The EB-01 patient-link id; present for `linked` and `link_in_review`, absent for `unlinked` |
| `ehr_patient` | The EHR patient triple from that link; present for `linked` and `link_in_review` |
| `unlinked_identity` | For `unlinked` only: legal name, date of birth and administrative sex, a minimal subset of the `openpractice-ehr-patient-link/1.0` snapshot (SHA-256 `76f465e144893000de4bd2240adb9f547899e2e8100315f698c3cc6cb885b802`), with the same shapes |
| `healthlinc_message_ref` | HealthLinc's request reference (issuer, `healthlinc`, value) |
| `conversation_source` | `healthlinc` for a conversation HealthLinc holds (a coach conversation, a direct message to the doctor or an administrative thread), `medxfactory` for a medical-AI conversation (ruling 51) |
| `conversation_ref` | The conversation's reference in that system (issuer, `healthlinc` or `medxfactory`, value) |
| `reply_path_ref` | HealthLinc's opaque reply-path reference, passed through |
| `category` | Always `clinical` |
| `urgency` | `routine`, `priority` or `urgent` |
| `submitted_at`, `routed_at`, `ack_by`, `respond_by` | Times |
| `clock_policy` | Policy id, version, and `approved` or `fail_closed_default` |
| `origin` | `intake`, `recategorized_from_administrative` or `split_from_mixed` |
| `split_group_ref` | Optional opaque reference for a split question |

Nothing else crosses: no message body, question text, reply text or attachment. The schema has no free-text field, so it cannot carry one. The openPractice patient triple does not cross, because the `link_id` already names the link; for an `unlinked` item, only the `unlinked_identity` subset crosses. openChart fetches the conversation by reference from whichever system holds it, HealthLinc or medXfactory, through `openchart-conversation-retrieval/1.0`, which openChart owns in its change `define-clinical-message-inbox` (ruling 69) and which HealthLinc and medXfactory implement, and replies to HealthLinc over its own reply path (companion items).

| Message | Direction | Purpose |
|---|---|---|
| `handoff.offer` | openPractice → openChart | Reference-only clinical item |
| `handoff.ack` | openChart → openPractice | `accepted` (inbox entry, clock started) or `rejected` with a code |
| `handoff.urgency_raised` | openPractice → openChart | Raise only; no message lowers urgency. Source `coach_triage`, `medical_ai_triage`, `patient_checklist` (ruling 71), `staff`, `agent_proposal` or `routing_rule`; the two triage values replace `healthlinc_triage`, which ruling 51 split (DC-44) |
| `handoff.clinician_urgency_raised` | openChart → openPractice | A clinician's raise, with openChart's new `respond_by`; openPractice shadows it and runs the urgent path for a raise to `urgent` (ER-03, DC-42) |
| `handoff.status` | openChart → openPractice | `clinician_acknowledged` (once, at a clinician's first explicit acknowledgment; ER-19), `responded`, `clock_expired`, `returned_not_clinical`, `content_unavailable` or `closed` |
| `clinical_need.marker` | openChart → openPractice | A clinician's coded, attributed, time-boxed marker: `appointment_needed` or `messaging_continuity`, with origin `clinician_judgment` or `urgent_escalation` and the urgent routing id (ER-18). On the EB-01 link, with no handoff id |
| `message.ack` | either side | Acknowledges raises, status and markers |

**Identity state.** A handoff is offered as `linked` on an active EB-01 link that is not in identity review or under a demographic hold (design call DC-24). An urgent or priority item is offered even when the chart is uncertain: as `link_in_review` on a link in review or on hold (DC-40), or as `unlinked` with only legal name, date of birth and administrative sex when there is no chart link (DC-39). openChart takes those as unfiled items with the uncertainty shown, and the office manager owns the identity exception; an urgent item's fallback runs at once. A routine item on an uncertain link is not offered and becomes an office-manager exception. openPractice never guesses a chart.

### 7. Acknowledgment, clocks and alerts

openChart owns the queue and the clock (ruling 43). openPractice sets `ack_by` and a shadow `respond_by` from the routing-clock policy (design call DC-23), and alerts the office manager on a missing acknowledgment, a rejection, `clock_expired`, `content_unavailable`, or silence past `respond_by`. For a `priority` item, these events also send the tracked on-call alert, without the guidance instruction (design call DC-30). When a clinician raises urgency in openChart, openPractice shadows openChart's new `respond_by`, never later than its current shadow (DC-42). The medical director approves the policy. Until then the defaults apply (design call DC-3):

| Urgency | Acknowledge within | Respond within |
|---|---|---|
| `urgent` | 5 minutes | 60 minutes |
| `priority` | 15 minutes | 8 hours |
| `routine` | 60 minutes | 48 hours |

These are calendar-time alerting thresholds, chosen to alert early. They are not clinical standards.

A `clinician_acknowledged` status from openChart is recorded and passed to HealthLinc as `inquiry.status` (ER-19). It cancels, delays and closes nothing on this path: every alert, tracked alert and fallback still runs on its own trigger (design call DC-51).

### 8. The urgent fallback

An urgent item is never blocked, delayed or dropped. When a handoff fails in any way, or openChart reports `clock_expired`, or nothing arrives by `respond_by`, openPractice at once alerts the office manager, sends a tracked alert to the on-call clinician, tells HealthLinc to show its own emergency guidance, and keeps retrying. An overdue response uses cause `clinical_response_overdue`. A tracked alert goes through the mobile approver and a second channel set in planning; its delivery and acknowledgment are recorded; unacknowledged, it is re-sent and escalated to the medical director (design call DC-29). With nobody on call, the medical director gets the tracked alert instead (design call DC-27). A rejected urgent inquiry raises a PHI-free office-manager exception, and HealthLinc is told to show its guidance (DC-35). openPractice sends a code, never advice. No agent is on this path, so the pause switch cannot affect it.

### 9. Hermes and staff lanes

Hermes answers in two forms, both at tier 3 and both starting at `propose`. A **read-only answer** is one of the `patient_answer.*` action types that agent-operations 1.0 carries (ruling 46). Change A's AH-05 exception 1 governs it (ruling 45), and this change references that exception rather than restating it: the practice may switch an answer type on to `act_after_delay` only, the server assembles the reply from an approved template, and the reply carries the answer-type id and version. A **drafted reply**, `gateway.administrative_reply`, is derived from untrusted patient text, so it stays at `propose`: Hermes drafts and a human in the lane decides. Hermes never decides its own draft (change A WI-03, ER-14). The drafted reply needs a governed MINOR version of `openpractice-agent-operations` first (design call DC-22). Until then, an item no read-only answer fits goes to staff. Hermes screens each item for clinical content before it drafts or answers, and proposes a re-route on any hit. Under ruling 68, medXfactory's local policy keeps Hermes's administrative patient messages separate from clinical ones; they follow ruling 45's guardrails with no clinical review, and consent and data-sharing conditions still apply. openPractice checks the patient's consent to AI-authored administrative messaging itself, before it schedules an answer and again before it sends it, and cancels every pending answer on withdrawal (change A AH-06; ruling 97). Hermes works an item through change A's work-item operations (AO-13): reading the item's administrative text, proposing a clinical re-route or an urgency raise, releasing the item to a human owner, and cancelling its own pending answer.

Staff lanes come from a fixed topic table: front desk (scheduling, demographics, forms), biller (statements, payments, coverage) and office manager (records requests, general, anything unmapped). Staff answer only in their lane; the office manager's lane covers every administrative lane (design call DC-16). Nobody in openPractice answers a clinical item.

### 10. Re-routing, splitting and removal

When an administrative item turns out to be clinical, it is handed to openChart by HealthLinc's reference and its stored text is removed (design call DC-15). Copies already sent to the agent layer or its model provider are deleted under the AH-02 agreements. A mixed question is split by HealthLinc, at intake or on request. openPractice staff never see clinical content, so only openChart can return a clinical item as administrative. Audit entries reference stored text by id and never copy it, so removal does not touch the tamper-evident chain (PR-10).

### 11. The payment hold

A hold withholds routine clinical messaging, new non-urgent scheduling and non-urgent medical-AI consults only (rulings 41 and 52). Its scheduling clause waits for a ratified scheduling change. The countdown starts only after the `hold_countdown_started` notice is delivered, or its delivery through another permitted channel is recorded; without either, no hold takes effect (DC-41). A clinician's `appointment_needed` marker from openChart, or an urgent escalation verified by its routing id, means an appointment is never blocked (ER-18, DC-26, DC-32). No hold can be placed before the medical director approves a versioned, counsel-reviewed policy that states every required item (PH-03). Placing is tier 3 with a countdown; lifting is tier 2 (design call DC-5). The patient is told when the countdown starts, before the hold takes effect (design call DC-6). Disputed amounts, and amounts inside an open GFE dispute window, never count until the policy states their treatment (design call DC-8). A lapsed membership is not a hold (design call DC-9). A hold stalls no claim, so it needs no FD-5 deadline escape (PH-08). A hold lifts by itself when its basis ends (design call DC-7).

### 12. The mobile approver

A thin client on the same versioned API, covering approvals, administrative escalations in the user's lane, the controller's day-close signature, and alerts. It shows no clinical content. Notifications carry no PHI, deep links need re-authentication, nothing persists on the device beyond the session, and every sign-off needs re-authentication at signing (design call DC-19). A lost device is revoked at once through AM-05.

## Required companion changes (rulings 40, 47 to 49 and 93)

Ruling 40 required these companion changes. Rulings 47 to 49 asked for them to be drafted, and they are being drafted in their own repositories, not here. Ruling 93 corrects the medXfactory entry: agent operations belong to one medXfactory change and clinical AI answering to another, so the list has four entries. Each must cite this change's canonical schema digests.

1. **`opensoft/openChart-spec`, change `define-clinical-message-inbox`: a clinical message inbox.** It must:
   - receive `openpractice-clinical-handoff/1.0` offers, validated against the schema at digest `2cc843f30c824bb48336d29090b15be0f6d12732cd1230a7dd0008e2d5e6021f`;
   - answer each offer with `handoff.ack`. It must accept an urgent or priority offer whose `identity_state` is `link_in_review` or `unlinked` as an unfiled item with the identity uncertainty shown (its MI-18), never refuse it for that state, and never file it to a chart or create a link from it automatically. It refuses a routine offer on a link in review with `link_in_review`, and a routine offer on a revoked or unknown link. An urgent or priority offer marked `linked` that openChart finds revoked, unknown or mismatched is also accepted unfiled with the uncertainty shown (its MI-18), so it still reaches a clinician;
   - send `handoff.clinician_urgency_raised`, with the new `respond_by` and the clinician's reference, whenever a clinician raises an item's urgency, and never lower urgency;
   - send `handoff.status` `clinician_acknowledged` once, when the item's owner first explicitly acknowledges it, never for system acceptance or a delivered notification, never after `returned_not_clinical`, and with no clinician identity or note (ER-19);
   - own the queue and the response clock, and report `responded`, `clock_expired`, `returned_not_clinical`, `content_unavailable` and `closed`. It must report `clock_expired` when its clock runs out and `responded` as soon as a clinician replies, because openPractice treats an expired clock, or silence past `respond_by`, as `clinical_response_overdue` and runs the urgent fallback, including the tracked on-call alert (ER-09);
   - keep an expired item in its queue until a clinician responds, and show the on-call clinician who receives openPractice's alert the item by its routing id;
   - fetch the conversation by `conversation_ref` from whichever system holds it, HealthLinc or medXfactory, through `openchart-conversation-retrieval/1.0`, which openChart owns in its change `define-clinical-message-inbox` (ruling 69), and never through openPractice;
   - accept `patient_checklist` as a source of `handoff.urgency_raised` (ruling 71);
   - give the clinician a reply path to HealthLinc that bypasses openPractice;
   - apply `handoff.urgency_raised` and never lower urgency;
   - let a clinician record, attribute and withdraw a clinical-need marker, `appointment_needed` or `messaging_continuity`, with a `valid_until` time and an origin, and send it to openPractice as `clinical_need.marker` with no reason or note (ER-18). For an origin of `urgent_escalation`, it must carry the routing id of that urgent handoff;
   - honor confidential-communication instructions for every patient communication it originates (ruling 27, EB-06);
   - say how a clinician reads a routed message for a patient with no EB-01 link. Its MI-18 now answers this for urgent and priority items (Open Question 5).
2. **`opensoft/HealthLinc`, change `define-practice-gateway-integration`: the patient app side of the gateway.** HealthLinc is a private repository; this change cites it by repository and change id only. It must:
   - cover the three patient paths of ruling 51: HealthLinc's coach, "connect to my doctor" (through openPractice to openChart), and "connect to medXfactory's medical AI";
   - keep the coach to general information only, with the not-medical-advice statement in addition; anything individualized goes to medXfactory's medical AI or to the doctor (ruling 54);
   - send the category and urgency proposed by its coach, or passed on from medXfactory's medical AI, with the conservative defaults (uncertain category clinical, uncertain urgency urgent), a triage reference, the `proposal_source`, the `conversation_source` and the `conversation_ref`, and never send clinical text to openPractice;
   - when a patient writes straight to the doctor with no AI triage, ask a short red-flag checklist: any red flag proposes `urgent` and shows emergency guidance, no red flag `routine`, a skipped checklist `priority`; let the patient raise but never lower the result; send it with `proposal_source` `patient_checklist` (ruling 71);
   - implement `openchart-conversation-retrieval/1.0`, which openChart owns in its change `define-clinical-message-inbox` (ruling 69), for the conversations it holds;
   - split mixed questions at intake, and resubmit on `inquiry.recategorized`;
   - consume `entitlement.result` as given, never remove the three always-granted capabilities, gate its medXfactory medical-AI connection on `medical_ai_consult` while never gating its own coach, and never derive a restriction on another backend's records from the result;
   - link to each backend through that backend's own link contract, with no master identity, store (openPractice issuer, `openpractice`, value), and use verified identity;
   - consume `patient.notice`, show its approved template after sign-in, honor its `restriction_codes` for any push or text, and answer every notice with `notice.ack`, `delivered` with the time or `failed` with a coded reason;
   - support the agreement that counsel requires between the practice and the HealthLinc operator, without which openPractice refuses every message with `operator_agreement_unverified` (PG-15), and keep its in-app emergency guidance working while refused;
   - keep emergency guidance in the app, and show it on every submission, on every `capability_withheld`, on `guidance.instruct` (whether keyed by routing id or by a refused inquiry's `message_id`), when openPractice is unreachable, and when an urgent inquiry is rejected;
   - tell the patient when to expect a response, in wording the practice approves, decided in planning;
   - offer a "this did not answer my question" action and an "ask for a person" action, sent as an inquiry with `human_requested` set;
   - keep push text free of PHI, and show patients when an agent wrote a reply;
   - when it connects the patient to a relying party that must verify the entitlement, such as medXfactory's medical AI, ask for an assertion by naming that relying party as `assertion_audience`, with the relying party's nonce when it gives one, and pass the assertion on unchanged; never alter, re-sign or vouch for it (PG-16);
   - take a representative's relationship and authority reference only from openPractice's `link.result` or assertion, never from its own `link.request` (PG-06);
   - report `clinician_acknowledged` to the patient or to a relying party only when `inquiry.status` carries it, and never infer it (ER-19);
   - validate against the gateway schema at digest `dd6ea44d981feb59caa3a1399384ec3d4045b8715f4ecb3c39b15bc01efbec41`.
3. **`MedxSoft/medXfactory`, change `define-care-organization-hermes-openpractice-operations` (medXfactory PR #38): the agent side only.** It authorizes no clinical answering. It must:
   - identify each Hermes instance by an openWallet ID, as a roster entry with a sponsoring human (AM-04);
   - consume `openpractice-agent-operations`: the read-only answer types and the work-item operations in 1.0 (rulings 46 and 93; change A AO-13), and the MINOR version that adds `gateway.administrative_reply`, `payment_hold.place` and `payment_hold.lift` (DC-22);
   - rely on openPractice's consent check for its patient answers (change A AH-06; ruling 97);
   - honor the ladder from `propose`, AH-05 and its exception 1, the per-patient read scope (AO-03), the pause switch (AO-09), and the rate and blast-radius limits (AO-08);
   - meet AH-02 (BAAs with the operator and each model provider), AH-03 (minimum necessary) and AH-04 (no PHI in agent logs), and delete copies of misrouted text on request (ER-12);
   - keep Hermes's administrative patient messages separate from clinical ones in its local policy, applying ruling 45's guardrails with no clinical review, and honoring consent and data-sharing conditions (ruling 68).
4. **`MedxSoft/medXfactory`, change `define-patient-medical-ai-connection` (medXfactory PR #39): the medical AI.** This entry takes over what the earlier draft wrongly placed in the Hermes change (ruling 93). It must:
   - own clinical AI answering and its clinical-safety governance;
   - propose category and urgency with the conservative defaults (ruling 51), and reach openPractice only through HealthLinc's gateway, with `conversation_source` `medxfactory` (DC-37);
   - implement `openchart-conversation-retrieval/1.0`, which openChart owns in its change `define-clinical-message-inbox` (ruling 69), for medical-AI conversations;
   - send the medical AI's records to MedxEHR, which relays them to openChart with receipts; medXfactory never writes to openChart directly, and openPractice is never in that path (rulings 53 and 70);
   - verify the entitlement assertion of PG-16 itself before any non-urgent consult, treat a missing or unverifiable assertion as no non-urgent grant, and never withhold priority or urgent need.

   This change states nothing about medXfactory's internals.

## Design calls for owner review

Each item below goes beyond rulings 34 to 54. Each is proposed, not ruled. Where it fails closed, the binding text already does; otherwise the binding text cites the design call so nothing is silently frozen.

| # | Design call | Where |
|---|---|---|
| DC-1 | Namespace token `healthlinc` for HealthLinc account and message references. HealthLinc is a working name that needs clearance; a rename would not change the token within `1.x`. | PG-05, both schemas |
| DC-2 | Reference-only clinical handoff, with exactly the fields in Decision 6. openChart fetches the conversation through `openchart-conversation-retrieval/1.0` (ruling 69). | ER-05, handoff schema |
| DC-3 | Fail-closed default acknowledgment and response windows until the medical director approves a policy (Decision 7). | ER-08 |
| DC-4 | A linked patient who is not active, or whose status cannot be evaluated, still gets urgent escalation, billing contact and own-records access. This **extends ruling 34 for safety**. | PG-03, PG-04 |
| DC-5 | Placing a hold is tier 3 (countdown and cancel window); lifting is tier 2. | PH-04, T-3 |
| DC-6 | The patient is told when the countdown starts, before the hold takes effect. | PH-05 |
| DC-7 | A hold lifts automatically, at tier 2 with notice, when its balance is paid or resolved, a dispute leaves the criteria unmet, or its policy version is withdrawn or expires. | PH-09, T-6 |
| DC-8 | Until the policy says otherwise, an amount inside an open GFE dispute window counts as disputed. | PH-06 |
| DC-9 | A lapsed membership is not a payment hold; it affects access only through the entitlement check. Membership dues count toward a hold only if the policy includes them. | PH-07 |
| DC-10 | Ruling 42 in a practice that sells memberships but also serves non-members: this change reads "the membership must be current" as applying to patients whose access the entitlement rules make membership-based. Until those rules are ratified, a practice that sells memberships does not cut off non-members: a patient with no membership is treated as active on the registry condition alone. This is a design call, not part of ruling 50. | PG-04 |
| DC-11 | A patient proposal that only adds confidential-communication restrictions is honored at once, on an interim basis, without changing the registry record. | PG-08 |
| DC-12 | Representative links need a versioned proxy policy that the medical director approves after counsel's review. Before one exists, none can be created. | PG-06 |
| DC-13 | **Resolved by ruling 45.** The narrow exception for Hermes answers is now change A's AH-05 exception 1 (read-only answers), which binds there. This change references it in ER-10, ER-14 and PG-11 and does not restate its conditions. Ruling 68 adds that medXfactory's local policy keeps those administrative messages separate from clinical ones, with no clinical review, under consent and data-sharing conditions. | ER-10, T-7 |
| DC-14 | Urgency and clinical-category proposals from staff and agents are applied by rule (raise only; clinical wins), so a safety raise never waits for an approval. | ER-02, ER-12, T-6 |
| DC-15 | Administrative question and reply text is stored in openPractice under Principle I safeguards, referenced by id from audit entries, and removed when an item is found clinical. | PG-10, ER-12, T-5 |
| DC-16 | The office manager's lane covers every administrative lane. | ER-11 |
| DC-17 | The routing rules are fixed and versioned; practices configure only lanes, hours and the on-call roster. | ER-02, T-2 |
| DC-18 | HealthLinc shows the patient when an agent wrote a reply. Counsel confirms any disclosure duty. | PG-11 |
| DC-19 | The mobile approver keeps no PHI on the device beyond the session, and every sign-off needs re-authentication at signing. | MA-05, MA-07 |
| DC-20 | Gateway 1.0 carries no attachments, such as insurance-card images. | PG-09 |
| DC-21 | Handoff shape A (ordered features, `tasks.md` §2) for this change. Rulings 34 to 54 do not state a shape. | `tasks.md` §1.2 |
| DC-22 | The read-only answer types are in agent-operations 1.0 (ruling 46). Agents get no other gateway action type (`gateway.administrative_reply`, `payment_hold.place`, `payment_hold.lift`) until a governed MINOR version of `openpractice-agent-operations` carries it. Until then, an administrative item that no read-only answer fits goes to staff. | ER-10, PH-04 |
| DC-23 | openPractice keeps a shadow `respond_by` and treats silence past it as an expired clock. | ER-07 |
| DC-24 | A handoff is offered as `linked` only on an active EB-01 link that is not in identity review or under a demographic hold; a patient with several EHR links goes to the configured EHR, or else to an exception. DC-39 and DC-40 extend this for urgent and priority items. | ER-06 |
| DC-25 | A human-sent reply has no tier-3 countdown: the explicit send is the release decision. | PG-11, T-8 |
| DC-26 | A hold never cancels a booked appointment, and never blocks an appointment that a clinician marks as needed (ER-18) or one arising from an urgent escalation verified by its routing id. PH-01's scheduling clause waits for a ratified scheduling change. | PH-01 |
| DC-27 | Clinical routing cannot be enabled until the on-call roster covers every hour or names the fallback; with nobody on call, the medical director and office manager are alerted. | ER-13 |
| DC-28 | Hold, routing-clock, proxy and messaging-continuity policy approvals are never opted in for agent signing. | PH-03, ER-08, PG-06, PG-04 |
| DC-29 | Tracked on-call alerts go through the mobile approver and a second channel set in planning. Delivery and acknowledgment are recorded, and an unacknowledged alert is re-sent and escalated to the medical director. The alert-acknowledgment window and re-send interval default to 5 minutes each until the routing-clock policy states them. | ER-09, ER-13 |
| DC-30 | Under ruling 41, "non-urgent" means applied urgency `routine` only. Any inquiry above `routine` needs only `urgent_escalation`, and a `priority` item's overdue acknowledgment or response also sends the tracked on-call alert, without the guidance instruction. | PG-03, PG-10, ER-07, PH-01 |
| DC-31 | How ruling 50 is carried out: the grace period comes from a messaging-continuity policy that the medical director approves; until a version is approved, a deactivation or a lapse withholds nothing that ruling 50 protects; a clinician keeps access open with a `messaging_continuity` marker (ER-18); and the same safeguards cover `medical_ai_consult`, which ruling 52 gates like non-urgent clinical messaging. | PG-04 |
| DC-32 | The clinical-need marker: coded, attributed to openChart's clinician reference, time-boxed by `valid_until`, sent as `clinical_need.marker` on the EB-01 link. An urgent origin is honored only when openPractice's own routing record shows that routing id as urgent on that link. No openPractice user or agent can record one. | ER-18, PH-01, PG-04 |
| DC-33 | While clinical routing is missing or disabled, clinical and urgent inquiries are refused with `clinical_routing_unavailable`, HealthLinc is told to show its guidance for that inquiry, the office manager owns a PHI-free exception, and routine clinical messaging is withheld with that reason. Clinical and urgent intake cannot go live before clinical routing is enabled. | PG-10, ER-09, ER-13 |
| DC-34 | A representative whose proxy policy gives no billing access sees `representative_scope` in place of `payment_hold`, and no hold-policy reference. | PG-06 |
| DC-35 | An urgent inquiry rejected for a link or authorization reason, or sent on a `pending_review` link, raises a PHI-free office-manager exception, and openPractice sends `guidance.instruct` keyed by the inquiry's `message_id`. | ER-09 |
| DC-36 | HealthLinc's `human_requested` flag: a patient's request for a person, or "this did not answer my question", routes to staff and never to a read-only answer. | PG-10, ER-10 |
| DC-37 | The conversation source values `healthlinc` and `medxfactory`, and the namespace token `medxfactory` for medXfactory conversation references. A medical-AI escalation reaches openPractice through HealthLinc's gateway; there is no direct contract between medXfactory and openPractice in this change. | PG-10, ER-05, both schemas |
| DC-38 | The capability token `medical_ai_consult` for ruling 52's non-urgent medical-AI consults, granted within the link's scope like the other non-safety capabilities. | PG-03, schema |
| DC-39 | **For the owner.** An urgent or priority item for a patient with no chart link is still offered, as `unlinked`, with an `unlinked_identity` of only legal name, date of birth and administrative sex: a minimal subset of the `openpractice-ehr-patient-link/1.0` snapshot (SHA-256 `76f465e144893000de4bd2240adb9f547899e2e8100315f698c3cc6cb885b802`). openChart holds it unfiled and never files or links on it automatically. This sends demographics to openChart without a confirmed link, on the reasoning that a missed urgent message is the greater harm. | ER-05, ER-06, handoff schema |
| DC-40 | An urgent or priority item on a link in identity review or under a demographic hold is still offered, as `link_in_review`, while the urgent fallback (urgent) or the tracked on-call alert (priority) runs. A routine item there keeps the exception path, so `link_in_review` as a reject reason applies only to routine offers. | ER-06, ER-09 |
| DC-41 | A hold countdown or a ruling 50 grace period starts only on a `delivered` `notice.ack` or a recorded delivery through a channel the contact rules allow, and runs its full length from that delivery. With neither, nothing is withheld and the undelivered notice becomes work. `grace_ends_at` appears only on `access_grace_started`. | PH-05, PG-04, gateway schema |
| DC-42 | A clinician's raise to `urgent` runs the urgent path, including the guidance instruction on a missed deadline; a raise to `priority` gets the priority alerting. openPractice's shadow deadline is never extended by a raise. | ER-03 |
| DC-43 | The HealthLinc operator-agreement gate refuses every gateway message, entitlement and link requests included, until the agreement is verified; it sends nothing else to an unverified deployment, and relies on HealthLinc's in-app emergency guidance meanwhile. | PG-15 |
| DC-45 | `inquiry.submitted` carries a required `proposal_source` (`coach_triage`, `medical_ai_triage`, `patient_checklist`). `coach_triage` and `patient_checklist` require a HealthLinc-held conversation, `medical_ai_triage` a medXfactory one, and `patient_checklist` the clinical category. A patient's later raise is a resubmission of the same request, sent on to openChart with source `patient_checklist`. | PG-10, ER-02, ER-03, both schemas |
| DC-44 | `handoff.urgency_raised.source` uses `coach_triage` and `medical_ai_triage` in place of `healthlinc_triage`, which ruling 51 split. Contract 1.0 is unratified, so no published value is withdrawn. | ER-03, handoff schema |
| DC-46 | **PROPOSED (2026-11-15).** The verifiable entitlement is a separate `assertion` object in `entitlement.result`, so HealthLinc can pass it on without the withheld list. openPractice signs the RFC 8785 canonical JSON of the assertion without its `signature`, with an asymmetric scheme so a relying party can verify but not forge it; `key_ref` names the key. The scheme, key distribution and rotation, and any maximum lifetime below `valid_until`, are decided in planning. | PG-16, gateway schema |
| DC-47 | **PROPOSED (2026-11-15).** openPractice issues an assertion only for a relying party the practice has registered: a tier-2 action by a named human, after the agreement counsel requires with the relying party's operator is verified (PG-15's pattern). The assertion lists only the granted capabilities that relying party is registered to rely on, plus `urgent_escalation`, which keeps what it learns to the minimum necessary. | PG-16, governance tiers |
| DC-48 | **PROPOSED (2026-11-15).** Replay is stopped by a unique `assertion_id`, the audience, link and expiry binding, and an optional relying-party nonce echoed as `audience_nonce`. The relying party's checks are listed in PG-16. | PG-16, gateway schema |
| DC-49 | **PROPOSED (2026-11-15).** The representative's relationship and authority reference, already recorded on the representative link, travel in `link.result` for a linked representative and in the assertion; the authority reference is the one recorded when a named human confirmed the authority. The registry's guarantor relationship never stands in for them. | PG-06, PG-16, gateway schema |
| DC-50 | **PROPOSED (2026-11-15).** `clinician_acknowledged` means openChart's explicit acknowledgment act by the item's owner, a named clinician; it is sent once, at the first acknowledgment, never after `returned_not_clinical`, and carries no clinician identity. It is added to `handoff.status` rather than as a new message, because the status already reports the item's progress and needs `message.ack`. | ER-19, both schemas |
| DC-51 | **PROPOSED (2026-11-15).** `clinician_acknowledged` changes no alert, fallback, retry or clock in openPractice; every safety path still runs on its own trigger. Whether openPractice should also alert when an urgent item has no clinician acknowledgment by a deadline is Open Question 19. | ER-19, ER-07 to ER-09 |

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
| Send an agent-drafted reply, `gateway.administrative_reply` (ER-10) | 3 | At `propose`, a named human in the lane approves; never Hermes itself | `propose` (AH-05) |
| Send a read-only answer, `patient_answer.*` (ER-10; change A AH-05 exception 1) | 3 | At `propose`, a named human in the lane; at `act_after_delay`, none, and the lane may cancel in the window | `act_after_delay` only |
| Approve a read-only answer type or template version (change A AH-05 exception 1) | Approval (T-4) | Named human; anything health-adjacent also the medical director; never opted in | None |
| Place a hold, `payment_hold.place` (PH-04) | 3 (DC-5, T-3) | Named human, or an opted-in named agent | `act_after_delay` only; never `act_with_notice`, which would skip the countdown |
| Lift a hold, `payment_hold.lift` (PH-04) | 2 (DC-5) | Named human, or an opted-in named agent | `act_with_notice` or `act_after_delay` |
| Automatic lift when the basis ends (PH-09) | 2, automatic (DC-7, T-6) | No sign-off; notification | — |
| Approve a hold policy version (PH-03) | Approval (T-4) | Medical director only; counsel's review recorded; never opted in (DC-28) | None |
| Approve a routing-clock policy version (ER-08) | Approval (T-4) | Medical director only; never opted in (DC-28) | None |
| Approve a proxy policy version (PG-06) | Approval (T-4) | Medical director only; counsel's review recorded; never opted in (DC-12, DC-28) | None |
| Approve a messaging-continuity policy version (PG-04, ruling 50) | Approval (T-4) | Medical director only; never opted in (DC-28, DC-31) | None |
| Record or withdraw a clinical-need marker (ER-18) | Not an openPractice action | A clinician in openChart only | None |
| Acknowledge a tracked alert (ER-09) | Not an action type | The alerted clinician or medical director | None |
| Revoke a mobile device session (MA-06) | 2 | The user, the office manager, or AM-05 offboarding | — |
| Record and verify the HealthLinc operator agreement (PG-15) | Approval (T-4) | An authorized named human; counsel decides the form; never opted in | None |
| Record a notice delivery through another permitted channel (PH-05, PG-04) | 2 | A named human | `propose` |
| Register or remove a relying party for entitlement assertions (PG-16) | 2 | Practice manager, or controller or office manager; named human only; after the relying party's operator agreement is verified (DC-47) | None |
| Day-close signature of the daily agent digest (MA-02) | As AO-10 | The controller | As AO-10 |
| Any other sign-off on mobile (MA-07) | That action's tier | That action's signer; tier 4 human only; D8 accountant only | That action's ceiling |

Every sign-off records the signer's identity, including an agent's openWallet ID, with provenance (BAB-17).

**Deadline escapes (FD-5).** Every gate names its escape:
- The clinical handoff is not a gate. Its escape is the alert path and the urgent fallback (ER-07, ER-09).
- Link confirmation, representative confirmation, proposal review and Hermes draft approval stall no claim. Claims use the registry's current data (PG-09). Their escape is escalation through the work-item clock (WI-06), not release.
- A payment hold stalls no claim (PH-08), so it needs no escape and no exception.
- The hold, routing-clock, proxy and messaging-continuity policy approvals hold nothing deadline-bearing. Holds and representative links are refused meanwhile (fail closed), the default clock windows apply, and a deactivation or lapse withholds nothing that ruling 50 protects.

## Constitution Check (pre-handoff, against v1.0.0)

| Principle or gate | Obligation | Status |
|---|---|---|
| I. HIPAA by design | Administrative text encrypted, access-controlled and audited on every read (PG-10). No PHI in logs, errors, notifications, keys or diagnostics (PG-13, ER-16). Mobile idle logoff, no PHI on the device, re-authentication (MA-04, MA-05). No gateway traffic before the HealthLinc operator agreement is verified (PG-15). An unlinked offer carries only three snapshot fields (DC-39). An entitlement assertion carries no PHI and no master identity, and goes only to a registered relying party (PG-16, DC-47). Patient notices carry no amount or reason. | Requirement level; a safeguard plan is owed per feature |
| II. Security review | Every feature touches PHI or authorization. Automatic routing, the urgent fallback and automatic hold lifts are on by default (T-6). Hermes drafts stay at `propose`; read-only answers act only under change A's AH-05 exception 1 (T-7). | Owed per feature; T-6 and T-7 need the owner's decision |
| III. Test-first | Contract tests at both boundaries against the canonical schemas (PG-14, ER-17). Synthetic fixtures only. | Owed per feature |
| IV. Clinical boundary | Routing record only; no clinical text in openPractice; reference-only handoff (ER-01, ER-05). Medical-AI conversations are referenced, never held, and the medical AI's records reach openChart through MedxEHR, never through openPractice (rulings 53 and 70). The clinical-need marker is a code with no note (ER-18). Transient misfiled content is removed (T-5). | Satisfied, with T-5 recorded |
| V. Versioned contracts | Two contracts, each with one canonical schema and digest. Agents only through the agent-operations contract. No database access. | Satisfied |
| VI. Portability | HealthLinc and openChart are optional; openPractice runs without either (PG-02). | Satisfied |
| Data gate | Synthetic fixtures; no credentials. | Requirement level |

## Conflicts and tensions

These are conflicts with ratified text: FD-1 to FD-11, constitution v1.0.0, and the openPractice `AGENTS.md` line on autonomous actions. T-10 and T-12 also record where a later ruling changes an earlier ruling's wording, and T-11 a gap in sibling changes that needs a follow-up. Twelve are recorded. Two rest on owner amendments (T-1, T-2), and three are resolved by owner rulings (T-9, T-10, T-12); the other seven need the owner's disposition at ratification.

| # | Tension | Status |
|---|---|---|
| T-1 | **FD-10(b)** keeps the companion app deferred. This change brings the HealthLinc gateway contract and escalation routing into the MVP. | **Amended by ruling 36**, recorded on change A's branch. This change can be ratified only with or after that amendment. FD-10(b)'s no-account pay link is unchanged (PG-12). |
| T-2 | **FD-1** fixes a 2–4 in-house staff frame with "no routing engines, no approval ladders". This change adds routing rules and uses change A's ladder. | **Amended by ruling 38**, for the frame only. Residual: the routing rules are a routing engine in FD-1's words. Mitigated by a fixed, shipped rule set with no rule authoring (DC-17). Conscious exception proposed. The ladder is change A's conscious exception T-6 under ruling 37, because the amendment does not lift "no approval ladders". |
| T-3 | **FD-5** lists tier 3 for outbound actions. Placing a hold restricts access and is not itself outbound. | Design call DC-5 for owner review. Tier 3 chosen because a hold affects the patient and carries an outbound notice. |
| T-4 | **FD-5** replaces approval with reversibility below tier 4. This change keeps explicit sign-offs below tier 4: link and representative confirmation, proposal review, approval of Hermes drafts at `propose`, and the hold, routing-clock and proxy policy approvals. | **Conscious exception proposed**, like `define-mvp-practice-foundation` T-17. Signers follow BAB-17; AH-05 bars agents where the content is untrusted. |
| T-5 | **Principle IV**, read with ruling 34. When HealthLinc misfiles a clinical or mixed question as administrative, openPractice briefly holds clinical text until someone finds it. | Residual risk. Text is removed at recategorization (ER-12, DC-15); backups age out. Copies sent to the agent layer are deleted under the AH-02 agreements. HealthLinc splits at intake (companion). |
| T-6 | **Principle II and `AGENTS.md`**: no autonomous patient-impacting or financial action by default. This change turns on by default: rule-based routing and handoff; the urgent fallback, which tells HealthLinc to show guidance; automatic urgency raises and clinical recategorization from proposals (DC-14); and automatic hold lifts (DC-7). | **Conscious exception proposed for patient safety.** Routing and the fallback only deliver to humans or show the app's own guidance, and decide nothing about care. Hold lifts only restore access. Owner to confirm. |
| T-7 | **Principle II and `AGENTS.md`**, for Hermes answering patients. | Consistent by default: every answer type and every drafted reply starts at `propose` (ER-10). Ruling 45 lets the practice switch on read-only answer types at `act_after_delay` under change A's AH-05 exception 1; that per-type opt-in by a named human, after a named human approves the template, is the explicit human approval flow. Drafted replies never rise above `propose`. |
| T-8 | **FD-5 tier 3** runs a countdown before outbound actions. A staff member's reply is sent without a countdown. | Design call DC-25: the explicit human send is the release decision, as for batch release in `define-mvp-practice-foundation` (AI-06, T-17). |
| T-9 | **`AGENTS.md`** (no autonomous patient-impacting actions by default), read with the literal wording of rulings 34 and 42. As first drafted, a deactivation, a lapsed membership, an unresolved entitlement or an evaluation failure would automatically cut off routine clinical messaging, with none of ruling 41's safeguards. | **Resolved by ruling 50.** Cut-offs other than a hold now need patient notice and a policy-set grace period, a clinician can keep access open, and an evaluation failure never stops routine clinical messages (PG-04, DC-31). |
| T-10 | **Ruling 41** says a hold blocks non-urgent clinical messaging and new non-urgent scheduling "only". `medical_ai_consult` is a third capability a hold may withhold. | **Resolved by ruling 52**, which lets a hold withhold non-urgent medical-AI consults with the same safeguards. Urgent help, billing contact, own records and HealthLinc's coach are never withheld (PH-01, PH-02). |
| T-11 | **EB-05 and EB-08** of `define-mvp-practice-foundation`, and **PL-08** of openChart's `define-openpractice-boundary`, report a date-of-birth demographic hold with the same `identity_review` state as a linkage conflict. This change therefore cannot tell them apart and sends both as `link_in_review` (ER-06). | **Recorded; follow-up proposed.** This change edits neither. The proposed follow-up is a governed amendment to EB-05 and EB-08 and to PL-08 that distinguishes a demographic hold from a linkage conflict, after which `identity_state` can gain a separate value in a MINOR version. Until then, urgent and priority items are offered in either case. |
| T-12 | **Ruling 51** proposes an uncertain urgency as urgent. Under ruling 71, a skipped red-flag checklist proposes `priority`, not `urgent`. | **Resolved by ruling 71**, the later and more specific ruling for a message written straight to the doctor with no AI triage. A red flag still proposes `urgent`, and the patient may raise but never lower the result (ER-02). |

## Risks / Trade-offs

- **[A missed urgent message]** → Never blocked by status or hold; offered even on a link in review or with no chart link (DC-39, DC-40); a clinician's raise gets the urgent path (DC-42); acknowledgment windows; shadow deadlines; the fallback on an expired or silent clock too; tracked on-call alerts that are re-sent and escalated to the medical director; HealthLinc's guidance; retries; no agent on the path; clinical and urgent intake cannot go live without clinical routing.
- **[Clinical content leaks into openPractice]** → No text on clinical intake; a reference-only handoff with no free-text field; removal on recategorization (T-5).
- **[Wrong-patient link]** → Verified identity, exact match, human confirmation, no auto-linking, revocation, and an incident assessment.
- **[A hold harms care]** → Only three non-urgent capabilities; never urgent, billing or records; a fail-closed policy gate; counsel review; patient notice before effect; automatic lift.
- **[Agent overreach]** → Start at `propose`; the AH-05 cap, with read-only answers only under AH-05 exception 1; Hermes never decides its own draft; reads limited to the linked patient; no drafted-reply or hold action type before the agent-operations MINOR; the pause switch.
- **[A cut-off harms care]** → A hold withholds only three non-urgent capabilities; other cut-offs need notice and a grace period, and a clinician can keep access open (ruling 50).
- **[Prompt injection through a patient message]** → Patient text is data, never instructions (AH-05); Hermes answers wait for a human.
- **[Lost phone]** → No PHI on the device; immediate revocation through AM-05.
- **[Contract drift]** → Canonical schemas compared by digest with each companion (`tasks.md` §1.4).

## Migration Plan

1. Validate strictly. Ratify `define-mvp-practice-foundation` and change A, including the FD-1 and FD-10(b) amendments. The owner completes `owner-ratification.md`.
2. Feature 1: the gateway, tested against a HealthLinc double.
3. Feature 2: escalation routing and the clinical handoff, tested against openChart and HealthLinc doubles. Clinical routing is enabled only with full on-call cover. Clinical and urgent inquiry intake from Feature 1 goes live only after clinical routing is enabled.
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
5. **A clinician reading a routed message for a patient with no EB-01 link** (ER-06). Answered for urgent and priority items: ER-06 offers them with `identity_state` `unlinked` and `unlinked_identity` (DC-39, for the owner), and the openChart companion's MI-18 accepts them unfiled. Routine items keep the exception path.
6. **The on-call roster's source** and its relation to change A's role recipes (ER-13). Planning.
7. **Routing-clock windows** (ER-08). The medical director; defaults meanwhile.
8. **Ruling 42 in a mixed membership practice** (DC-10). The owner. Ruling 50 settled the messaging safeguards; DC-10's treatment of non-members is still a design call.
9. **AI-authorship disclosure** for agent replies (DC-18). Counsel.
10. **Retention of administrative communication records** (PG-10). Not covered by `add-reimbursement-retention-control`. The owner and counsel.
11. **The agent-operations MINOR** for `gateway.administrative_reply` and the hold action types (DC-22). Change A's owner. The read-only answer types are already in 1.0 (ruling 46).
12. **HealthLinc name clearance** and the `healthlinc` token (DC-1). The owner.
13. **The second channel for tracked alerts** and the alert-acknowledgment windows (DC-29). Planning, then the medical director's routing-clock policy.
14. **The scheduling change** that PH-01's scheduling clause waits for, and the messaging-continuity policy's grace period (DC-31). The owner and the medical director.
15. **The `medxfactory` namespace token** and medXfactory's issuer handling (DC-37). The owner, with the medXfactory companion.
16. **The HealthLinc operator agreement** that PG-15 requires: its form and counterparty. Counsel.
17. **Distinguishing a demographic hold from a linkage conflict** in EB-05, EB-08 and openChart's PL-08 (T-11). The owner, through a follow-up change.
18. **The assertion's signature scheme, key distribution and rotation, maximum lifetime, and relying-party registration** (DC-46 to DC-48). Planning, then the owner; counsel for the relying party's agreement (DC-47).
19. **An alert on a missing clinician acknowledgment** (DC-51). Whether openPractice should alert, or run the urgent fallback, when an urgent item has no `clinician_acknowledged` by a deadline. The owner. openChart's `define-clinical-message-inbox` records the gap as its tension MT-5.
20. **The PROPOSED calls of the 2026-11-15 follow-up** (DC-46 to DC-51). The owner.

## Follow-up of 2026-11-15 (ruling 93)

This change was merged to `main` as an unratified draft. On 2026-11-15 the owner accepted ruling 93, a draft follow-up that closes gaps its consumers found. It is edited in place, because nothing here is ratified, built or archived; the contracts keep their names at `1.0`, and the digests identify the bytes. `Status` stays `draft`, and `owner-ratification.md` stays PENDING.

| What changed | Why | Where |
|---|---|---|
| A verifiable entitlement assertion | A relying party other than the patient app could not verify that openPractice issued an entitlement (`opensoft/HealthLinc`, change `define-practice-gateway-integration`, finding X-11; `MedxSoft/medXfactory`, change `define-patient-medical-ai-connection`, finding P-1) | PG-16, PG-03, PG-14; gateway schema; DC-46 to DC-48 |
| The representative's relationship and authority reference | `link.result` and `entitlement.result` carried neither (HealthLinc finding X-9) | PG-06, PG-16; gateway schema; DC-49 |
| `clinician_acknowledged` in `handoff.status` and `inquiry.status` | No status said a clinician had picked an item up (HealthLinc finding X-10; medXfactory `define-patient-medical-ai-connection` finding P-4; openChart `define-clinical-message-inbox` tension MT-5) | ER-19, ER-01, ER-17, ER-07; both schemas; DC-50, DC-51 |
| Hermes's consent check and work-item operations, cited | Carried by change A (AH-06, AO-13) | ER-10, Decision 9 |
| Erratum: the medXfactory companion entry split in two | Clinical AI answering was attributed to the Hermes change (medXfactory `define-care-organization-hermes-openpractice-operations`, finding U-10) | "Required companion changes" items 3 and 4; `proposal.md`; `tasks.md` 1.4; `owner-ratification.md` |

| Contract | Digest before | Digest now |
|---|---|---|
| `openpractice-patient-gateway/1.0` | `f2596a9daa997d4d88142fd425bae4a6a0a70656856c6434de843553a2c8123f` | `dd6ea44d981feb59caa3a1399384ec3d4045b8715f4ecb3c39b15bc01efbec41` |
| `openpractice-clinical-handoff/1.0` | `19dff20f95cd31afdef02270119a123a7c4662a4fcc5b20ef09d5b9cd5d91e2d` | `2cc843f30c824bb48336d29090b15be0f6d12732cd1230a7dd0008e2d5e6021f` |

Every addition is optional or tied to a new value, so a message valid before stays valid, with one exception: the gateway schema now requires `representative` on a `link.result` that links a representative.
