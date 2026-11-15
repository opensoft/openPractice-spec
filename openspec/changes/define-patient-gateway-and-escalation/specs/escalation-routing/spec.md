## ADDED Requirements

### Requirement: ER-01 openPractice keeps a routing record only, with no clinical content
openPractice SHALL keep a routing record for every accepted inquiry, and that record SHALL hold only (ruling 34):
- the routing id and the gateway link;
- whether the patient or a representative submitted it;
- the submitted, routed, acknowledgment and closing times;
- the proposed and applied category;
- the proposed and applied urgency, with every raise and its source;
- the current owner and lane;
- HealthLinc's message reference and reply-path reference;
- the handoff state and the clock policy version.

For a clinical item, the routing record SHALL be all that openPractice keeps. No question text, reply text or other clinical content SHALL be stored in openPractice, in line with constitution Principle IV. Administrative question and reply text is kept only under PG-10 and PG-11.

#### Scenario: A clinical question is routed
- **WHEN** a clinical inquiry is routed to openChart
- **THEN** openPractice's routing record holds the references, times, category, urgency and owner, and no text

#### Scenario: Someone tries to attach clinical content
- **WHEN** a user or integration tries to attach a note or message text to a clinical routing record
- **THEN** the attempt is refused and nothing is stored

### Requirement: ER-02 HealthLinc's AI proposes category and urgency, and openPractice's fixed rules apply them
openPractice SHALL take the category and urgency that HealthLinc's AI proposes and apply them through openPractice's routing rules (ruling 44). The rules SHALL be a fixed, versioned rule set that ships with openPractice. Practice configuration SHALL be limited to staff lanes, practice hours and the on-call roster (FD-1 as amended by ruling 38; design call DC-17).

The rules SHALL apply these defaults (design call DC-14):
- an unknown category routes as clinical;
- when any proposal marks a part clinical, that part routes as clinical;
- the applied urgency is the highest of every urgency proposal received, whether from HealthLinc, a staff member or an agent, and of any minimum the rules set.

Applying the rules is automatic. It makes no decision for the patient; it only delivers the item to an owner (tension T-6). Each routing decision SHALL record the rule-set version and the inputs it used.

#### Scenario: A routine billing question
- **WHEN** HealthLinc proposes `administrative`, topic `billing_statement` and `routine`
- **THEN** the rules route the item to the biller lane at `routine` and record the rule-set version

#### Scenario: An agent proposes a higher urgency
- **WHEN** an agent proposes `urgent` for an item applied at `routine`
- **THEN** the rules apply `urgent` at once, record the agent as the source, and no approval is awaited

#### Scenario: A practice tries to author a rule
- **WHEN** an administrator tries to add a routing rule beyond lanes, hours and the on-call roster
- **THEN** the change is refused

### Requirement: ER-03 Urgency is never downgraded
openPractice MUST NOT lower an item's applied urgency, whether by rule, staff action, agent action or a later HealthLinc proposal. Urgency MAY only be raised. Each raise SHALL be recorded with its source and time. A raise on a clinical item SHALL be sent to openChart as `handoff.urgency_raised`. Neither contract SHALL contain a message that lowers urgency.

#### Scenario: A staff member tries to lower urgency
- **WHEN** a staff member tries to change an `urgent` item to `routine`
- **THEN** the change is refused and the item stays `urgent`

#### Scenario: HealthLinc later proposes a lower urgency
- **WHEN** HealthLinc resubmits a split part with a lower urgency than the item already has
- **THEN** the applied urgency stays as it was, and the lower proposal is recorded

#### Scenario: Urgency is raised on a clinical item
- **WHEN** an item already handed to openChart is raised from `priority` to `urgent`
- **THEN** openChart receives `handoff.urgency_raised` with the new acknowledgment and response times

