## Context

Constitution v1.0.0 is in force. Its six principles are I (HIPAA by design), II (security review), III (test-first), IV (clinical boundary), V (versioned contracts) and VI (portability). The framing decisions FD-1 to FD-11 were ratified on 2026-08-27. openPractice's AGENTS.md says: "Do not enable autonomous financial or patient-impacting actions by default."

This change is stacked on `define-mvp-practice-foundation` (PR #2). It uses that change's registry (PR-01 to PR-14), EHR gates (EB-04, EB-05, EB-08, EB-09, EB-17, EB-18), accounting gates (AI-06 to AI-08, AI-14, AI-18), bank and route gates (BAB-12, BAB-13, BAB-16), the single recipient-profile shape (AI-23) and the signer rules (BAB-17). It redefines none of them. It also cites `define-customer-financial-data-boundaries` (PR #3, on branch `define-customer-financial-data-boundaries`, not yet on main).

The roster brainstorm, `ideation/brainstorm/openpractice-small-practice-beta-roster-access-and-offboarding.md`, is `Status: brainstorm`. This change adopts its seven recipes, its "owner is always a Roster entry" rule, its robots-on-the-staff-list model and its same-day offboarding switch, and turns them into requirements.

## Owner rulings (2026-11-14)

Every row has the same source: **Brett Heap, 2026-11-14, authoring session.** **Rulings are not ratification.**

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
| 44 | HealthLinc's AI proposes the category and urgency, and openPractice's routing rules apply them. Staff may re-route a misfiled item but may not answer outside their lane. A mixed question is split. openPractice never downgrades urgency. | Sibling change |

## Goals / Non-Goals

**Goals:**
- Name every actor and record every action with its actor, actor type and principal.
- Let agents do registry work through the same governed API as people, never with less friction than the FD-5 tier.
- Keep one owned queue for everything that needs a person.
- Specify remote-VA and AI data-handling safeguards before any agent is opted above `propose`.

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
| Derived from untrusted content (AH-05) | — | `propose` | Human signer |

Raising an action type above `propose` is that action type's BAB-17 agent-signing opt-in; there is no second mechanism. Every opt-in needs the AH-01 gate.

### 3. One inbox, many gates

WI-07 lists the gates of `define-mvp-practice-foundation` that appear as work items. Each gate's own requirement keeps its meaning, tier, signer and deadline escape (WI-06).

### 4. The agent operations contract

The canonical schema is `contracts/openpractice-agent-operations-1.0.schema.json`, SHA-256 `cf49f1ce20a9154d493cad0c225ec973a2e23f0fbf7527beeb2e14bffdccb46b`. It defines requests, responses, autonomy settings and the daily digest. Payload content is validated against each registry operation's own schema (PR-09), which is defined in planning. A response that executes or schedules a tier-4 or D8 action is invalid by schema. A hard delete's autonomy setting cannot rise above `propose`.

## Governance tiers (FD-5) for actions this change adds

| Action | Tier | Signer (BAB-17) |
|---|---|---|
| Add or change a roster entry, including an agent or VA | 2 | Practice manager; named human only (proposed) |
| Grant or widen an agent's scope | 2 | Practice manager, or controller or office manager; named human only (proposed) |
| Raise an action type's rung above `propose` | 2, plus the AH-01 gate | Practice manager, or controller or office manager; named human only (proposed) |
| Revoke a grant, lower a rung, or offboard | 1 | Granting person, practice manager, or controller or office manager |
| Press the practice-wide pause | 1 | Practice manager, medical director, or controller or office manager |
| Lift the pause | 2 | Practice manager, or controller or office manager; named human only (proposed) |
| Change rate or blast-radius limits | 2 | Practice manager, or controller or office manager |
| Sign the daily agent digest | Day close | Controller or office manager |

**Deadline escapes (FD-5).** No gate this change adds stalls a claim. Work items that carry existing gates keep those gates' escapes unchanged (WI-06). An unsigned digest blocks nothing (AO-10).

## Constitution Check (against v1.0.0)

| Principle or rule | How this change meets it | Status |
|---|---|---|
| I. HIPAA by design | No PHI reaches an agent layer without a verified agreement and a recipient profile (AH-02, AH-03). There is no PHI in logs (AO-12, AH-04). A VA BAA is required (VA-01). | Requirement level |
| II. Security review | Every opt-in above `propose`, and every VA company, needs a security review and a risk-analysis entry. | Owed per feature |
| III. Test-first | Contract tests cover every operation, rung, cap, limit and the pause (AO-12). | Owed per feature |
| IV. Clinical boundary | No recipe grants clinical write authority. Recipient profiles admit no clinical content (AH-03). | Satisfied |
| V. Versioned contracts | One canonical schema with a digest. The API is the only path (AO-02). | Satisfied |
| VI. Portability | The contract is vendor-neutral; openWallet is referenced by opaque ID. | Satisfied |
| AGENTS.md: no autonomous financial or patient-impacting actions by default | Every action type starts at `propose` (AO-04). | Satisfied |

## Conflicts and tensions with ratified text

| # | Tension | Status |
|---|---|---|
| T-1 | **FD-1** fixes the frame at 2–4 in-house staff and rejects "approval ladders" and an "RBAC matrix". This change makes the agent-operated practice primary and adds per-action-type scopes and a ladder. | **Amended by ruling 38** (FD amendments on this branch). The ladder comes from ruling 37. Recipes still compose and never edit. |
| T-2 | **FD-10(b)** keeps the companion app deferred. | **Amended by ruling 36**: the HealthLinc integration contract and escalation routing are MVP scope, built in the sibling change. HealthLinc's own app features stay deferred. |
| T-3 | **FD-5** replaces approval with reversibility, visibility and delay, warning that "approval queues rot within a month". Here every agent action type starts at `propose`, so agent work waits for a person. | **Conscious exception** (ruling 37, AGENTS.md). The practice opts action types up within their FD-5 caps. |
| T-4 | **FD-5** tier 1 is "do + undo, silent". An agent here never acts silently: its highest rung on tier-1 actions is `act_with_notice`. | **Stricter than FD-5** by design call D-7; recorded for owner review. |
| T-5 | **FD-5** has no daily sign-off. Here the controller signs a daily agent digest (AO-10). | **Recorded exception.** An unsigned digest blocks nothing. |

The roster brainstorm, which is not ratified, ships "no permissions screen" and only seven recipes. This change adds five recipes and per-agent grants. That is a departure from brainstorm text, not from ratified text.

## Design calls for owner review (beyond rulings 34 to 44)

| # | Call | Where |
|---|---|---|
| D-1 | A new recipe grants nothing until the owner approves its grant set (fail closed). | AM-02 |
| D-2 | Patients and representatives are not roster entries; system integrations are named integration identities. | AM-01 |
| D-3 | An agent drops to `observe` when its sponsor is inactive, and an agent cannot sponsor an agent. | AM-04 |
| D-4 | Granting scopes, raising rungs and lifting the pause are named-human-only tier-2 actions by the practice manager or the controller or office manager. | Tier table |
| D-5 | Raising a rung above `propose` is the BAB-17 opt-in, so there is one mechanism, not two. | AO-04 |
| D-6 | The default owner is the controller or office manager. | AM-06, WI-02 |
| D-7 | On tier-1 and tier-2 actions the highest rung is `act_with_notice`; agents never act silently (T-4). | AO-04 |
| D-8 | An unsigned digest blocks nothing. The alternative is to demote every rung to `propose` after N unsigned days. | AO-10 |
| D-9 | Rate and blast-radius values are set in planning and fail closed. | AO-08 |
| D-10 | A concurrent VA session from two devices ends both sessions. Screen capture is a residual risk recorded in the risk analysis. | VA-02, VA-05 |
| D-11 | The agent's PHI is defined by an AI-23 recipient profile, with the provider as recipient. Clinical content is excluded. | AH-03 |
| D-12 | Any untrusted contribution caps the whole action at `propose`. | AH-05 |
| D-13 | Contract 1.0 offers hard delete only as a proposal. | AO-11 |

## Risks / Trade-offs

- **[Prompt injection through patient or fax content]** → AH-05 caps the action at `propose`, and untrusted contribution is recorded.
- **[Runaway agent]** → Limits (AO-08), the pause (AO-09), the daily digest (AO-10) and same-day offboarding (AM-05).
- **[Approval fatigue from propose-first]** → The practice opts action types up within their caps (T-3).
- **[VA data exfiltration]** → No export, masked fields, device rules and per-VA accountability. Screen capture remains a residual risk.
- **[Provider retention of PHI]** → Governed by the per-provider agreement (AH-02) and recorded in the risk analysis.

## Migration Plan

1. Validate strictly. The owner completes `owner-ratification.md`.
2. Build the actor model, the agent recipe, offboarding and the inbox with `define-mvp-practice-foundation` Feature 1 (registry).
3. Build the agent operations contract for registry operations. Agents start at `propose`.
4. Before any opt-in above `propose`: verify a provider agreement, record a recipient profile, and complete a security review (AH-01).
5. Before any VA works: verify the VA company's BAA (VA-01).
6. Each later feature adds its operations as a MINOR version.

## Open Questions

1. The grant sets of the five new recipes (D-1). This blocks those recipes.
2. The rate and blast-radius values (D-9). This blocks agent opt-in above `propose`.
3. The openWallet identity verification mechanics, decided in planning with the companion change.
4. Whether an unsigned digest should eventually demote rungs (D-8).
