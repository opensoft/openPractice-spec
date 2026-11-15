## ADDED Requirements

### Requirement: WI-01 One queue holds every work item, each with an owner, a clock, a tier and a proposed action
openPractice SHALL keep one queue of work items. Each item SHALL carry:
- its owner, a person or agent roster entry (AM-06);
- a clock with a due time;
- the FD-5 tier of its action;
- a reference to its source;
- where one exists, a proposed action that the owner can approve, change or reject.

Screens, the mobile approver and the API SHALL all read this one queue. An item that lacks any required field SHALL NOT be created.

#### Scenario: An agent proposal becomes a work item
- **WHEN** an agent at `propose` prepares a contact update
- **THEN** a work item exists with an owner, a due time, tier 2 and the proposed change

#### Scenario: An item without an owner
- **WHEN** a process tries to create a work item with no owner
- **THEN** creation is refused, and the default owner rule of WI-02 applies instead

### Requirement: WI-02 Nothing is left unowned
Every work item SHALL have an owner from creation until it closes. When an owner is absent, the item SHALL pass to that owner's named cover. When an owner is offboarded, the item SHALL be reassigned (AM-05). When no rule applies, the controller or office manager SHALL own the item. A daily check SHALL report any item that lost its owner, as a defect.

#### Scenario: The owner is offboarded
- **WHEN** an item's owner is offboarded
- **THEN** the item is reassigned to a named owner the same day

#### Scenario: The check finds an orphan
- **WHEN** the daily check finds an item with no valid owner
- **THEN** the controller or office manager receives it, and the defect is recorded

### Requirement: WI-03 A proposed action is approved, changed or rejected under its tier
The owner, or an authorized signer under BAB-17, SHALL be able to approve, change or reject a proposed action, but the decider SHALL never be the proposer.
- Approving executes the action under its FD-5 tier.
- Changing replaces the proposal and repeats its dry run (AO-05).
- Rejecting records a coded reason.

An agent SHALL NOT decide its own proposal. An agent SHALL NOT decide another agent's proposal unless the deciding agent itself holds that action type at a rung above `propose` (AO-04), at or above the rung the decision exercises, so that it could have taken the action itself; the action then runs under the deciding agent's rung, so an `act_after_delay` decision runs its delay. A proposal that untrusted content contributed to SHALL be decided by a human (AH-05).

Every decision SHALL record the signer's identity, including an agent's openWallet ID. A tier-4 proposal SHALL be decided by a named human only. A D8 or estimator-version proposal SHALL be decided by the practice's accountant only, the outside-accountant roster entry of AM-01 (ruling 141).

**A proposal made in another role (ruling 144).** The decider is never the proposer, with one exception: where the practice has nobody else, a person MAY decide a proposal they made in another role on a record where their own party holds a role, such as a change to their own address they proposed as a patient through the patient app, under the one-person alternative of `define-mvp-practice-foundation` PR-15. The decision records both roles and notifies the outside accountant, and without an outside accountant on the roster the act waits (ruling 144). The practice has nobody else when, at the moment of the decision, the roster holds no other human roster entry that may decide the proposal and no agent that may decide it whose sponsor is not the person (design call D-39, PROPOSED). Everywhere else the rule stands, whatever role the proposer acted in, and the proposal waits for another decider.

#### Scenario: Staff approve a proposed correction
- **WHEN** front-desk staff approve an agent's proposed address correction
- **THEN** the correction applies under tier 2, and the decision records the staff member

#### Scenario: An agent tries to approve a tier-4 proposal
- **WHEN** an agent tries to approve a proposed hard delete
- **THEN** the decision is refused, and the item waits for a named human

#### Scenario: An agent tries to approve its own proposal
- **WHEN** an agent that owns an item tries to approve the proposal it made
- **THEN** the decision is refused, and the item waits for another authorized signer

#### Scenario: An agent decides another agent's proposal without an opt-in
- **WHEN** an agent at `propose` for address updates tries to approve another agent's proposed address update
- **THEN** the decision is refused, and the item waits for a human or for an agent opted in above `propose` for that action type