### Requirement: ER-04 Clinical items are handed to openChart under one versioned handoff contract
openPractice SHALL hand every clinical item to the patient's linked openChart, or other linked EHR, under one versioned contract, `openpractice-clinical-handoff/1.0` (rulings 34, 36 and 43). The canonical schema SHALL be `openpractice-clinical-handoff-1.0.schema.json` in this change's `contracts/` directory, identified by the SHA-256 digest of its bytes. Both sides SHALL validate every message against the schema with the recorded digest. Any schema change SHALL produce a new digest, recorded in this change and in the openChart companion change before either side implements it.

The contract follows PG-01's versioning rules: a shared MAJOR version is compatible, additive changes increment MINOR, no sender sends a MINOR the receiver has not declared, and an unsupported MAJOR is refused with `unsupported_major_version`. The messages SHALL be `handoff.offer`, `handoff.ack`, `handoff.urgency_raised`, `handoff.status` and `message.ack`.

#### Scenario: A valid offer is accepted
- **WHEN** openPractice sends a `handoff.offer` that validates against the schema with the recorded digest
- **THEN** openChart accepts it with `handoff.ack` and starts its response clock

#### Scenario: An offer fails the schema
- **WHEN** openChart receives an offer that fails the canonical schema
- **THEN** it rejects the offer with `schema_invalid`, and openPractice alerts the office manager under ER-07

### Requirement: ER-05 The handoff is reference-only and carries exactly the stated fields
A `handoff.offer` SHALL carry exactly these fields, and no others (design call DC-2):
- the routing id, as `handoff_id`, and the EB-01 `link_id`;
- the EHR patient triple from that link;
- HealthLinc's message reference and reply-path reference;
- the category `clinical` and the urgency;
- the submitted, routed, acknowledge-by and respond-by times;
- the clock policy reference;
- the origin, and an opaque split-group reference for a split question.

It MUST NOT carry the message body, question text, reply text, attachments or any other clinical content, and the schema SHALL refuse any other field. openChart SHALL fetch the message body from HealthLinc over HealthLinc's own link (ruling 39). The clinician's reply SHALL go from openChart to HealthLinc over openChart's reply path, never through openPractice. Both are companion-change items.

#### Scenario: An offer is built
- **WHEN** openPractice builds a `handoff.offer`
- **THEN** it contains only the listed references, codes and times, and no text

#### Scenario: openChart cannot fetch the message
- **WHEN** openChart reports `content_unavailable` for a handoff
- **THEN** openPractice alerts the office manager, and runs ER-09's fallback if the item is urgent

### Requirement: ER-06 Automatic handoff needs an active EHR link that is not in identity review
openPractice SHALL offer a handoff automatically only when the patient has an active EB-01 link to the EHR that is not in identity review (EB-05) (design call DC-24). When no active link exists, or the link is in identity review, openPractice MUST NOT guess a chart. It SHALL make the item a clinical routing exception owned by the office manager, holding no clinical content. It SHALL run ER-09's fallback when the item is urgent. When a patient is linked to more than one EHR, the item SHALL go to the EHR the practice configures for clinical messaging; with no such configuration, it becomes an exception.

#### Scenario: The patient has an active link
- **WHEN** a clinical item arrives for a patient with an active EB-01 link that is not in review
- **THEN** openPractice offers the handoff to that EHR

#### Scenario: The link is in identity review
- **WHEN** an urgent clinical item arrives for a patient whose EHR link is in identity review
- **THEN** no handoff is offered, the office manager owns the exception, and ER-09's fallback runs

#### Scenario: The patient has no EHR link
- **WHEN** a routine clinical item arrives for a patient with no EHR link
- **THEN** the office manager owns a clinical routing exception with no clinical content, under a clock

### Requirement: ER-07 openChart acknowledges each handoff and owns the clock, and openPractice alerts on silence or expiry
openChart SHALL own the queue and the response clock for every routed clinical item (ruling 43). openPractice SHALL expect a `handoff.ack` for each `handoff.offer` by its `ack_by` time. It SHALL keep a shadow of the response deadline as `respond_by` (design call DC-23).

