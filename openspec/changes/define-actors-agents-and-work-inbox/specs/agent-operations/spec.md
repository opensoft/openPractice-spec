## ADDED Requirements

### Requirement: AO-01 One versioned agent operations contract with a canonical schema
openPractice SHALL define the contract `openpractice-agent-operations/1.0`. Its canonical schema SHALL be `contracts/openpractice-agent-operations-1.0.schema.json` in this change, identified by the SHA-256 digest of its bytes. The contract SHALL carry a `MAJOR.MINOR` version. A client is compatible exactly when it shares the MAJOR version. Additive changes increment MINOR. A breaking change needs a new MAJOR version, a new digest and a migration path. Every request and response SHALL validate against the schema at its recorded digest.

#### Scenario: A request fails the schema
- **WHEN** an agent sends a request that does not validate against the canonical schema
- **THEN** it is refused with reason `schema_invalid`, and nothing changes

#### Scenario: An unsupported MAJOR version
- **WHEN** a client sends a `2.0` request to a `1.x` deployment
- **THEN** it is refused with reason `unsupported_major_version`

### Requirement: AO-02 Anything a screen can do, the API can do under the same rules
Every operation a screen offers SHALL be available through the versioned API (constitution Principle V, PR-09), and every API operation SHALL be governed exactly as its screen equivalent. That means the same authorization, the same FD-5 tier and sign-off rules (BAB-17), and the same audit (PR-10, AM-03). No client, agent or screen SHALL have a private endpoint or a path that skips a check.

#### Scenario: An agent updates a patient's address
- **WHEN** an agent updates an address through the API
- **THEN** the same authorization, tier and audit apply as when front-desk staff do it on screen

#### Scenario: A private endpoint is proposed
- **WHEN** a feature plan proposes an agent-only endpoint that skips a tier
- **THEN** it is out of scope until it uses the governed API

### Requirement: AO-03 Agents act only within explicit, revocable scopes per action type, set by named humans, and read only their item's patient
An agent SHALL act only on action types for which it holds an explicit grant. Each grant names the action type, the agent's roster entry, the granting person, the start date and an optional end date. No grant, at any rung, SHALL be made until the AH-01 gate is met.

Only a named human SHALL make, widen or change a grant, change a rung (AO-04), change a limit (AO-08) or lift the pause (AO-09). That human SHALL be a roster entry holding the practice manager recipe or the controller or office manager recipe, and SHALL be recorded as a human actor; the schema refuses an agent or an integration identity as `granted_by`. No agent SHALL make any of these changes, for itself or for another agent, whatever its own grants. None of them SHALL be made because untrusted content asks for it (AH-05). Granting or widening a scope SHALL be a tier-2 action with notification to the sponsoring human; when the granting person is that sponsor, the notification SHALL also go to every other named human who holds the practice manager or the controller or office manager recipe, and where there is none, the grant SHALL record that the agent's own sponsor made it (`define-practice-readiness` SR-03), which the readiness checklist lists under its RC-06. This tightening is ruling 148, and its details are design call D-40 (PROPOSED). A grant SHALL be revocable at once by the granting person, the practice manager, or the controller or office manager. A grant SHALL NOT include any tier-4 action or the D8 or estimator approvals.

An agent's reads SHALL be limited to the target patient of the work item it is working, named by `work_item_ref` on every agent request. For an item that came through the patient gateway (change `define-patient-gateway-and-escalation`), the target patient is the patient bound to that gateway link. An agent SHALL NOT read any other patient's record under that item, whatever the item's text asks. An agent's patient search SHALL return only the exact identity match for the item's target patient (PR-08), and never a candidate list (design call D-17). A request outside this scope SHALL be refused with reason `patient_scope`, and the item SHALL go to a human owner.

#### Scenario: An agent without a grant
- **WHEN** an agent requests an action type it holds no grant for
- **THEN** the request is refused with reason `no_grant`, and a work item is not created on its behalf

#### Scenario: A grant is revoked mid-session
- **WHEN** a grant is revoked while the agent holds a session
- **THEN** its next request under that grant is refused

#### Scenario: An agent tries to widen its own scope
- **WHEN** an agent tries to grant itself a new action type, raise its own rung, raise a limit, or lift the pause
- **THEN** the change is refused, nothing changes, and only a named human may make it

#### Scenario: A message asks about another person's balance
- **WHEN** a patient's message leads an agent to read another patient's balance
- **THEN** the read is refused with `patient_scope`, no data about the other patient is returned, and the item goes to a human owner

