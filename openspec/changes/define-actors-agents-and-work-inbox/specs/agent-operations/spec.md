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

Only a named human SHALL make, widen or change a grant, change a rung (AO-04), change a limit (AO-08) or lift the pause (AO-09). That human SHALL be a roster entry holding the practice manager recipe or the controller or office manager recipe, and SHALL be recorded as a human actor; the schema refuses an agent or an integration identity as `granted_by`. No agent SHALL make any of these changes, for itself or for another agent, whatever its own grants. None of them SHALL be made because untrusted content asks for it (AH-05). Granting or widening a scope SHALL be a tier-2 action with notification to the sponsoring human. A grant SHALL be revocable at once by the granting person, the practice manager, or the controller or office manager. A grant SHALL NOT include any tier-4 action or the D8 or estimator approvals.

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
Every state-changing operation SHALL accept a dry-run mode. A dry run returns the effect the operation would have, its FD-5 tier, the rung that would apply and any sign-off it would need, and changes nothing. A dry run that reads PHI SHALL be audited as a read (PR-10).

#### Scenario: A dry run of a deactivation
- **WHEN** an agent dry-runs a patient deactivation
- **THEN** it receives the effect, tier 2 and the rung, and the patient stays active

#### Scenario: A dry run that would be refused
- **WHEN** an agent dry-runs a hard delete
- **THEN** the result states that a named human must sign at tier 4, and nothing changes

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
Each agent SHALL have limits per action type: a maximum number of actions per time window, and a maximum number of records one action may touch. When a limit is reached, the agent's further actions of that type SHALL become proposals. Its sponsoring human and the controller or office manager SHALL be alerted. The limit values SHALL be set in planning, fail closed, and be changeable only by a named human (AO-03), the practice manager or the controller or office manager, at tier 2.

#### Scenario: A rate limit is reached
- **WHEN** an agent reaches its hourly limit for address updates
- **THEN** its further address updates become proposals, and the sponsor and the controller are alerted

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
Each day, openPractice SHALL produce a digest of every agent action and proposal outcome, by agent and action type, with references and no PHI in its summary lines. The digest SHALL enter the day close, and the controller or office manager, a named human, SHALL sign it. An unsigned digest SHALL NOT block practice operations or claims. It SHALL carry into the next day's close and raise work owned by the controller or office manager.

#### Scenario: The controller signs the digest
- **WHEN** the controller signs the day close
- **THEN** the agent digest is signed with it, and the signature records the signer's identity

#### Scenario: The digest is not signed
- **WHEN** a day closes without the controller signing the digest
- **THEN** operations continue, and the digest carries into the next close as owned work

### Requirement: AO-11 The MVP builds the patient-registry operations and the read-only answer types; later features add theirs
The MVP SHALL build the contract only for these operations:
- the patient-registry operations of `define-mvp-practice-foundation` (PR-01 to PR-14): search, create, read, the field updates, guarantor and coverage changes, deactivation and reactivation;
- the read-only answer types of AH-05 exception 1 (rulings 45 and 46): `patient_answer.coverage_on_file`, `patient_answer.balance`, `patient_answer.appointment_time` and `patient_answer.practice_information`.

In the MVP, Hermes answers routine administrative questions through the read-only answer types. Each answer type SHALL be available only once the openPractice data it reads exists under a ratified change: appointment times wait for a ratified scheduling change, and balances for the patient receivables of `define-mvp-practice-foundation` (design call D-15). Until then the practice cannot switch that answer type on. Each practice-approved answer type is a versioned document under the contract, with its template reference, allowlisted fields, topics and approvals. A hard delete is offered only as a proposal (tier 4). Every later feature SHALL add its operations to the contract as a MINOR version when it ships. A request for an operation the deployed version lacks SHALL be refused.

#### Scenario: A registry update through the contract
- **WHEN** an agent updates a coverage priority under contract 1.0
- **THEN** PR-07 applies, with the agent's rung and audit

#### Scenario: A read-only answer under contract 1.0
- **WHEN** an agent selects the coverage-on-file answer type for an eligible routine administrative item, and the practice has switched that answer type on at `act_after_delay`
- **THEN** openPractice assembles the reply from the approved template and the bound patient's coverage fields, and schedules it behind the lane's cancel window

#### Scenario: An answer type whose data does not exist yet
- **WHEN** a practice tries to switch on `patient_answer.appointment_time` before a ratified scheduling change supplies appointment data
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
- the AH-05 untrusted-content cap, the server-side untrusted determination, and every condition of AH-05 exception 1.

Tests SHALL use synthetic data only.

#### Scenario: A refused request is logged
- **WHEN** an agent's request is refused
- **THEN** the log carries the request reference, the agent reference and a coded reason, and no demographic value

#### Scenario: The cap test fails
- **WHEN** a build lets a tier-3 action run at `act_with_notice`
- **THEN** the contract tests fail