openPractice SHALL alert the office manager when any of these happens:
- no accepted acknowledgment arrives by `ack_by`;
- openChart rejects the handoff;
- openChart reports `clock_expired` or `content_unavailable`;
- neither `responded` nor `clock_expired` arrives by `respond_by`.

Each alert SHALL become work owned by the office manager (WI-02), and SHALL also reach the office manager's mobile approver when one is registered (MA-02). openPractice SHALL retry an unacknowledged offer with the same `message_id`. An alert SHALL never cancel or withdraw the handoff. openChart's `returned_not_clinical` status SHALL re-route the item under ER-12.

#### Scenario: openChart acknowledges and responds in time
- **WHEN** openChart accepts an offer before `ack_by` and reports `responded` before `respond_by`
- **THEN** no alert is raised and the routing record closes

#### Scenario: No acknowledgment arrives
- **WHEN** `ack_by` passes with no accepted acknowledgment
- **THEN** the office manager is alerted, the offer is retried with the same `message_id`, and the item stays owned

#### Scenario: The response deadline passes silently
- **WHEN** `respond_by` passes and openChart has reported neither `responded` nor `clock_expired`
- **THEN** openPractice treats the clock as expired and alerts the office manager

### Requirement: ER-08 Acknowledgment and response windows come from the medical director's policy, with fail-closed defaults
The acknowledgment window and the response clock for each urgency SHALL come from a versioned routing-clock policy. The practice's medical director, a named human, SHALL approve each version; the approval SHALL NOT be opted in for agent signing (design call DC-28).

Until a version is approved, openPractice SHALL use these fail-closed defaults, in calendar time (design call DC-3):
- `urgent`: acknowledge within 5 minutes; respond within 60 minutes;
- `priority`: acknowledge within 15 minutes; respond within 8 hours;
- `routine`: acknowledge within 60 minutes; respond within 48 hours.

Each handoff SHALL record the policy version and whether it was approved or a default. An approved policy SHALL keep the windows ordered, so an urgent window is never longer than a priority window, and a priority window never longer than a routine one. A change SHALL apply only to handoffs offered after it. These windows are alerting thresholds, not clinical standards.

#### Scenario: No policy is approved yet
- **WHEN** a clinical item is handed off before any routing-clock policy is approved
- **THEN** the default windows apply and the offer records `fail_closed_default`

#### Scenario: An approved policy applies
- **WHEN** the medical director approves a policy version with an urgent acknowledgment window of 3 minutes
- **THEN** urgent handoffs offered afterwards use 3 minutes and record that version as approved

#### Scenario: An agent tries to approve the policy
- **WHEN** a named AI agent tries to approve a routing-clock policy version
- **THEN** the approval is refused whatever the opt-in settings

### Requirement: ER-09 An urgent escalation is never blocked, delayed or dropped, and it has a fallback
An urgent escalation MUST NOT be blocked, delayed by a payment hold or any other status, or dropped (rulings 41 and 43). openPractice SHALL accept and route an urgent inquiry from every linked patient, active or not (PG-03, PG-04).

openPractice SHALL run the fallback at once when the item is urgent and any of these happens: openChart is unreachable; openChart rejects the handoff; openChart reports `content_unavailable`; no acknowledgment arrives within the urgent window; or ER-06 makes the item an exception. The fallback SHALL:
- alert the office manager;
- alert the on-call clinician for the hour (ER-13);
- send HealthLinc `guidance.instruct` with `show_emergency_guidance`, so HealthLinc shows its own in-app emergency guidance.

openPractice SHALL keep retrying the handoff while the fallback runs. openPractice MUST NOT give clinical advice: the instruction is a code, and the guidance is HealthLinc's. The pause switch (AO-09) and an unavailable Hermes SHALL NOT affect urgent routing, which never depends on an agent. An urgent inquiry that openPractice rejects as invalid SHALL raise an integration exception owned by the office manager. HealthLinc treats any rejected urgent inquiry under its own emergency guidance (companion item).