#### Scenario: A sponsor widens their own agent's scope
- **WHEN** the practice manager, who sponsors an agent, widens that agent's scope, and the practice also has a controller
- **THEN** the sponsor is notified as before, and the controller is notified too

### Requirement: AO-04 The autonomy ladder never gives less friction than the FD-5 tier
Each granted action type SHALL sit on one rung of the autonomy ladder (ruling 37):
- `observe`: read only, with no proposals;
- `propose`: the agent prepares the action as a work item for a human or other authorized signer;
- `act_with_notice`: the agent acts and the owner is notified;
- `act_after_delay`: the agent's action executes after a visible delay with a cancel window.

Every action type SHALL start at `propose`, and the practice SHALL opt each one up separately. Only a named human SHALL change a rung (AO-03). Raising an action type above `propose` for an agent SHALL be that action type's agent-signing opt-in under BAB-17; there is no second opt-in. Every grant, at any rung, SHALL require the AH-01 gate.

The highest rung SHALL be capped so that an agent never acts with less friction than the action's FD-5 tier requires:

| Action | Highest agent rung |
|---|---|
| Tier 1 or tier 2 | `act_with_notice` (an agent never acts silently) |
| Tier 3 | `act_after_delay`, with at least the tier's cancel window |
| An explicit sign-off gate of `define-mvp-practice-foundation` (its tension T-17) | `act_with_notice`, which is the BAB-17 agent sign-off |
| Tier 4 | `propose`; a named human signs |
| The D8 or an estimator-version approval | `propose`; the practice's accountant signs |
| An action derived from untrusted content (AH-05) | `propose`, except AH-05 exception 1 |
| A read-only answer to the signed-in patient (AH-05 exception 1) | `act_after_delay` only, with the practice-set window that the item's lane can cancel |
| A work-item operation of AO-13 | `propose`; reading item text needs no more than `observe` |

#### Scenario: An action type is opted up within its cap
- **WHEN** the practice opts the address-update action type up to `act_with_notice` for an agent, and the AH-01 gate is met
- **THEN** the agent updates addresses and the owner is notified of each update

#### Scenario: An opt-up beyond the cap
- **WHEN** someone tries to set a tier-3 action type or a read-only answer type to `act_with_notice`, or a tier-4 action type above `propose`
- **THEN** the change is refused, and the rung stays where it was

#### Scenario: Default rung
- **WHEN** a new action type is granted to an agent
- **THEN** it starts at `propose`