#### Scenario: A sole staff member decides a change they proposed as a patient
- **WHEN** a practice's only staff member, who is also its patient, decides an address change they proposed through the patient app, and an outside accountant is on the roster
- **THEN** the change applies, the decision records both roles, and the outside accountant is notified

#### Scenario: No outside accountant on the roster
- **WHEN** the same staff member tries to decide that proposal, and no outside accountant is on the roster
- **THEN** the decision is refused, and the proposal waits

#### Scenario: A proposal made in the same role
- **WHEN** a sole staff member tries to approve a proposal they made as staff
- **THEN** the decision is refused, and the proposal waits for another decider

### Requirement: WI-04 Agents hand work to humans with their evidence
An agent SHALL hand a work item to a human owner, with its evidence and reasoning references (AO-07), whenever any of these holds:
- the action exceeds its rung or cap (AO-04);
- the action derives from untrusted content (AH-05);
- a limit stops it (AO-08);
- the agents are paused (AO-09);
- the agent cannot complete the item.

The agent hands the item over through the contract's `work_item.release_to_human` operation (AO-13), which takes effect at once and names a coded reason; when the agents are paused or their PHI access is suspended, the item routes to a human owner without it (AO-09, AH-01). An agent SHALL NOT pass an item to another agent to avoid a cap.

#### Scenario: An agent meets a cap
- **WHEN** an agent's action needs a tier-4 signature
- **THEN** it hands the item to a named human with its evidence and reasoning references

#### Scenario: An agent-to-agent pass to dodge a cap
- **WHEN** an agent tries to pass a capped item to another agent
- **THEN** the pass is refused, and the item goes to a human owner

### Requirement: WI-05 Absence covers and departure reassigns
When an owner marks themselves absent, their open items SHALL be covered by a named cover, and the original owner's name SHALL stay attached. When an owner departs, their items SHALL be reassigned. A cover SHALL hold the recipe needed to act on the item. If no cover with that recipe exists, the item SHALL go to the controller or office manager.

#### Scenario: A biller is out sick
- **WHEN** the biller marks themselves absent and names the external biller as cover
- **THEN** billing items pass to the external biller, still showing the biller's name

#### Scenario: The cover lacks the recipe
- **WHEN** the named cover lacks the recipe an item needs
- **THEN** that item goes to the controller or office manager

### Requirement: WI-06 Every item has a clock, and every gate keeps its deadline escape
Each work item SHALL have a due time. When the clock expires, the item SHALL be raised to the owner's cover and to the controller or office manager. When an item is one of the gates that `define-mvp-practice-foundation` defines, that gate's own FD-5 deadline escape SHALL apply unchanged; for example, EB-18 for identity review and BAB-16 for offering-route holds. A work item SHALL NOT add a hold that stalls a claim past its filing threshold.

#### Scenario: A correction request sits too long
- **WHEN** a correction-request item's clock expires
- **THEN** it is raised to the owner's cover and to the controller

#### Scenario: A claim nears its filing threshold
- **WHEN** an identity-review item would stall a claim past its filing threshold
- **THEN** EB-18's escape applies, and the inbox shows the release

### Requirement: WI-07 The inbox carries the existing gates without redefining them
The inbox SHALL present the gates of `define-mvp-practice-foundation` as work items. Each gate's own requirement SHALL govern its meaning, tier, signer and deadline escape:
- identity review (EB-05);
- link confirmation (EB-04);
- held-date-of-birth rejections (EB-08);
- correction requests (EB-09);
- the manual-entry attestation (EB-17);
- export-batch release (AI-06);
- delivery failures (AI-07);
- reconciliation exceptions (AI-08);
- the BAA lapse (AI-14);
- credential expiry (AI-18);
- unexpected deposits (BAB-12);
- re-enrollment (BAB-13);
- offering-route holds (BAB-16).

Where this change and a referenced requirement differ, the referenced requirement SHALL govern.

#### Scenario: A batch release appears in the inbox
- **WHEN** an export batch is assembled
- **THEN** a release item appears, and AI-06 and BAB-17 decide who may release it

#### Scenario: A conflicting inbox rule
- **WHEN** an inbox rule would let a correction request apply without review
- **THEN** EB-09 governs, and the request is not applied automatically