#### Scenario: A held patient sends an urgent message
- **WHEN** a patient on a payment hold, or with a deactivated record, submits an urgent clinical inquiry
- **THEN** openPractice accepts it and hands it off with no delay

#### Scenario: openChart is down
- **WHEN** openChart does not acknowledge an urgent handoff within the urgent window
- **THEN** the office manager and the on-call clinician are alerted, HealthLinc is told to show its emergency guidance, and the handoff is retried

#### Scenario: An urgent inquiry is rejected as invalid
- **WHEN** an inquiry proposed as urgent fails the gateway schema
- **THEN** the office manager owns an integration exception that holds no PHI, and HealthLinc applies its emergency guidance

### Requirement: ER-10 Hermes answers administrative items only under change A's ladder and scopes, starting at propose
Answering an administrative item by agent SHALL be the action type `gateway.administrative_reply`, at FD-5 tier 3 because it is outbound (rulings 34 and 37). It SHALL be governed by the autonomy ladder and scopes of change `define-actors-agents-and-work-inbox` (AO-03, AO-04), and SHALL start at `propose`. At `propose`, Hermes drafts the reply with evidence and reasoning references (AO-07), and a named human in the item's lane approves, changes or rejects it in the work inbox (WI-03) before it is relayed.

The action type MAY rise above `propose` only where AO-04 and AH-05 allow, only through its BAB-17 opt-in, and only to `act_after_delay`, because a tier-3 action never runs with less friction than its countdown (ruling 37). Every answer is derived from the patient's message, which is untrusted content, so this change asserts no rung above `propose` for it. Design call DC-13 proposes a narrow exception for owner review.

Before Hermes processes any administrative question, the AI data-handling rules SHALL be met: a provider BAA (AH-02), minimum necessary under an AI-23 recipient profile (AH-03), and no PHI in agent logs (AH-04). Hermes MUST NOT answer a clinical item or the clinical part of a mixed one; it SHALL propose a re-route instead. Hermes acts only as a roster entry with an openWallet ID and a sponsoring human (AM-04).

Change A's AO-11 limits the MVP agent contract to registry operations. Until a governed MINOR version of `openpractice-agent-operations` carries this action type, agents get no gateway action type, and every administrative item routes to staff (design call DC-22).

#### Scenario: Hermes drafts an answer that staff approve
- **WHEN** Hermes drafts a reply about office hours at `propose` and a front-desk staff member approves it
- **THEN** HealthLinc receives the reply with author kind `agent`, rung `propose` and the approver's reference

#### Scenario: Hermes tries to send without approval
- **WHEN** Hermes tries to relay a reply at `propose` without a human approval
- **THEN** the relay is refused and the draft waits in the work inbox

#### Scenario: Hermes sees clinical content
- **WHEN** Hermes finds clinical content in an administrative item
- **THEN** it drafts no answer and proposes a re-route, which the rules apply under ER-12

### Requirement: ER-11 Other administrative items route to staff by lane, and staff answer only in their lane
Every administrative item SHALL be assigned a lane by the fixed topic-to-lane table (ruling 34):
- `scheduling`, `demographics` and `forms` go to the front desk;
- `billing_statement`, `payment` and `coverage` go to the biller;
- `records_request`, `general` and any unmapped topic go to the office manager.

When Hermes does not draft an item, it SHALL route to that lane's staff. Lanes SHALL be filled from change A's role recipes (AM-02), including external billers and remote VAs under their safeguards (VA-01 to VA-05). A staff member SHALL answer only items in their own lane (ruling 44). The office manager's lane covers every administrative lane (design call DC-16). No openPractice staff member, office manager included, SHALL answer a clinical item. A reply from outside the item's lane SHALL be refused; the staff member MAY re-route the item instead (ER-12).