### Requirement: AO-05 Every operation supports a dry run
Every state-changing operation SHALL accept a dry-run mode. A dry run returns the effect the operation would have, its FD-5 tier, the rung that would apply and any sign-off it would need, and changes nothing. A dry run that reads PHI SHALL be audited as a read (PR-10). A dry run whose operation would be refused SHALL answer `refused`, with the refusal reason the live request would get, and changes nothing; `dry_run_result` carries no `refusal_reason`, and no `executes_at` for an action that would be scheduled (AO-07; the schema's response rules).

#### Scenario: A dry run of a deactivation
- **WHEN** an agent dry-runs a patient deactivation
- **THEN** it receives the effect, tier 2 and the rung, and the patient stays active

#### Scenario: A dry run of a tier-4 action
- **WHEN** an agent dry-runs a hard delete
- **THEN** the result states that a named human must sign at tier 4, and nothing changes

#### Scenario: A dry run that would be refused
- **WHEN** an agent dry-runs an action type for which it holds no grant
- **THEN** the response is `refused` with `no_grant`, as the live request would be, and nothing changes

### Requirement: AO-06 Every state-changing request carries an idempotency key free of PHI
Every state-changing request SHALL carry an idempotency key. The same key with the same payload SHALL produce the same single effect. The same key with a different payload SHALL be refused. Keys MUST NOT contain names, dates of birth, contact details, account numbers, member identifiers or any other per-person data.

#### Scenario: A retried request
- **WHEN** an agent retries a timed-out request with the same key and payload
- **THEN** exactly one effect exists, and the response repeats the original outcome

#### Scenario: Key reuse with a new payload
- **WHEN** an agent reuses a key with a different payload
- **THEN** the request is refused with reason `idempotency_conflict`

### Requirement: AO-07 Each proposal and action carries evidence and reasoning references
Each agent proposal or action SHALL carry evidence references: opaque references to the records, documents or messages it relied on. It SHALL also carry a reasoning reference that points to a reasoning artifact held in openPractice under PR-10 access control. A reasoning artifact MUST NOT contain clinical content (constitution Principle IV). openPractice SHALL record whether untrusted content contributed, as it determines on the server under AH-05; the agent's own flag may only raise that determination. Reasoning text and evidence content MUST NOT appear in logs, notifications or diagnostics. A proposal without evidence references SHALL be refused.

#### Scenario: A proposal with evidence
- **WHEN** an agent proposes a coverage update citing a scanned card and an eligibility response
- **THEN** the work item shows both references and the reasoning reference, and the log holds only identifiers

#### Scenario: A proposal with no evidence
- **WHEN** an agent submits a proposal with no evidence references
- **THEN** it is refused with reason `evidence_missing`

### Requirement: AO-08 Rate and blast-radius limits stop runaway agents
Each agent SHALL have limits per action type: a maximum number of actions per time window, and a maximum number of records one action may touch. When a limit is reached, the agent's further actions of that type SHALL become proposals. A work-item operation of AO-13 is already a proposal, and a re-route or an urgency raise applies by rule at once, so for those operations a reached limit SHALL instead refuse every further request of that kind with `rate_limited`, and the item that request named SHALL pass at once to its lane's human owner, who may still re-route it or raise it (design call D-25, PROPOSED). The released item SHALL show its human owner the refused proposal, the category or urgency the agent proposed, as a proposal for a human to judge, while the item keeps its category and urgency until a human changes them. When the refused proposal was `urgent`, the alert that tells the owner of the release SHALL be sent at urgent priority. The automatic application stays refused at the limit, so nothing floods the clinical queue or starts patient guidance without a human (design call D-25, PROPOSED). Its sponsoring human and the controller or office manager SHALL be alerted. The limit values SHALL be set in planning, fail closed, and be changeable only by a named human (AO-03), the practice manager or the controller or office manager, at tier 2.

#### Scenario: A rate limit is reached
- **WHEN** an agent reaches its hourly limit for address updates
- **THEN** its further address updates become proposals, and the sponsor and the controller are alerted

#### Scenario: An agent re-routes item after item
- **WHEN** an agent reaches its limit for `work_item.propose_clinical_reroute` and sends another re-route
- **THEN** the request is refused with `rate_limited`, the item passes to its lane's human owner with its category unchanged and the refused re-route shown, and the sponsor and the controller are alerted

#### Scenario: An urgent raise is refused at a limit
- **WHEN** an agent at its limit for `work_item.propose_urgency_raise` proposes `urgent` for an item
- **THEN** the request is refused with `rate_limited`, the item keeps its urgency and passes to its lane's human owner with the refused `urgent` proposal shown, the owner is alerted at urgent priority, and no clinical routing or patient guidance starts until a human acts

#### Scenario: A bulk action exceeds the blast radius
- **WHEN** an agent's single action would touch more records than its limit
- **THEN** the action is refused as an action and offered as a proposal

### Requirement: AO-09 A practice-wide pause switch stops every agent at once
The practice manager, the medical director, and the controller or office manager SHALL each be able to pause all agents with one action. While paused:
- every agent SHALL be limited to `observe`;
- pending delayed actions SHALL be held, not executed;
- work SHALL route to human owners.

The pause SHALL NOT block, delay or drop any urgent patient escalation, which routes to humans. Lifting the pause SHALL be a tier-2 action by a named human (AO-03), the practice manager or the controller or office manager, and it restores the prior rungs. A delayed action held during the pause SHALL restart its full delay window when the pause lifts, and SHALL NOT execute at once.

#### Scenario: The pause is pressed
- **WHEN** the medical director presses the pause
- **THEN** every agent drops to `observe`, delayed actions are held, and the inbox shows the pause

#### Scenario: An urgent escalation during a pause
- **WHEN** an urgent escalation arrives while agents are paused
- **THEN** it routes to a human owner without delay

#### Scenario: The pause lifts with actions held
- **WHEN** the pause is lifted while delayed actions are held
- **THEN** each held action restarts its full delay window and can be cancelled within it; none executes at once

### Requirement: AO-10 A daily digest of agent actions enters the day close for the controller's signature
Each day, openPractice SHALL produce a digest of every agent action and proposal outcome, by agent and action type, with references and no PHI in its summary lines. The digest SHALL enter the day close, and the controller or office manager, a named human, SHALL sign it. The lines for an agent whom that signer sponsors SHALL be signed by another named human who holds the practice manager or the controller or office manager recipe and does not sponsor that agent; where the practice has no such person, the signer SHALL sign those lines in both roles, each recorded, and the digest SHALL mark them as reviewed by the agent's own sponsor (`define-practice-readiness` SR-03). This tightening is ruling 148, and its details are design call D-40 (PROPOSED). An unsigned digest SHALL NOT block practice operations or claims. It SHALL carry into the next day's close and raise work owned by the controller or office manager.

#### Scenario: The controller signs the digest
- **WHEN** the controller signs the day close
- **THEN** the agent digest is signed with it, and the signature records the signer's identity

#### Scenario: The digest is not signed
- **WHEN** a day closes without the controller signing the digest
- **THEN** operations continue, and the digest carries into the next close as owned work

#### Scenario: The digest lists an agent its signer sponsors
- **WHEN** the controller signs the daily digest, which lists actions of an agent the controller sponsors, and the practice manager does not sponsor that agent
- **THEN** the practice manager signs that agent's lines, and the controller signs the rest

### Requirement: AO-11 The MVP builds the patient-registry operations and the read-only answer types; later features add theirs
The MVP SHALL build the contract only for these operations:
- the patient-registry operations of `define-mvp-practice-foundation` (PR-01 to PR-14): search, create, read, the field updates, guarantor and coverage changes, deactivation and reactivation;
- the read-only answer types of AH-05 exception 1 (rulings 45 and 46): `patient_answer.coverage_on_file`, `patient_answer.balance`, `patient_answer.appointment_time` and `patient_answer.practice_information`;
- the work-item operations of AO-13 (ruling 93): `work_item.read_text`, `work_item.propose_clinical_reroute`, `work_item.propose_urgency_raise`, `work_item.release_to_human` and `work_item.cancel_pending_answer`.

In the MVP, Hermes answers routine administrative questions through the read-only answer types. Each answer type SHALL be available only once the openPractice data it reads exists under a ratified change: appointment times wait for the booking of `define-revenue-cycle-core` (AP-01 to AP-09), and balances for its guarantor-account balances (PS-01, PS-09); that change is a draft not yet on `main` (design calls D-15 and D-41, PROPOSED). Until then the practice cannot switch that answer type on. Each practice-approved answer type is a versioned document under the contract, with its template reference, allowlisted fields, topics and approvals. A hard delete is offered only as a proposal (tier 4). Every later feature SHALL add its operations to the contract as a MINOR version when it ships. A request for an operation the deployed version lacks SHALL be refused.

#### Scenario: A registry update through the contract
- **WHEN** an agent updates a coverage priority under contract 1.0
- **THEN** PR-07 applies, with the agent's rung and audit

#### Scenario: A read-only answer under contract 1.0
- **WHEN** an agent selects the coverage-on-file answer type for an eligible routine administrative item, and the practice has switched that answer type on at `act_after_delay`
- **THEN** openPractice assembles the reply from the approved template and the bound patient's coverage fields, and schedules it behind the lane's cancel window

#### Scenario: An answer type whose data does not exist yet
- **WHEN** a practice tries to switch on `patient_answer.appointment_time` before the booking of `define-revenue-cycle-core`, a draft not yet on `main`, is ratified and supplies appointment data
- **THEN** the change is refused, and appointment questions go to staff

#### Scenario: An operation not yet in the contract
- **WHEN** an agent requests a claim submission under contract 1.0
- **THEN** it is refused with reason `operation_not_supported`

### Requirement: AO-12 No PHI in agent logs or errors, and contract tests cover the boundary
Logs, errors, diagnostics and refusal reasons under this contract MUST NOT contain names, dates of birth, contact details, identifier values or clinical content. They SHALL use coded reasons and opaque references. The contract SHALL have contract tests covering:
- every operation;
- the dry run and idempotency;
- every rung and every cap;
- grants and revocation, and refusal of any grant, rung, limit or pause change that is not made by a named human;
- the per-patient read scope;
- the limits and the pause;
- the digest;
- the AH-05 untrusted-content cap, the server-side untrusted determination, and every condition of AH-05 exception 1;
- every work-item operation of AO-13: its outcome, its `propose` cap, `not_item_owner`, `text_not_held` for every item kind that has no text, `answer_not_pending`, and the `rate_limited` refusal with the item's release at an AO-08 limit, the refused proposal shown to the human owner, and an urgent-priority alert when the refused proposal was `urgent`;
- the consent check of AH-06 before scheduling and before every relay, `consent_not_recorded`, a withdrawal that wins over a concurrent relay, the hold after `ai_messaging_withdrawal_requested`, a representative's withdrawal, and the cancellation of every pending answer on withdrawal.

Tests SHALL use synthetic data only.

#### Scenario: A refused request is logged
- **WHEN** an agent's request is refused
- **THEN** the log carries the request reference, the agent reference and a coded reason, and no demographic value

#### Scenario: The cap test fails
- **WHEN** a build lets a tier-3 action run at `act_with_notice`
- **THEN** the contract tests fail

### Requirement: AO-13 An agent works an item through five work-item operations, none of which rises above propose
Contract 1.0 SHALL carry five work-item operations (ruling 93). Each request SHALL name the item the agent is working as `work_item_ref`, SHALL carry no `payload` and no `answer_type`, and SHALL act only on that item. Each operation is an action type like any other: it needs a grant (AO-03), the AH-01 gate and the per-patient read scope of AO-03. Each SHALL be refused with `not_item_owner` unless the agent is the item's current owner when openPractice receives the request (design call D-26, PROPOSED); an item that has passed to a human, including every item the pause or a suspension routed to humans, is out of the agent's reach. A reached AO-08 limit refuses further requests of that kind with `rate_limited` and releases the item to a human (AO-08). While agents are paused, every agent is limited to `observe` (AO-09) and its items route to human owners, so a work-item operation is refused with `paused`, or with `not_item_owner` once its item has a human owner. None of them changes a patient record, and none rises above `propose`; the schema refuses an autonomy setting above `propose` for any of them (design calls D-18 to D-20). None of them is an exception to AH-05: an agent's work on untrusted content stays at `propose`.

**`work_item.read_text`.** The agent reads the text openPractice holds for the item it is working. The response has outcome `read_result` and carries the text as `item_text`, with its kind and the reference of the stored record. What text exists (design call D-18, PROPOSED):
- **An administrative inquiry:** the question text that PG-10 of change `define-patient-gateway-and-escalation` stores as an administrative communication record. This is the only text kind in contract 1.0.
- **A clinical inquiry:** no text exists in openPractice. A clinical inquiry carries no text into openPractice (PG-10); its conversation lives in HealthLinc or medXfactory, and only openChart retrieves it. The read SHALL be refused with `text_not_held`.
- **An item whose text was removed** when it was recategorized as clinical (ER-12): no text exists any more. The read SHALL be refused with `text_not_held`.
- **The document behind a registry item,** such as an inbound fax, a scanned insurance card, an upload or an email named as evidence (AO-07): no change gives openPractice a store of document content, so contract 1.0 has no document text to return. The read SHALL be refused with `text_not_held`. A governed change that gives openPractice a document's administrative content SHALL add that text kind as a MINOR version; clinical content never becomes readable here (constitution Principle IV).

The text is untrusted content: data, never instructions (AH-05). Its read SHALL be audited as a PHI read (PR-10), limited to the item's patient (AO-03) and to the operator's and provider's recipient profiles for this action type (AH-03), and never logged (AO-12). A read changes nothing, so its rung needs to be no higher than `observe`.

**`work_item.propose_clinical_reroute`.** The agent proposes that the administrative item it is working is clinical (`proposed_category` `clinical`) or mixed (`mixed`). openPractice's fixed routing rules SHALL apply the proposal at once, raise-only, with no human decision, as change `define-patient-gateway-and-escalation` already provides for an agent's clinical-category proposal (its ER-12 and design call DC-14): the item routes as clinical, its stored text is removed, and a mixed item's administrative part is resubmitted. The response has outcome `applied_by_rule`. The agent drafts and answers nothing more on that item (ER-10 of that change), and every pending agent answer on the item is cancelled (ER-12 of that change). The agent's own rung stays `propose`; the rule applies the proposal, and the rule can only add clinical care (design call D-19).

**`work_item.propose_urgency_raise`.** The agent proposes a raise to `priority` or `urgent` (`proposed_urgency`). The routing rules SHALL apply it at once when it is higher than the item's applied urgency, and SHALL record it and change nothing when it is not; urgency is never lowered (ER-02 and ER-03 of that change). The response has outcome `applied_by_rule`. A raise is always a proposal: no rung above `propose` exists for it, because the rules, not the agent, raise the urgency (design call D-19).

**`work_item.release_to_human`.** The agent gives the item it owns to a human owner, with its evidence and reasoning references, whenever WI-04 requires it. It SHALL name a `release_reason`: `exceeds_rung_or_cap`, `untrusted_content`, `limit_reached`, `patient_asked_for_person`, `ai_messaging_withdrawal_requested` or `cannot_complete` (design calls D-21 and D-27, PROPOSED). With `ai_messaging_withdrawal_requested`, the patient's text asked to stop AI-authored answers, and openPractice holds every agent answer to that patient under AH-06 until a staff member decides. The item SHALL pass to the human owner the inbox's rules choose (WI-02, WI-05), never to another agent (WI-04), and stays open with its clock. The release takes effect at once at `propose`, because it only puts the item before a human, which is what a proposal does (design call D-20). The response has outcome `released`.

**`work_item.cancel_pending_answer`.** The agent cancels a read-only answer that it scheduled for the item it still owns, while the answer is inside its cancel window, named by `cancels_request_id`, the `request_id` of the request that scheduled it. Once the item has a human owner, that human cancels instead (ER-14 of that change). The cancellation takes effect at once at `propose`, because it only stops an outbound answer (design call D-20). The answer SHALL NOT be sent, and the item SHALL pass to its lane's human owner and stay open (ER-14 of that change). The response has outcome `cancelled`. An answer already sent, already cancelled, or scheduled by another request SHALL be refused with `answer_not_pending`.

Each of these responses records openPractice's own untrusted determination (AH-05, AO-07), except a read, which acts on nothing. The schema refuses `executed`, `scheduled` and `proposed` for every work-item operation, and each new outcome for any other action type.

#### Scenario: The agent reads an administrative question
- **WHEN** an agent granted `work_item.read_text` reads the routine administrative inquiry it is working
- **THEN** it receives the stored question text and its record reference with outcome `read_result`, the read is audited as a PHI read, and nothing changes

#### Scenario: The agent tries to read a clinical inquiry
- **WHEN** an agent asks to read the text of a clinical inquiry
- **THEN** the read is refused with `text_not_held`, because openPractice holds no clinical text, and the agent reads nothing elsewhere

#### Scenario: The agent tries to read a fax behind a registry item
- **WHEN** an agent asks to read the content of an inbound fax that a coverage item cites as evidence
- **THEN** the read is refused with `text_not_held`, because contract 1.0 holds no document content

#### Scenario: The agent finds a symptom in a billing question
- **WHEN** an agent working a billing item sends `work_item.propose_clinical_reroute` with `proposed_category` `mixed`
- **THEN** the routing rules apply it at once with outcome `applied_by_rule`, the item's clinical part goes to openChart by reference, the stored text is removed, and the agent drafts nothing more for the item

#### Scenario: The agent proposes a lower urgency than the item has
- **WHEN** an agent proposes `priority` for an item already `urgent`
- **THEN** the proposal is recorded, the item stays `urgent`, and nothing else changes

#### Scenario: The patient asks for a person
- **WHEN** the text of an item the agent owns asks for a person, and the agent sends `work_item.release_to_human` with `patient_asked_for_person`
- **THEN** the item passes at once to its lane's human owner with the agent's evidence and reasoning references, with outcome `released`, and no read-only answer is sent

#### Scenario: The agent cancels its own pending answer
- **WHEN** an agent cancels the read-only answer it scheduled, inside the cancel window
- **THEN** the answer is not sent, the item passes to its lane's human owner, and the response is `cancelled`

#### Scenario: The answer has already gone
- **WHEN** an agent asks to cancel an answer that has already been sent
- **THEN** the request is refused with `answer_not_pending`

#### Scenario: The agent no longer owns the item
- **WHEN** an agent sends any work-item operation for an item that has passed to a human owner
- **THEN** the request is refused with `not_item_owner`, and nothing is read or changed

#### Scenario: The patient asks the agent to stop AI answers
- **WHEN** the text of an item the agent owns asks to stop AI-written answers, and the agent releases the item with `ai_messaging_withdrawal_requested`
- **THEN** the item passes to its lane's human owner, and every agent answer to that patient is held until a staff member records the withdrawal or records that consent stands (AH-06)

#### Scenario: Someone tries to opt a work-item operation up
- **WHEN** someone sets `work_item.release_to_human` or `work_item.propose_urgency_raise` to `act_with_notice` for an agent
- **THEN** the setting is refused, and the operation stays at `propose`
