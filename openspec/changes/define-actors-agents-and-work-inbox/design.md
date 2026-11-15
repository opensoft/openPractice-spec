## Context

Constitution v1.0.0 is in force. Its six principles are I (HIPAA by design), II (security review), III (test-first), IV (clinical boundary), V (versioned contracts) and VI (portability). The framing decisions FD-1 to FD-11 were ratified on 2026-08-27. openPractice's AGENTS.md says: "Do not enable autonomous financial or patient-impacting actions by default."

This change is stacked on `define-mvp-practice-foundation` (PR #2). It uses that change's registry (PR-01 to PR-14), EHR gates (EB-04, EB-05, EB-08, EB-09, EB-17, EB-18), accounting gates (AI-06 to AI-08, AI-14, AI-18), bank and route gates (BAB-12, BAB-13, BAB-16), the single recipient-profile shape (AI-23) and the signer rules (BAB-17). It redefines none of them. It also cites `define-customer-financial-data-boundaries` (PR #3, on branch `define-customer-financial-data-boundaries`, not yet on main).

The roster brainstorm, `ideation/brainstorm/openpractice-small-practice-beta-roster-access-and-offboarding.md`, is `Status: brainstorm`. This change adopts its seven recipes, its "owner is always a Roster entry" rule, its robots-on-the-staff-list model and its same-day offboarding switch, and turns them into requirements.

## Owner rulings (2026-11-14)

Rows 34 to 54 have the same source: **Brett Heap, 2026-11-14, authoring session.** Rows marked (2026-11-15) have the source **Brett Heap, 2026-11-15, accepting the recommendations on decisions 79 to 98 except 80**; each quotes the question and the accepted wording. **Rulings are not ratification.**

| # | Ruling | Governs here |
|---|---|---|
| 34 | openPractice is the gateway and router. An active patient who is not on payment hold gets access. Clinical questions go to openChart, the doctor's clinical interface, where the doctor reads and answers. Administrative questions are answered by Hermes or routed to the office manager or staff. openPractice keeps only a routing record (who, when, category, urgency, reference) and no clinical content (Principle IV). | Sibling change; AO-09 (urgent work never paused) |
| 35 | For now the physician uses openChart, on mobile or workstation. The openPractice app, on workstation and mobile, covers administrative work and approvals. This is a current-phase decision and can be revisited. | AM-02 (no clinical write authority in any recipe) |
| 36 | FD-10(b) is amended. The HealthLinc integration contract and escalation routing come into the MVP; HealthLinc's own app features stay on HealthLinc's roadmap. | FD amendments; T-2 |
| 37 | Autonomy ladder per action type: observe → propose → act with notice → act after a delay. Every action type starts at propose, and the practice opts each one up. Tier 4 stays human-only. The ladder reconciles with FD-5 and BAB-17, and an agent never acts with less friction than the action's FD-5 tier requires. | AO-03, AO-04, WI-03, WI-04; T-3 |
| 38 | FD-1 is amended. The agent-operated practice is the primary frame, so screens are built for exceptions and approvals. The 2–4 staff mode remains for when agents are off. | FD amendments; AO-09; T-1 |
| 39 | No master patient identity exists across HealthLinc, openPractice and openChart. HealthLinc links to each backend through that backend's own link contract. | Sibling change; AM-01 (patients are not roster entries) |
| 40 | Packaging: two new changes. The current four are ratified separately. HealthLinc, openChart and MedxFactory/Hermes each need a companion change, which is listed but not drafted. | `proposal.md` |
| 41 | A payment hold blocks only non-urgent clinical messaging and new non-urgent scheduling. It never blocks urgent escalations, billing contact or access to the patient's own records. Placing or lifting a hold is a logged, tiered action, and the patient is told. The medical director approves the hold policy, and the exact policy needs the practice's counsel. | Sibling change |
| 42 | "Active" means the registry record is active. Where the practice sells memberships, the membership must also be current, per `define-customer-financial-data-boundaries` (PR #3, not yet on main). | Sibling change |
| 43 | openChart owns the queue and response clock for routed clinical questions. openPractice gets an acknowledgment per handoff, and it alerts the office manager on a missing acknowledgment or an expired clock. | Sibling change |
| 44 | HealthLinc's AI proposes the category and urgency, and openPractice's routing rules apply them. Staff may re-route a misfiled item but may not answer outside their lane. A mixed question is split. openPractice never downgrades urgency. Amended by ruling 51. | Sibling change |
| 45 | Hermes may reply to a patient without a human only with read-only answers. Such an answer changes nothing and tells the signed-in patient only their own administrative information (appointment time, balance, coverage on file) or practice-approved information (hours, policies). During a conversation the agent can read only that one patient's data; each answer type is switched on by the practice; no clinical content, and anything uncertain goes to staff; the patient can always ask for a human. Anything that changes something stays a proposal or follows the ladder. | AH-05 exception 1; AO-03; AO-04; T-7 |
| 46 | The read-only answer types are in the MVP agent operations contract (1.0), alongside the registry operations. In the MVP, Hermes answers routine administrative questions. | AO-11; schema `readOnlyAnswerType`, `answerType`; `tasks.md` 2.3 |
| 47 | The openChart companion change is to be drafted: `define-clinical-message-inbox` in `opensoft/openChart-spec`. | `proposal.md` "Required companion changes" |
| 48 | The HealthLinc companion change is to be drafted: `define-practice-gateway-integration` in `opensoft/HealthLinc` (a private repository, cited by repository and change id only). | `proposal.md` "Required companion changes" |
| 49 | The medXfactory companion change is to be drafted: `define-care-organization-hermes-openpractice-operations` in `MedxSoft/medXfactory`. | `proposal.md` "Required companion changes" |
| 50 | Cut-offs other than a payment hold get safeguards too. If the access check is unavailable, routine clinical messages still route to openChart. A lapsed membership or a deactivation stops messaging only after the patient is told and a policy-set grace period has passed, and a clinician can keep access open for continuity of care. | Sibling change (boundary context) |
| 51 | HealthLinc's in-app AI is a coach, not a doctor: it gives general health information with a clear not-medical-advice statement. Clinical AI doctors live in `MedxSoft/medXfactory`, and HealthLinc talks to medXfactory. HealthLinc is a gateway to the patient's doctor: from it the patient connects to their human doctor (through openPractice to openChart) or to medXfactory's medical AI. Amends ruling 44: category and urgency may be proposed by HealthLinc's coach or by medXfactory's medical AI, both with conservative defaults (uncertain category is clinical, uncertain urgency is urgent, urgency is never lowered). | Sibling change (boundary context); `proposal.md` "Why" |
| 52 | Non-urgent medXfactory medical-AI consults sit behind the practice's access check. A payment hold may withhold them, with the same safeguards as non-urgent clinical messaging. Urgent is never withheld. HealthLinc's coach is HealthLinc's own and is never gated by the practice. | Sibling change (boundary context) |
| 53 | The medical AI's exchanges with a patient are recorded in the chart through medXfactory's own governed path (MedxEHR and openChart). They never pass through openPractice, which holds no clinical content. | Sibling change (boundary context); AH-03 |
| 54 | HealthLinc's coach gives general information only. Anything individualized is handed to medXfactory's medical AI or to the doctor. The content boundary is the rule; the not-medical-advice statement is required in addition. | Sibling change (boundary context) |
| 93 | (2026-11-15) Question: follow-up change to the merged openPractice contracts. Accepted: "Yes, one draft PR carrying: the agent operations Hermes lacks and the consent check; a verifiable entitlement; a clinician-acknowledged status; the representative's relationship and authority; the retraction reason code …" The list ends with an erratum to the gateway change's companion list: agent operations belong to medXfactory change `define-care-organization-hermes-openpractice-operations`, and clinical AI answering to medXfactory change `define-patient-medical-ai-connection`. | AO-04, AO-11, AO-12, AO-13 (work-item operations); WI-04; AH-05 exception 1 condition 11 and AH-06 (consent check); schema; "Follow-up of 2026-11-15" below |
| 97 | (2026-11-15) Question: does Hermes need each patient's consent before a model provider processes their administrative data? Accepted: "No; the provider agreement is enough for practice operations, with counsel to confirm. Consent applies to AI answering the patient directly." | AH-06 (consent gates only answers that reach the patient; AH-02's agreements cover provider processing for practice operations); D-23 |

## Goals / Non-Goals

**Goals:**
- Name every actor and record every action with its actor, actor type and principal.
- Let agents do registry work through the same governed API as people, never with less friction than the FD-5 tier.
- Keep one owned queue for everything that needs a person.
- Specify remote-VA and AI data-handling safeguards before any agent receives a grant at any rung.

**Non-Goals:**
- Agent operations beyond the patient registry in the MVP.
- Redefining any gate of `define-mvp-practice-foundation`.
- Claims about vendors, model providers, VA companies, openWallet or Hermes internals.

## Decisions

### 1. Actors and recipes

AM-01 declares the actor types. Staff, VAs, nurses, clinicians, the medical director, the controller or office manager, and AI agents are roster entries. Patients and representatives act only through the HealthLinc gateway and are never roster entries. System integrations are named integration identities, as PR-10 already allows.

AM-02 adds five recipes to the seven: remote VA, nurse, medical director, controller or office manager, and agent. A new recipe grants nothing until the owner approves its grant set, so access fails closed.

### 2. The autonomy ladder and FD-5

| Action | FD-5 friction | Highest agent rung (AO-04) | Who signs when the agent may not |
|---|---|---|---|
| Tier 1 (do + undo) | None | `act_with_notice` (stricter than FD-5: an agent never acts silently) | — |
| Tier 2 (do + notify) | Notification | `act_with_notice` | — |
| Tier 3 (delay + cancel window) | Delay with cancel | `act_after_delay`, with at least the tier's window | — |
| Sign-off gates (`define-mvp-practice-foundation` T-17) | Explicit sign-off | `act_with_notice`, which is the BAB-17 agent sign-off | Authorized human signer |
| Tier 4 (hard stop) | Human approval | `propose` | Named human only |
| D8 or estimator-version approval | Accountant approval | `propose` | The practice's accountant only |
| Derived from untrusted content (AH-05) | — | `propose`, except AH-05 exception 1 | Human signer |
| Read-only answer to the signed-in patient (AH-05 exception 1) | Tier 3 (outbound) | `act_after_delay` only, with a practice-set window the lane can cancel | Lane human, while the answer type is at `propose` |
| Work-item operation (AO-13): read the item's text, propose a clinical re-route or an urgency raise, release the item to a human, cancel a pending answer | — (none changes a record) | `propose`; a read needs only `observe` | — (the routing rules apply a raise; a release or a cancellation puts the item before a human) |

Raising an action type above `propose` is that action type's BAB-17 agent-signing opt-in; there is no second mechanism. Every grant, at any rung, needs the AH-01 gate. Grants, rungs, limits and the pause are changed only by named humans, and the decider of a proposal is never its proposer (AO-03, WI-03).

### 3. One inbox, many gates

WI-07 lists the gates of `define-mvp-practice-foundation` that appear as work items. Each gate's own requirement keeps its meaning, tier, signer and deadline escape (WI-06).

### 4. The agent operations contract

The canonical schema is `contracts/openpractice-agent-operations-1.0.schema.json`, SHA-256 `5b1572e3b8b31191de2ff3f3567cc03da02741e0637063bd71e8696659db813c` (it was `894fe93fa36024e2fe61481e09e47884838fd6b263ebee30c106dfb7a0813c92` before the follow-up of 2026-11-15). It defines requests, responses, autonomy settings, read-only answer types, the work-item operations and the daily digest. Payload content is validated against each registry operation's own schema (PR-09), which is defined in planning. The schema enforces these rules:
- A response that executes or schedules a tier-4 or D8 action is invalid. A hard delete's autonomy setting cannot rise above `propose`.
- `granted_by` and the digest signer are human actors; an agent or an integration identity is refused (AO-03).
- Every agent request names its work item, which binds its reads to one patient (AO-03).
- A response states openPractice's own untrusted determination. An untrusted action is never executed, and it is scheduled only as a read-only answer at `act_after_delay` (AH-05).
- A read-only answer request carries no payload, only the selected answer type. Its setting rises only to `act_after_delay`, with a cancel window. An answer type needs a human approver, a medical-director sign-off when health-adjacent, and topics that exclude records requests and demographics (AH-05 exception 1).
- A work-item operation (AO-13) names its work item, carries no payload and no answer type, and carries exactly the one field its operation needs: `proposed_category`, `proposed_urgency`, `release_reason` or `cancels_request_id`. Its outcome is `read_result`, `applied_by_rule`, `released` or `cancelled`, never `executed`, `scheduled` or `proposed`, and its setting never rises above `propose`. Only `work_item.read_text` returns data in this schema, as `item_text`, whose only kind in 1.0 is the administrative inquiry.
- A request refused because the patient's consent is not recorded or is held (AH-06) carries `consent_not_recorded`; a work-item operation on an item the agent does not own carries `not_item_owner`; a work-item operation past its AO-08 limit carries `rate_limited`, and its item passes to a human.

## Governance tiers (FD-5) for actions this change adds

| Action | Tier | Signer (BAB-17) |
|---|---|---|
| Add or change a roster entry, including an agent or VA | 2 | Practice manager; named human only |
| Grant or widen an agent's scope | 2, plus the AH-01 gate | Practice manager, or controller or office manager; named human only (AO-03) |
| Raise an action type's rung above `propose`, including switching on a read-only answer type | 2, plus the AH-01 gate | Practice manager, or controller or office manager; named human only (AO-03, AO-04) |
| Approve a read-only answer type or template version (AH-05 exception 1) | Approval | Named human; anything health-adjacent also needs the medical director; never opted in for agent signing |
| Revoke a grant, lower a rung, or offboard | 1 | Granting person, practice manager, or controller or office manager |
| Return an answer type to `propose` after an audit defect | Automatic (AH-05 exception 1) | No sign-off; recorded |
| Press the practice-wide pause | 1 | Practice manager, medical director, or controller or office manager |
| Lift the pause | 2 | Practice manager, or controller or office manager; named human only (AO-03, AO-09) |
| Lift an AH-01 suspension after re-verification | 2 | Named human only (AH-01) |
| Change rate or blast-radius limits | 2 | Practice manager, or controller or office manager; named human only (AO-03, AO-08) |
| Sign the daily agent digest | Day close | Controller or office manager; named human |
| Record a patient's consent to AI-authored administrative messaging (AH-06) | 2 | Named human only, on the patient's own instruction; never an agent, and never opted in for agent signing (D-22) |
| Record a withdrawal of that consent (AH-06) | 1 | Any staff member who receives the request of the patient, or of a representative whose authority on the link is confirmed; takes effect at once (D-28) |
| Record that consent stands after an agent's `ai_messaging_withdrawal_requested` release (AH-06) | 2 | Named human only, after checking with the patient; held answers restart their cancel windows (D-27) |
| Cancel every pending or held agent answer to a patient on withdrawal (AH-06) | Automatic (D-29) | No sign-off; recorded with no PHI |

**Deadline escapes (FD-5).** No gate this change adds stalls a claim. Work items that carry existing gates keep those gates' escapes unchanged (WI-06). An unsigned digest blocks nothing (AO-10).

## Constitution Check (against v1.0.0)

| Principle or rule | How this change meets it | Status |
|---|---|---|
| I. HIPAA by design | No agent receives a grant at any rung, and no PHI reaches an agent layer, without verified BAAs with the agent operator and each model provider and their recipient profiles (AH-01 to AH-03). A lapse suspends all agent PHI access. Agents read only their work item's patient (AO-03). There is no PHI in logs (AO-12, AH-04). A VA BAA is required (VA-01). | Requirement level |
| II. Security review | Every agent grant, every opt-in above `propose`, every read-only answer type and every VA company needs a security review and a risk-analysis entry. | Owed per feature |
| III. Test-first | Contract tests cover every operation, rung, cap, limit and the pause (AO-12). | Owed per feature |
| IV. Clinical boundary | No recipe grants clinical write authority. Recipient profiles admit no clinical content (AH-03). Reasoning artifacts hold no clinical content (AO-07). Read-only answers carry no clinical content (AH-05 exception 1). An agent reads only administrative text openPractice already holds; no clinical text exists to read (AO-13). | Satisfied |
| V. Versioned contracts | One canonical schema with a digest. The API is the only path (AO-02). | Satisfied |
| VI. Portability | The contract is vendor-neutral; openWallet is referenced by opaque ID. | Satisfied |
| AGENTS.md: no autonomous financial or patient-impacting actions by default | Every action type starts at `propose` (AO-04). A read-only answer type answers without a human only after a named human approves its template and switches it on (AH-05 exception 1). | Satisfied |

## Conflicts and tensions with ratified text

| # | Tension | Status |
|---|---|---|
| T-1 | **FD-1** fixes the frame at 2–4 in-house staff. This change makes the agent-operated practice primary. | **Amended by ruling 38** (FD amendments on this branch), for the frame only. The amendment does not lift FD-1's "no approval ladders, no RBAC matrix"; see T-6. Recipes still compose and never edit. |
| T-2 | **FD-10(b)** keeps the companion app deferred. | **Amended by ruling 36**: the HealthLinc integration contract and escalation routing are MVP scope, built in the sibling change. HealthLinc's own app features stay deferred. |
| T-3 | **FD-5** replaces approval with reversibility, visibility and delay, warning that "approval queues rot within a month". Here every agent action type starts at `propose`, so agent work waits for a person. | **Conscious exception** (ruling 37, AGENTS.md). The practice opts action types up within their FD-5 caps. |
| T-4 | **FD-5** tier 1 is "do + undo, silent". An agent here never acts silently: its highest rung on tier-1 actions is `act_with_notice`. | **Stricter than FD-5** by design call D-7; recorded for owner review. |
| T-5 | **FD-5** has no daily sign-off. Here the controller signs a daily agent digest (AO-10). | **Recorded exception.** An unsigned digest blocks nothing. |
| T-6 | **FD-1** rejects "approval ladders" and an "RBAC matrix" as configuration a small practice never articulated. This change adds the autonomy ladder (AO-04) and per-agent, per-action-type grants (AO-03). Ruling 38's amendment does not lift that text. | **Conscious exception remaining under ruling 37.** Mitigated: the ladder applies to agents only, every action type starts at `propose`, recipes for people still compose and never edit, and the caps are fixed by the FD-5 tiers rather than authored by the practice. Owner to confirm at ratification. |
| T-7 | **Design call D-12** caps any action that untrusted content contributed to at `propose`. Rulings 34 and 45 have Hermes answer patients' administrative questions without a human. | **Narrow exception by ruling 45**, binding as AH-05 exception 1. D-12 stands for everything else. AGENTS.md is still met, because no answer type acts until a named human approves it and switches it on. |

The roster brainstorm, which is not ratified, ships "no permissions screen" and only seven recipes. This change adds five recipes and per-agent grants. That is a departure from brainstorm text, not from ratified text.

## Design calls for owner review (beyond rulings 34 to 54)

| # | Call | Where |
|---|---|---|
| D-1 | A new recipe grants nothing until the owner approves its grant set (fail closed). | AM-02 |
| D-2 | Patients and representatives are not roster entries; system integrations are named integration identities. | AM-01 |
| D-3 | An agent drops to `observe` when its sponsor is inactive, and an agent cannot sponsor an agent. | AM-04 |
| D-4 | The signers for granting scopes, raising rungs, changing limits and lifting the pause are the practice manager or the controller or office manager, at tier 2. That only a named human may make these changes is binding (AO-03), not a design call. | Tier table, AO-03 |
| D-5 | Raising a rung above `propose` is the BAB-17 opt-in, so there is one mechanism, not two. | AO-04 |
| D-6 | The default owner is the controller or office manager. | AM-06, WI-02 |
| D-7 | On tier-1 and tier-2 actions the highest rung is `act_with_notice`; agents never act silently (T-4). | AO-04 |
| D-8 | An unsigned digest blocks nothing. The alternative is to demote every rung to `propose` after N unsigned days. | AO-10 |
| D-9 | Rate and blast-radius values are set in planning and fail closed. | AO-08 |
| D-10 | A concurrent VA session from two devices ends both sessions. Screen capture is a residual risk recorded in the risk analysis. | VA-02, VA-05 |
| D-11 | The agent's PHI is defined by AI-23 recipient profiles, one for the agent operator and one for each model provider. Clinical content is excluded. | AH-03 |
| D-12 | Any untrusted contribution caps the whole action at `propose`, except AH-05 exception 1 (ruling 45; T-7). | AH-05 |
| D-13 | Contract 1.0 offers hard delete only as a proposal. | AO-11 |
| D-14 | Read-only answers go only to a patient asking on the patient's own link. A representative's question goes to staff. This reads ruling 45's "the signed-in patient ... their own" strictly. | AH-05 exception 1 |
| D-15 | Contract 1.0 has four read-only answer families: coverage on file, balance, appointment time and practice information. Each is available only once the data it reads exists under a ratified change, so appointment times wait for a scheduling change. Answer-type topics exclude records requests and demographics. | AO-11, schema |
| D-16 | An AH-01 lapse suspends every grant and all PHI access on that path. Only a named human's tier-2 action lifts the suspension, after re-verification, and held delayed actions restart their windows. | AH-01 |
| D-17 | An agent's search returns only the exact identity match for its work item's target patient, never a candidate list. | AO-03 |
| D-18 | **PROPOSED (2026-11-15).** `work_item.read_text` returns only the administrative question text that the sibling change's PG-10 stores. A clinical inquiry, text removed on recategorization, and the document behind a registry item have no text in openPractice, and are refused with `text_not_held`; a document text kind needs a governed change and a MINOR version. A read needs no rung above `observe`, and its setting is capped at `propose`. Reading text is carried because a consumer finding asks for it (`MedxSoft/medXfactory`, change `define-care-organization-hermes-openpractice-operations`, finding U-11); the owner was shown the other four operations by name, so this one needs his confirmation. | AO-13, schema `itemText` |
| D-19 | **PROPOSED (2026-11-15).** An agent's clinical re-route and urgency raise are always proposals, at `propose` with no higher rung, and openPractice's fixed routing rules apply them at once, raise-only, as the sibling's ER-02, ER-12 and DC-14 already provide for any agent proposal. The outcome is `applied_by_rule`. The more conservative alternative, a human decision on each raise, was rejected because the sibling's rulings make a safety raise never wait for an approval. | AO-13 |
| D-20 | **PROPOSED (2026-11-15).** Releasing an item to a human and cancelling the agent's own pending answer take effect at once at `propose`, because each only puts the item before a human; neither has a rung above `propose`. After a cancellation the item passes to its lane's human owner. | AO-13, WI-04 |
| D-21 | **PROPOSED (2026-11-15).** The release reasons are WI-04's (`exceeds_rung_or_cap`, `untrusted_content`, `limit_reached`, `cannot_complete`) plus `patient_asked_for_person` (ruling 45) and `ai_messaging_withdrawal_requested` (D-27). A cancellation names the `request_id` that scheduled the answer, and an agent may cancel only its own pending answer on an item it still owns. | AO-13, schema `releaseReason` |
| D-22 | **PROPOSED (2026-11-15).** The consent record lives with the patient's administrative record in openPractice; its default is no consent (fail closed); only a named human records a grant, at tier 2, on the patient's own instruction; any staff member records a withdrawal, at tier 1, effective at once; a representative cannot grant it. The check runs before scheduling and immediately before every relay, and relays and withdrawals are serialized per patient, so a withdrawal recorded first wins. | AH-06 |
| D-23 | **PROPOSED (2026-11-15).** The consent check covers every agent-authored reply that reaches a patient, including, once a MINOR carries it, a human-approved agent draft. It never gates provider processing for practice operations (ruling 97). | AH-06 |
| D-24 | **PROPOSED (2026-11-15).** The work-item operations get their own outcomes (`read_result`, `applied_by_rule`, `released`, `cancelled`) instead of `executed`, so the rule that an action untrusted content contributed to is never `executed` stays unchanged. | Schema `response` |
| D-25 | **PROPOSED (fix round 1).** At an AO-08 limit, a work-item operation cannot "become a proposal", since it already is one and the rules apply re-routes and raises at once. So further requests of that kind are refused with `rate_limited`, and the item passes to its lane's human owner, who may still re-route or raise it; the usual alert goes to the sponsor and the controller. A re-route removes the item's stored text, which cannot be undone (ER-12 of the sibling change), so a runaway agent must not be able to repeat it. The released item shows its human owner the refused proposal (category or urgency), and when the refused proposal was `urgent` the release alert goes to the owner at urgent priority, so a truly urgent item caught at the limit is not lost; the automatic application stays refused, so nothing floods the clinical queue or starts patient guidance without a human (confirmation pass). The limit values are policy, set in planning with the others (D-9). | AO-08, AO-13 |
| D-26 | **PROPOSED (fix round 1).** Every work-item operation is refused with `not_item_owner` unless the agent is the item's current owner. This also closes reading text during a pause or a suspension, when the items have passed to humans. | AO-13, schema |
| D-27 | **PROPOSED (fix round 1).** A patient who tells an agent to stop AI-authored answers is heard: the agent releases the item with `ai_messaging_withdrawal_requested`, and openPractice holds every agent answer to that patient until a staff member records the withdrawal or records that consent stands. Untrusted text can therefore only stop answers, never start them. | AH-06, AO-13 |
| D-28 | **PROPOSED (fix round 1).** A representative whose authority on the link is confirmed may withdraw the patient's consent, never grant it. | AH-06 |
| D-29 | **PROPOSED (fix round 1), under ruling 93.** The ruling names the consent check, not the cancellation. Cancelling every pending or held agent answer to the patient at once on withdrawal is this change's way of carrying the check out. | AH-06 |

## Risks / Trade-offs

- **[Prompt injection through patient or fax content]** → openPractice derives untrusted contribution from provenance, so an agent cannot under-report it. AH-05 caps the action at `propose`. The read-only exception lets text only select an approved answer type; the server assembles the reply, and the agent writes no free text.
- **[Privilege escalation]** → An agent holds only the agent recipe. Grants, rungs, limits and the pause are named-human-only, and the schema refuses an agent as `granted_by`. The decider is never the proposer.
- **[Cross-patient disclosure]** → Every agent request names its work item, which binds reads to one patient (AO-03).
- **[Runaway agent]** → Limits (AO-08), the pause (AO-09), the daily digest (AO-10) and same-day offboarding (AM-05). At a limit, a work-item operation is refused and its item released to a human, so a runaway agent cannot keep re-routing items or raising their urgency (D-25).
- **[Prompt injection through text an agent reads]** → The text is data, never instructions (AH-05). The work-item operations it could steer only raise care (a clinical re-route or an urgency raise, applied by rule), or put an item before a human (a release or a cancellation); none changes a record or sends anything (AO-13).
- **[An answer after consent is withdrawn]** → openPractice checks consent before scheduling and again before sending, and cancels every pending answer on withdrawal (AH-06).
- **[Approval fatigue from propose-first]** → The practice opts action types up within their caps (T-3).
- **[VA data exfiltration]** → No export, masked fields, device rules and per-VA accountability. Screen capture remains a residual risk.
- **[Operator or provider retention of PHI]** → Governed by the operator and provider agreements (AH-02) and recorded in the risk analysis.

## Migration Plan

1. Validate strictly. The owner completes `owner-ratification.md`.
2. Build the actor model, the agent recipe, offboarding and the inbox with `define-mvp-practice-foundation` Feature 1 (registry).
3. Before any agent grant at any rung: verify the operator's and each provider's agreements, record the recipient profiles, and complete a security review (AH-01).
4. Build the agent operations contract for registry operations and the read-only answer types. Agents start at `propose`.
5. Before any VA works: verify the VA company's BAA (VA-01).
6. Each later feature adds its operations as a MINOR version.

## Open Questions

1. The grant sets of the five new recipes (D-1). This blocks those recipes.
2. The rate and blast-radius values (D-9). This blocks agent opt-in above `propose`.
3. The openWallet identity verification mechanics, decided in planning with the companion change.
4. Whether an unsigned digest should eventually demote rungs (D-8).
5. Each practice's read-only answer types: templates, allowlisted fields, topics, cancel windows and the sampled-audit rate (AH-05 exception 1). This blocks switching any answer type on.
6. **A request mode below the rung the practice has set.** Should an agent be able to ask openPractice to treat one request at a lower rung than the one the practice set for that action type? A consumer asks for one (`MedxSoft/medXfactory`, change `define-care-organization-hermes-openpractice-operations`, finding U-11). Ruling 93 did not include it, and this change does not add it. The owner decides whether to. Its absence blocks nothing in openPractice, which always applies the rung the practice set (AO-04).
7. **Reading item text (D-18).** Whether to keep `work_item.read_text`, which ruling 93's list carries only by reference to a consumer finding, and which the owner was not shown by name. It blocks agent work on any item whose text the agent must read.
8. **The work-item placements, codes and limits (D-19 to D-21, D-24 to D-26).** The owner confirms or changes them; the limit values are set in planning. They block nothing until an agent is granted the operations.
9. **The consent record (D-22, D-23, D-27 to D-29):** where it is kept, its default, who records it, the hold after an agent hears a withdrawal, a representative's withdrawal, the cancellation on withdrawal, whether a human-approved agent draft needs consent, and whether HealthLinc should carry a patient's own grant or withdrawal through the gateway. Counsel confirms its form. It blocks switching on any read-only answer type.

## Follow-up of 2026-11-15 (ruling 93)

This change was merged to `main` as an unratified draft. On 2026-11-15 the owner accepted ruling 93, a draft follow-up that closes gaps its consumers found. It is edited in place, because nothing here is ratified, built or archived, and the contract keeps the name `openpractice-agent-operations/1.0`; the digest identifies the bytes. `Status` stays `draft`, and `owner-ratification.md` stays PENDING.

| What changed | Why | Where |
|---|---|---|
| Five work-item operations: read the item's text, propose a clinical re-route, propose an urgency raise, release the item to a human, cancel a pending answer | The agent layer had no contract path for these (`MedxSoft/medXfactory`, change `define-care-organization-hermes-openpractice-operations`, finding U-11); ruling 93 | AO-13; AO-04, AO-11, AO-12; WI-04; schema; D-18 to D-21, D-24 |
| A consent check for agent answers, inside openPractice | The consent condition of ruling 68 named no check (same change, finding U-12); rulings 93 and 97 | AH-06; AH-05 exception 1 condition 11; governance tiers; D-22, D-23 |
| Not added: a request mode below the rung the practice has set | A consumer asks for it (same change, finding U-11); ruling 93 did not include it | Open Question 6 |
| Fix round 1 of the pull request review | Rate limits for the work-item operations; ownership; a withdrawal heard by the agent or given by a representative; the check before every relay; the cancellation on withdrawal marked as a design call | AO-08, AO-13, AH-06; D-25 to D-29 |
| Confirmation pass of the review | An urgency raise refused at a limit was passed on unchanged, so a truly urgent item could sit at its old urgency | AO-08 (the refused proposal shown, an urgent-priority release alert), AO-12; D-25 |

Contract digest: `openpractice-agent-operations/1.0` moved from `894fe93fa36024e2fe61481e09e47884838fd6b263ebee30c106dfb7a0813c92` to `5b1572e3b8b31191de2ff3f3567cc03da02741e0637063bd71e8696659db813c`. The new action types, outcomes, request fields, refusal reasons and digest counts are additive; nothing earlier was removed or renamed.