#### Scenario: A biller answers a billing question
- **WHEN** a biller replies to an item in the biller lane
- **THEN** the reply is relayed with lane `biller`

#### Scenario: A front-desk member answers a billing question
- **WHEN** a front-desk staff member tries to reply to an item in the biller lane
- **THEN** the reply is refused, and they may re-route the item

#### Scenario: Anyone tries to answer a clinical item
- **WHEN** an office manager tries to reply to a clinical item
- **THEN** the reply is refused, because no openPractice lane answers clinical items

### Requirement: ER-12 Misfiled items are re-routed, and mixed questions are split, so no clinical part stays in openPractice
Staff MAY re-route a misfiled item (ruling 44). Every re-route SHALL keep the item owned and SHALL never lower its urgency. Re-routing between administrative lanes is tier 2.

When staff or an agent find that an administrative item is clinical, the rules SHALL route it as clinical at once (design call DC-14). openPractice SHALL then:
- hand it off to openChart by HealthLinc's message reference, with origin `recategorized_from_administrative`;
- remove the stored question text from openPractice, with a removal event that holds no PHI;
- tell HealthLinc with `inquiry.recategorized`.

A mixed question SHALL be split so that no clinical part stays in openPractice. HealthLinc's AI splits a mixed question at intake into parts, each with its own reference. When staff find a mixed item later, openPractice SHALL route the item's HealthLinc reference to openChart as clinical, remove the stored text, and ask HealthLinc to resubmit only the administrative part.

openPractice staff cannot judge content they never see. A clinical item SHALL therefore move to administration only when openChart returns it as `returned_not_clinical`; HealthLinc then resubmits it as administrative. Backup copies of removed text age out under backup retention, and the removal is not described beyond that scope.

#### Scenario: An administrative item turns out to be clinical
- **WHEN** a front-desk staff member flags an administrative item as clinical
- **THEN** it is handed to openChart by reference, its stored text is removed, and HealthLinc receives `inquiry.recategorized`

#### Scenario: A mixed question is found after intake
- **WHEN** a biller finds that a billing item also asks about a symptom
- **THEN** the item's reference goes to openChart as clinical, the stored text is removed, and HealthLinc is asked to resubmit only the billing part

#### Scenario: A clinician returns an item as administrative
- **WHEN** openChart reports `returned_not_clinical` for a handoff
- **THEN** HealthLinc is asked to resubmit it as administrative, and the item stays owned meanwhile

#### Scenario: A re-route would lower urgency
- **WHEN** an `urgent` item is re-routed to another lane whose default is `routine`
- **THEN** the item keeps `urgent`

### Requirement: ER-13 Items route at every hour, with on-call cover
openPractice SHALL route items at every hour. Clinical items SHALL hand off to openChart at any hour; openChart owns after-hours clinical cover in its queue. openPractice SHALL keep a practice-hours calendar and an on-call roster. For every hour, the roster names the on-call clinician and the administrative cover (change A WI-05).

ER-09's fallback SHALL alert the on-call clinician for the hour. When no on-call clinician is rostered for the hour, the fallback SHALL alert the medical director and the office manager instead, and SHALL still instruct HealthLinc to show emergency guidance (fail closed). Routine administrative items received after hours SHALL wait for the lane's next opening, and their clocks SHALL show that. Urgent administrative items SHALL go to the administrative cover at once. A practice SHALL NOT enable clinical routing until the on-call roster covers every hour or names the fallback (design call DC-27).

#### Scenario: An urgent message at night
- **WHEN** an urgent clinical item arrives at 02:00 and openChart does not acknowledge it in time
- **THEN** the rostered on-call clinician and the office manager are alerted, and HealthLinc shows its emergency guidance

#### Scenario: Nobody is on call
- **WHEN** the fallback runs for an hour with no on-call clinician rostered
- **THEN** the medical director and the office manager are alerted, and HealthLinc shows its emergency guidance

#### Scenario: A routine question at night
- **WHEN** a routine administrative item arrives after hours
- **THEN** it waits for the lane's next opening, and its clock shows that opening

### Requirement: ER-14 No escalation is ever unowned
Every routed item SHALL have exactly one owner at all times, and the owner SHALL be a roster entry (change A WI-02, AM-06). Until openChart accepts a clinical handoff, the office manager SHALL own its routing record. After acceptance, openChart owns the clinical queue, and the office manager owns any alert. An administrative item SHALL be owned by its lane owner, or by Hermes as a roster entry while it drafts, with Hermes's sponsoring human accountable (AM-04). Reassignment and absence cover SHALL follow WI-05. When an owner is offboarded (AM-05), absent or unavailable, the item SHALL move to cover at once. No item SHALL close without a recorded outcome.

#### Scenario: An owner is offboarded
- **WHEN** the biller who owns an open item is offboarded the same day
- **THEN** the item moves at once to the biller lane's cover, or to the office manager when no cover exists

#### Scenario: Someone closes an item with no outcome
- **WHEN** a user tries to close an item without recording an outcome
- **THEN** the close is refused and the item stays owned

### Requirement: ER-15 Paused agents or an unavailable Hermes send administrative items to staff
When the practice-wide pause switch is on (change A AO-09), or Hermes is unavailable, every administrative item SHALL route to staff by lane (ER-11). Drafts awaiting approval SHALL stay as proposals that a human may approve, change or reject. Nothing Hermes drafted SHALL be sent automatically while paused. Clocks SHALL keep running, and no item SHALL be lost. Clinical routing and the urgent fallback SHALL be unaffected, because they never depend on an agent.

#### Scenario: The pause switch is turned on
- **WHEN** an authorized person turns on the practice-wide pause switch
- **THEN** new administrative items go straight to staff lanes, and existing drafts wait for human action

#### Scenario: Hermes cannot be reached
- **WHEN** Hermes does not respond to a drafting request within the retry policy
- **THEN** the item routes to its lane's staff with its clock unchanged

### Requirement: ER-16 No PHI in routing records, alerts, logs, keys or diagnostics
Routing records, logs, errors, diagnostics, alerts, push text, idempotency keys and coded reasons on both contracts MUST NOT contain names, dates of birth, contact details, question or reply text, or clinical content (EB-12; change A AO-12 and AH-04). Diagnostics SHALL reference items only by routing id, `message_id`, `gateway_link_id` and `link_id`. Alerts SHALL name only the routing id, the urgency and a coded cause.

#### Scenario: An acknowledgment is overdue
- **WHEN** the office manager is alerted that an acknowledgment is overdue
- **THEN** the alert names the routing id, the urgency and the cause `clinical_ack_overdue`, and nothing else

#### Scenario: A handoff fails
- **WHEN** a handoff is rejected and a diagnostic bundle is made
- **THEN** the bundle holds the ids and the coded reason, and no patient detail

### Requirement: ER-17 The handoff boundary is covered by contract tests
The handoff boundary SHALL be covered by contract tests, written first (constitution Principle III), that validate every message against the clinical-handoff canonical schema at its recorded digest. They SHALL cover:
- the reference-only payload, and refusal of any content field;
- acknowledgment, rejection and retry;
- every alert trigger, and the urgent fallback with its guidance instruction;
- urgency raises, and the absence of any lowering;
- the link precondition;
- re-routing and splitting, with text removal;
- paused agents;
- no PHI in diagnostics.

The tests SHALL run against openChart and HealthLinc contract doubles, with synthetic data only.

#### Scenario: CI runs the handoff suite
- **WHEN** CI runs the handoff contract tests
- **THEN** they pass against contract doubles with synthetic data only

#### Scenario: A test adds a content field
- **WHEN** a test builds a `handoff.offer` that carries message text
- **THEN** schema validation fails, and the test expects that failure
