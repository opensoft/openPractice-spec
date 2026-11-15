## ADDED Requirements

### Requirement: ER-01 openPractice keeps a routing record only, with no clinical content
openPractice SHALL keep a routing record for every accepted inquiry, and that record SHALL hold only (ruling 34):
- the routing id and the gateway link;
- whether the patient or a representative submitted it;
- the submitted, routed, acknowledgment and closing times;
- the proposed and applied category;
- the proposed and applied urgency, with every raise and its source;
- the current owner and lane;
- HealthLinc's request reference, the conversation source and conversation reference (ruling 51), and the reply-path reference;
- the split-group reference and, for a continuation, the routing id it continues (ER-12);
- the handoff state, including the time openChart reported a clinician's acknowledgment (ER-19), and the clock policy version.

The continuation reference only links two items. It SHALL NOT exempt either from any rule: an item routed as clinical is governed by ER-07 to ER-09 whether or not it continues another (ER-12).

For a clinical item, the routing record SHALL be all that openPractice keeps. No question text, reply text or other clinical content SHALL be stored in openPractice, in line with constitution Principle IV. Administrative question and reply text is kept only under PG-10 and PG-11.

#### Scenario: A clinical question is routed
- **WHEN** a clinical inquiry is routed to openChart
- **THEN** openPractice's routing record holds the references, times, category, urgency and owner, and no text

#### Scenario: Someone tries to attach clinical content
- **WHEN** a user or integration tries to attach a note or message text to a clinical routing record
- **THEN** the attempt is refused and nothing is stored

### Requirement: ER-02 HealthLinc's coach, medXfactory's medical AI or the patient's red-flag checklist proposes category and urgency, and openPractice's fixed rules apply them
openPractice SHALL take the category and urgency that HealthLinc's coach or medXfactory's medical AI proposes, or that HealthLinc's red-flag checklist yields for a message written straight to the doctor, and apply them through openPractice's routing rules (rulings 44, 51 and 71). The inquiry names its proposer as `proposal_source`: `coach_triage`, `medical_ai_triage` or `patient_checklist`. The two AIs use the conservative defaults: an uncertain category is proposed as clinical, an uncertain urgency as urgent, and urgency is never lowered. The checklist proposes `urgent` on any red flag, with HealthLinc's emergency guidance, `routine` with no red flag, and `priority` when the patient skips it (tension T-12); the patient may raise the result but never lower it, and the message is clinical. The rules SHALL be a fixed, versioned rule set that ships with openPractice. Practice configuration SHALL be limited to staff lanes, practice hours and the on-call roster (FD-1 as amended by ruling 38; design call DC-17).

The rules SHALL apply these defaults (design call DC-14):
- an unknown category routes as clinical;
- an unknown urgency applies as `urgent`;
- when any proposal marks a part clinical, that part routes as clinical;
- the applied urgency is the highest of every urgency proposal received, whether from HealthLinc's coach, medXfactory's medical AI, the patient's checklist, a staff member or an agent, and of any minimum the rules set.

Applying the rules is automatic. It makes no decision for the patient; it only delivers the item to an owner (tension T-6). Each routing decision SHALL record the rule-set version and the inputs it used.

#### Scenario: A routine billing question
- **WHEN** HealthLinc proposes `administrative`, topic `billing_statement` and `routine`
- **THEN** the rules route the item to the biller lane at `routine` and record the rule-set version

#### Scenario: An agent proposes a higher urgency
- **WHEN** an agent proposes `urgent` for an item applied at `routine`
- **THEN** the rules apply `urgent` at once, record the agent as the source, and no approval is awaited

#### Scenario: A direct message with a red flag
- **WHEN** a patient writes straight to the doctor, answers the red-flag checklist with a red flag, and HealthLinc submits it with `proposal_source` `patient_checklist` and `urgent`
- **THEN** openPractice applies `urgent` and routes it as clinical at once, and HealthLinc has already shown its emergency guidance

#### Scenario: A direct message with no red flag
- **WHEN** the checklist shows no red flag and HealthLinc proposes `routine` with `patient_checklist`
- **THEN** openPractice routes the item as clinical at `routine`

#### Scenario: A skipped checklist
- **WHEN** a patient skips the red-flag checklist and HealthLinc proposes `priority` with `patient_checklist`
- **THEN** openPractice routes the item as clinical at `priority`, never lower

#### Scenario: A patient tries to lower a checklist urgency
- **WHEN** a patient resubmits the same direct message with a lower urgency than the checklist produced or than the item already has
- **THEN** the applied urgency stays as it was, and the lower proposal is recorded

#### Scenario: A practice tries to author a rule
- **WHEN** an administrator tries to add a routing rule beyond lanes, hours and the on-call roster
- **THEN** the change is refused

### Requirement: ER-03 Urgency is never downgraded
openPractice MUST NOT lower an item's applied urgency, whether by rule, staff action, agent action or a later proposal from HealthLinc's coach or medXfactory's medical AI. Urgency MAY only be raised. Each raise SHALL be recorded with its source and time. A raise on a clinical item SHALL be sent to openChart as `handoff.urgency_raised`, with source `coach_triage`, `medical_ai_triage`, `patient_checklist`, `staff`, `agent_proposal` or `routing_rule`. A patient's own raise through HealthLinc's checklist uses `patient_checklist` (ruling 71). These two triage sources replace the earlier `healthlinc_triage`, which ruling 51 split; contract 1.0 is unratified, so no published value is withdrawn (design call DC-44). Neither contract SHALL contain a message that lowers urgency.

A clinician MAY raise an item's urgency in openChart. openChart SHALL report it as `handoff.clinician_urgency_raised`, with the new urgency, the time, openChart's new `respond_by` and the clinician's reference, and openPractice SHALL acknowledge it with `message.ack` (design call DC-42). openPractice SHALL then:
- record the raise, with the clinician reference as its source;
- shadow the new `respond_by`, never later than its current shadow deadline;
- for a raise to `urgent`, run the urgent path for that item: ER-09's tracked fallback on `clock_expired` or on silence past the new `respond_by`, including `guidance.instruct` to HealthLinc so the patient gets emergency guidance if the deadline is missed;
- for a raise to `priority`, apply the priority alerting of ER-07.

A clinician's raise to an urgency at or below the applied urgency SHALL be recorded and SHALL change nothing; openPractice never lowers urgency.

#### Scenario: A staff member tries to lower urgency
- **WHEN** a staff member tries to change an `urgent` item to `routine`
- **THEN** the change is refused and the item stays `urgent`

#### Scenario: HealthLinc later proposes a lower urgency
- **WHEN** HealthLinc resubmits a split part with a lower urgency than the item already has
- **THEN** the applied urgency stays as it was, and the lower proposal is recorded

#### Scenario: Urgency is raised on a clinical item
- **WHEN** an item already handed to openChart is raised from `priority` to `urgent`
- **THEN** openChart receives `handoff.urgency_raised` with the new acknowledgment and response times

#### Scenario: A clinician raises urgency in openChart
- **WHEN** openChart sends `handoff.clinician_urgency_raised` raising a routine item to `urgent` with a new `respond_by`
- **THEN** openPractice records the raise, acknowledges it, shadows the new deadline and treats the item as urgent

#### Scenario: The clinician's new deadline passes in silence
- **WHEN** the new `respond_by` of a clinician-raised urgent item passes with neither `responded` nor `clock_expired`
- **THEN** ER-09's fallback runs: the office manager is alerted, the on-call clinician gets a tracked alert, and HealthLinc receives `guidance.instruct` with cause `clinical_response_overdue`

#### Scenario: A clinician raise would lower urgency or extend the deadline
- **WHEN** a clinician raise names `priority` for an item already `urgent`, or a `respond_by` later than openPractice's current shadow deadline
- **THEN** the item stays `urgent` and the earlier deadline stands, and the raise is recorded

### Requirement: ER-04 Clinical items are handed to openChart under one versioned handoff contract
openPractice SHALL hand every clinical item to the patient's linked openChart, or other linked EHR, under one versioned contract, `openpractice-clinical-handoff/1.0` (rulings 34, 36 and 43). The canonical schema SHALL be `openpractice-clinical-handoff-1.0.schema.json` in this change's `contracts/` directory, identified by the SHA-256 digest of its bytes. Both sides SHALL validate every message against the schema with the recorded digest. Any schema change SHALL produce a new digest, recorded in this change and in the openChart companion change before either side implements it.

The contract follows PG-01's versioning rules: a shared MAJOR version is compatible, additive changes increment MINOR, no sender sends a MINOR the receiver has not declared, and an unsupported MAJOR is refused with `unsupported_major_version`. The messages SHALL be `handoff.offer`, `handoff.ack`, `handoff.urgency_raised`, `handoff.clinician_urgency_raised` (ER-03), `handoff.status`, `clinical_need.marker` (ER-18) and `message.ack`.

#### Scenario: A valid offer is accepted
- **WHEN** openPractice sends a `handoff.offer` that validates against the schema with the recorded digest
- **THEN** openChart accepts it with `handoff.ack` and starts its response clock

#### Scenario: An offer fails the schema
- **WHEN** openChart receives an offer that fails the canonical schema
- **THEN** it rejects the offer with `schema_invalid`, and openPractice alerts the office manager under ER-07

### Requirement: ER-05 The handoff is reference-only and carries exactly the stated fields
A `handoff.offer` SHALL carry exactly these fields, and no others (design call DC-2):
- the routing id, as `handoff_id`;
- the identity state, `linked`, `link_in_review` or `unlinked` (ER-06);
- for `linked` and `link_in_review`, the EB-01 `link_id` and the EHR patient triple from that link;
- for `unlinked`, no link and no EHR triple, but an `unlinked_identity` holding only the legal name, date of birth and administrative sex, a minimal subset of the `openpractice-ehr-patient-link/1.0` snapshot (`define-mvp-practice-foundation`, SHA-256 `76f465e144893000de4bd2240adb9f547899e2e8100315f698c3cc6cb885b802`), with the same shapes;
- HealthLinc's request reference and reply-path reference;
- where the conversation lives, as `conversation_source`: `healthlinc` for a conversation HealthLinc holds (a coach conversation, a direct message to the doctor or an administrative thread) or `medxfactory` for a medical-AI conversation, and the `conversation_ref` in that system (ruling 51);
- the category `clinical` and the urgency;
- the submitted, routed, acknowledge-by and respond-by times;
- the clock policy reference;
- the origin, and an opaque split-group reference for a split question.

It MUST NOT carry the message body, question text, reply text, a medical-AI transcript, attachments or any other clinical content, and the schema SHALL refuse any other field. openChart SHALL fetch the conversation by reference from whichever system holds it, HealthLinc or medXfactory, through `openchart-conversation-retrieval/1.0`, the neutral conversation-retrieval contract that openChart owns in its change `define-clinical-message-inbox` and that HealthLinc and medXfactory implement (ruling 69). The clinician's reply SHALL go from openChart to HealthLinc over openChart's reply path, never through openPractice. Both are companion-change items.

#### Scenario: An offer is built
- **WHEN** openPractice builds a `handoff.offer`
- **THEN** it contains only the listed references, codes and times, and no text

#### Scenario: An offer for a medical-AI conversation
- **WHEN** openPractice offers an item whose conversation lives in medXfactory
- **THEN** the offer carries `conversation_source` `medxfactory` and a `medxfactory` conversation reference, and no text

#### Scenario: openChart cannot fetch the message
- **WHEN** openChart reports `content_unavailable` for a handoff
- **THEN** openPractice alerts the office manager, and runs ER-09's fallback if the item is urgent

### Requirement: ER-06 Every handoff states its identity state, and urgent and priority items are offered even when the link is uncertain
openPractice SHALL offer a handoff with `identity_state` `linked` when the patient has an active EB-01 link to the EHR that is not in identity review (EB-05) and not under a demographic hold (EB-08) (design call DC-24). openPractice MUST NOT guess a chart in any state.

For an item whose applied urgency is `urgent` or `priority`, uncertainty about the chart SHALL NOT stop the offer (design calls DC-39 and DC-40):
- On a link in identity review or under a demographic hold, openPractice SHALL still offer the handoff, with `identity_state` `link_in_review`, the link and the EHR triple. One state covers both cases, because neither EB-05 nor openChart's PL-08 distinguishes them (tension T-11).
- For a patient with no active chart link, openPractice SHALL still offer the handoff, with `identity_state` `unlinked`, no link and no EHR triple, and an `unlinked_identity` limited to the patient's legal name, date of birth and administrative sex from the registry (ER-05).
- openChart accepts such an offer as an unfiled item with the identity uncertainty shown (its companion requirement MI-18), and never files it to a chart or creates a link from it automatically.
- At the same time, openPractice SHALL make the item a clinical routing exception owned by the office manager, holding no clinical content, so that a human resolves the identity. For an urgent item it SHALL also run ER-09's fallback at once. For a priority item it SHALL at once send ER-09's tracked on-call alert, without the emergency-guidance instruction (design call DC-30), and ER-07's priority alerting then applies.

A routine item on a link in review or under a hold, or for a patient with no active chart link, SHALL NOT be offered. It SHALL become a clinical routing exception owned by the office manager, holding no clinical content, as before. openChart's reject reason `link_in_review` therefore applies only to routine offers.

When a patient is linked to more than one EHR, the item SHALL go to the EHR the practice configures for clinical messaging; with no such configuration, it becomes an exception, and an urgent or priority item is offered `unlinked` to that configured EHR when one exists.

#### Scenario: The patient has an active link
- **WHEN** a clinical item arrives for a patient with an active EB-01 link that is not in review
- **THEN** openPractice offers the handoff to that EHR with `identity_state` `linked`

#### Scenario: An urgent item on a link in identity review
- **WHEN** an urgent clinical item arrives for a patient whose EHR link is in identity review
- **THEN** openPractice offers the handoff with `identity_state` `link_in_review`, the office manager owns the identity exception, and ER-09's fallback runs at once

#### Scenario: A priority item on a link under a demographic hold
- **WHEN** a priority clinical item arrives for a patient whose link is under a date-of-birth hold
- **THEN** openPractice offers the handoff with `identity_state` `link_in_review`, the office manager owns the identity exception, and the on-call clinician gets a tracked alert

#### Scenario: An urgent item for a patient with no chart link
- **WHEN** an urgent clinical item arrives for a patient with no active EB-01 link
- **THEN** openPractice offers the handoff with `identity_state` `unlinked`, no link, and only the legal name, date of birth and administrative sex, and ER-09's fallback runs at once

#### Scenario: A routine item on a link in review
- **WHEN** a routine clinical item arrives for a patient whose EHR link is in identity review
- **THEN** no handoff is offered, and the office manager owns a clinical routing exception with no clinical content, under a clock

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

Each alert SHALL become work owned by the office manager (WI-02), and SHALL also reach the office manager's mobile approver when one is registered (MA-02). For an item whose applied urgency is `priority`, each of these events SHALL also send the tracked on-call alert of ER-09 to the on-call clinician, without the emergency-guidance instruction (design call DC-30). openPractice SHALL retry an unacknowledged offer with the same `message_id`. An alert SHALL never cancel or withdraw the handoff. openChart's `returned_not_clinical` status SHALL re-route the item under ER-12.

#### Scenario: openChart acknowledges and responds in time
- **WHEN** openChart accepts an offer before `ack_by` and reports `responded` before `respond_by`
- **THEN** no alert is raised and the routing record closes

#### Scenario: No acknowledgment arrives
- **WHEN** `ack_by` passes with no accepted acknowledgment
- **THEN** the office manager is alerted, the offer is retried with the same `message_id`, and the item stays owned

#### Scenario: The response deadline passes silently
- **WHEN** `respond_by` passes and openChart has reported neither `responded` nor `clock_expired`
- **THEN** openPractice treats the clock as expired and alerts the office manager

#### Scenario: A priority item is not acknowledged
- **WHEN** `ack_by` passes for a `priority` item with no accepted acknowledgment
- **THEN** the office manager is alerted, the on-call clinician receives a tracked alert, and no emergency-guidance instruction is sent

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
An urgent escalation MUST NOT be blocked, delayed by a payment hold or any other status, or dropped (rulings 41 and 43). openPractice SHALL accept and route every inquiry whose applied urgency is above `routine` from every linked patient, active or not (PG-03, PG-04, PG-10). Where clinical routing is not enabled at all, PG-10's defensive refusal applies instead: the patient sees emergency guidance at once, and the office manager owns an exception.

openPractice SHALL run the fallback at once when the item is urgent and any of these happens:
- openChart is unreachable;
- openChart rejects the handoff;
- openChart reports `content_unavailable`;
- no acknowledgment arrives within the urgent window;
- openChart reports `clock_expired`;
- neither `responded` nor `clock_expired` arrives by `respond_by`;
- ER-06 offers the item with `identity_state` `link_in_review` or `unlinked`, or makes it an exception.

The fallback SHALL:
- alert the office manager;
- send a tracked alert to the on-call clinician for the hour (ER-13);
- send HealthLinc `guidance.instruct` with `show_emergency_guidance` and a coded cause, so HealthLinc shows its own in-app emergency guidance. A `clock_expired` report, or silence past `respond_by`, uses cause `clinical_response_overdue`.

A tracked alert SHALL go through the recipient's registered mobile approver (MA-02, MA-03) and through a second channel the practice sets in planning, with text that carries no PHI (ER-16). openPractice SHALL record each alert's delivery state and the recipient's acknowledgment. When the recipient does not acknowledge within the alert-acknowledgment window, openPractice SHALL re-send the alert and escalate it to the medical director, and SHALL keep re-sending to both until one of them acknowledges. The office manager SHALL be told of each escalation. The window and the re-send interval come from the routing-clock policy (ER-08); until it states them, both are 5 minutes (design call DC-29). Acknowledging an alert never closes the item.

openPractice SHALL keep retrying the handoff while the fallback runs. openPractice MUST NOT give clinical advice: the instruction is a code, and the guidance is HealthLinc's. The pause switch (AO-09) and an unavailable Hermes SHALL NOT affect urgent routing, which never depends on an agent.

An urgent inquiry that openPractice rejects SHALL raise an exception owned by the office manager that holds no PHI (design call DC-35). This covers a schema failure; `link_revoked`, `link_unknown` and `unauthorized`; and an inquiry on a link that is still `pending_review`, which is rejected with `link_pending_review`. When the inquiry can be parsed, openPractice SHALL also send HealthLinc `guidance.instruct` for its `message_id` with cause `urgent_inquiry_rejected`. HealthLinc shows its emergency guidance for any rejected urgent inquiry (companion item), so the patient still gets it.

#### Scenario: A held patient sends an urgent message
- **WHEN** a patient on a payment hold, or with a deactivated record, submits an urgent clinical inquiry
- **THEN** openPractice accepts it and hands it off with no delay

#### Scenario: openChart is down
- **WHEN** openChart does not acknowledge an urgent handoff within the urgent window
- **THEN** the office manager and the on-call clinician are alerted, HealthLinc is told to show its emergency guidance, and the handoff is retried

#### Scenario: An urgent inquiry is rejected as invalid
- **WHEN** an inquiry proposed as urgent fails the gateway schema
- **THEN** the office manager owns an integration exception that holds no PHI, and HealthLinc applies its emergency guidance

#### Scenario: openChart reports an expired clock on an urgent item
- **WHEN** openChart reports `clock_expired` for an urgent handoff
- **THEN** the office manager is alerted, the on-call clinician receives a tracked alert, and HealthLinc receives `guidance.instruct` with cause `clinical_response_overdue`

#### Scenario: An urgent item passes its response deadline in silence
- **WHEN** `respond_by` passes for an urgent handoff with neither `responded` nor `clock_expired`
- **THEN** the fallback runs, with cause `clinical_response_overdue`

#### Scenario: The on-call clinician does not acknowledge
- **WHEN** the on-call clinician does not acknowledge a tracked alert within the alert-acknowledgment window
- **THEN** the alert is re-sent and escalated to the medical director, re-sending continues until one acknowledges, and the office manager is told

#### Scenario: An urgent inquiry on a revoked or unconfirmed link
- **WHEN** an urgent inquiry arrives on a revoked link, or on a link that is still `pending_review`
- **THEN** it is rejected with `link_revoked` or `link_pending_review`, the office manager owns an exception that holds no PHI, and HealthLinc receives `guidance.instruct` with cause `urgent_inquiry_rejected`

### Requirement: ER-10 Hermes answers administrative items only under change A's ladder and scopes, starting at propose
Hermes SHALL answer an administrative item only in one of two forms, each at FD-5 tier 3 because it is outbound, each governed by the autonomy ladder and scopes of change `define-actors-agents-and-work-inbox` (AO-03, AO-04), and each starting at `propose` (rulings 34, 37, 45 and 46):
- **A read-only answer**, one of the `patient_answer.*` action types of `openpractice-agent-operations/1.0`. Change A's AH-05 exception 1 governs it, and this change does not restate its conditions. A practice may switch an answer type on only to `act_after_delay`, under every condition AH-05 exception 1 states. openPractice relays the server-assembled reply with the answer-type id and version (PG-11). In the MVP, this is how Hermes answers routine administrative questions.
- **A drafted reply**, the action type `gateway.administrative_reply`. Hermes drafts the reply with evidence and reasoning references (AO-07), and a named human in the item's lane approves, changes or rejects it in the work inbox (WI-03) before it is relayed. Every draft is derived from the patient's message, which is untrusted content, so it never rises above `propose` (AH-05). It needs a governed MINOR version of `openpractice-agent-operations` first (design call DC-22).

An item that no read-only answer fits, or that is not eligible under AH-05 exception 1, routes to staff by lane (ER-11) until the drafted-reply MINOR exists. An item with `human_requested` set, and any follow-up on an item that already received a read-only answer, SHALL route to staff.

Before Hermes processes any administrative question, change A's AH-01 gate SHALL be met: verified BAAs with the agent operator and with each model provider (AH-02), minimum necessary under AI-23 recipient profiles (AH-03), and no PHI in agent logs (AH-04). Hermes reads only the linked patient's data (AO-03). Before it drafts or answers, Hermes SHALL screen the item for clinical content; any hit makes it propose a re-route, which the rules apply as clinical (ER-12, DC-14). Hermes MUST NOT answer a clinical item or the clinical part of a mixed one. Hermes's administrative patient messages are kept separate from clinical ones under medXfactory's local policy (ruling 68): they follow ruling 45's guardrails, as change A's AH-05 exception 1 encodes them, and need no clinical review, while consent and data-sharing conditions still apply. openPractice checks the patient's consent to AI-authored administrative messaging itself, before it schedules an answer and again before it sends it, and cancels every pending answer to that patient when consent is withdrawn (change A AH-06; ruling 97). Hermes works an item through change A's work-item operations (AO-13): it reads the administrative text openPractice holds for the item, proposes a clinical re-route or an urgency raise, which the rules apply at once, releases the item to a human owner, or cancels its own pending answer. Hermes acts only as a roster entry with an openWallet ID and a sponsoring human (AM-04).

#### Scenario: Hermes gives a read-only answer
- **WHEN** a patient asks on their own link about office hours, the item is eligible under change A's AH-05 exception 1, and the practice has switched the hours answer type on at `act_after_delay`
- **THEN** openPractice assembles the reply, holds it for the lane's cancel window, and relays it with author kind `agent`, rung `act_after_delay` and the answer-type id and version, and the item stays open

#### Scenario: The patient asks for a person
- **WHEN** a patient's follow-up arrives with `human_requested` set after a read-only answer
- **THEN** the item routes to the lane's staff, and no further read-only answer is sent

#### Scenario: Hermes drafts an answer that staff approve
- **WHEN** Hermes drafts a reply at `propose` and a front-desk staff member approves it
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
- cancel every pending agent answer on that item before it is sent, because an agent never answers a clinical item (ER-10), whoever found the item clinical;
- remove the stored question text from openPractice, with a removal event that holds no PHI;
- tell HealthLinc with `inquiry.recategorized`.

When clinical routing is missing or disabled, an item found clinical cannot be handed to openChart. openPractice SHALL still cancel every pending agent answer on it and remove its stored text, and in place of the handoff and `inquiry.recategorized` SHALL apply PG-10's defensive handling: send HealthLinc `guidance.instruct` for the item's `message_id` with cause `clinical_routing_unavailable`, and raise an exception owned by the office manager that holds no PHI. This applies to every administrative item found clinical, a continuation included. For a mixed item, openPractice SHALL still keep the lane reminder of ruling 106 below, so that the administrative part is not lost: no resubmission is asked of HealthLinc, and the lane's owner contacts the patient for the administrative part, which the patient may send as a new question (design call DC-63, PROPOSED).

A mixed question SHALL be split so that no clinical part stays in openPractice. HealthLinc splits a mixed question at intake into parts, each with its own reference. When staff or an agent find a mixed item later, openPractice SHALL route the item's HealthLinc reference to openChart as clinical, cancel every pending agent answer on the item, remove the stored text, and ask HealthLinc, in one `inquiry.recategorized`, to resubmit only the administrative part. Its text is deleted from openPractice (ruling 106).

**The administrative part comes back as the same question (ruling 106).** A resubmission that sets `recategorizes_routing_id` continues the item that routing id names; it is not a new question. It carries its own HealthLinc reference, so PG-10's same-reference rule gives it its own routing id, and it needs no message beyond `inquiry.submitted` and `inquiry.ack`. openPractice SHALL accept it as a continuation only when the routing id names an item on the same `gateway_link_id` and one of these holds (design call DC-54, PROPOSED):
- openPractice asked HealthLinc to resubmit that item's administrative part, or to resubmit it as administrative after openChart returned it, and that request's reminder is open. Accepting the continuation closes the reminder, so each request admits one continuation;
- openPractice handed the item to openChart as clinical, so openChart may have asked HealthLinc to split it, no resubmission request for the item is open, and no continuation of either kind has been accepted for the item since that handoff.

Otherwise openPractice SHALL reject the inquiry with `routing_unknown`, link it to nothing and close no reminder, and HealthLinc tells the patient that the part was not delivered and offers to send it as a new question (companion item). The lane's owner MAY reopen a reminder that a continuation closed, when the continuation did not carry the part that was asked for, with a coded reason; the reopening is audited (PR-10), and a reopened reminder admits one more continuation and falls due 48 hours after it is reopened, as DC-55 sets for a new one (design call DC-54, PROPOSED). A retry of an accepted continuation, with the same `message_id` or HealthLinc reference, returns its routing id (PG-10). For an accepted continuation, openPractice SHALL:
- link the continuation to the original routing record by that routing id, and record the continuation's split-group reference on both records (ER-01);
- order the continuation in its lane by the original item's submitted time, and report that time wherever the item's submission is shown, while the continuation's own response clock starts when it arrives (design call DC-54, PROPOSED). A continuation accepted under the second case keeps the original's submitted time only when it arrives while openChart's handoff of the original is open, or within 7 days after that handoff closed, a routing-clock policy value (ER-08; DC-54, PROPOSED). Arriving later, it is still accepted, and is ordered and shown by its own arrival time;
- send the patient no notice for it. HealthLinc shows the continuation under the original question, with no second "question received" notice (companion item);
- apply the urgency HealthLinc proposes for it, never lower (ER-02, ER-03). ER-07 to ER-09 govern clinical items handed to openChart. While a continuation stays administrative they SHALL NOT apply to it: ER-09's urgent path, with its emergency guidance to the patient, does not run for it, and it is not refused for `clinical_routing_unavailable` (PG-10, ER-13). Once a continuation is itself routed as clinical under this requirement, ER-07 to ER-09 SHALL apply to it as to any clinical item, and its being a continuation exempts it from nothing (ER-01). The clinical part keeps its urgency in openChart, and nothing lowers it (design call DC-61, PROPOSED).

The same applies to a resubmission after openChart returns an item as `returned_not_clinical`, and to one HealthLinc makes when openChart asks it to split an item, whose handoff id is the routing id.

**A reminder on the lane (ruling 106).** When openPractice asks HealthLinc to resubmit an administrative part, or to resubmit a returned item as administrative, it SHALL keep a reminder on the original item's lane, or for an item openChart returned, on the office manager's lane, which covers every administrative lane, until the continuation arrives or a human closes the reminder with a coded reason, so the administrative part is never lost. The reminder holds no text: only the routing id, the lane, its owner, the time of the request and the original's coded topic hint when it had one. It is owned like any work item (AM-06). Its clock and alert are design calls under ruling 106 (DC-55, PROPOSED): the reminder is due 48 hours after the request; when that passes, openPractice alerts the lane's owner, who may contact the patient through the practice's approved channel, and the reminder stays open. A continuation closes it on arrival. When the patient says no part is needed, a human closes it.

**The patient confirms the excerpt (companion item; design call DC-56, PROPOSED).** HealthLinc MAY ask the patient to confirm the administrative excerpt before it sends it.

openPractice staff cannot judge content they never see. A clinical item SHALL therefore move to administration only when openChart returns it as `returned_not_clinical`; HealthLinc then resubmits it as administrative. Backup copies of removed text age out under backup retention, and the removal is not described beyond that scope.

**Copies outside openPractice (ruling 104).** Where the removed text had reached the agent layer, the agent operator SHALL delete its copies, in the agent's memory and in the caches and logs under its control, and return a confirmation, which openPractice SHALL record with no PHI. A model provider keeps nothing but the short-lived in-memory cache that change A AH-02 allows (ruling 133), which expires within the limit recorded for its zero-data-retention arrangement, so openPractice SHALL record, in place of a deletion confirmation, the agent's declared model path (change A AH-02, D-32) and the reference to the verified zero-data-retention arrangement under which the text was processed, which states any such cache and its limit. The deadline, and what follows a missing confirmation, are design calls under ruling 104 (DC-57, PROPOSED): the operator's confirmation is due within 24 hours of the request. When it has not arrived by then, openPractice raises an exception owned by the office manager and alerts the agent's sponsor (AM-04), with no PHI. When it has not arrived within 72 hours, openPractice treats it as a lapse of the operator's controls under change A AH-01, suspending PHI access for every agent on that operator's path until the confirmation is recorded. Until a later MINOR version of `openpractice-agent-operations` carries the request and the confirmation (change A Open Question 11), they travel through the channel the operator's agreement names: openPractice gives an authorized person the work of sending the request, and that person records the operator's receipt on the AH-02 pattern, with the item reference, the kinds of copy deleted, the time of deletion and who recorded it, and no PHI.

#### Scenario: An administrative item turns out to be clinical
- **WHEN** a front-desk staff member flags an administrative item as clinical
- **THEN** it is handed to openChart by reference, its stored text is removed, and HealthLinc receives `inquiry.recategorized`

#### Scenario: A mixed question is found after intake
- **WHEN** a biller finds that a billing item also asks about a symptom
- **THEN** the item's reference goes to openChart as clinical, the stored text is removed, HealthLinc is asked to resubmit only the billing part, and a reminder with no text stays on the biller lane

#### Scenario: The administrative part comes back
- **WHEN** HealthLinc resubmits the billing part with `recategorizes_routing_id` naming the original item
- **THEN** openPractice gives it its own routing id, links it to the original, orders it by the original's submitted time, closes the lane reminder, and the patient sees one question with no second notice

#### Scenario: A continuation names another patient's item
- **WHEN** a resubmission on patient A's link sets `recategorizes_routing_id` to a routing id on patient B's link
- **THEN** openPractice rejects it with `routing_unknown`, B's reminder stays open, and nothing is shown under B's question

#### Scenario: A second continuation for the same request
- **WHEN** a continuation already answered an item's resubmission request and closed its reminder, and another resubmission with a new HealthLinc reference names the same routing id while no other request for the item is open
- **THEN** openPractice rejects it with `routing_unknown` and links it to nothing, even though the item was handed to openChart as clinical, because a continuation has already been accepted since that handoff; HealthLinc tells the patient the part was not delivered and offers to send it as a new question

#### Scenario: A split, then a return
- **WHEN** openChart asks HealthLinc to split an item and its continuation C1 is accepted, and later openChart returns the rest as `returned_not_clinical`, and openPractice asks HealthLinc to resubmit it and opens a reminder
- **THEN** the resubmission is accepted as a continuation of the same item, and accepting it closes that reminder

#### Scenario: A wrong part closes the reminder
- **WHEN** a continuation on the same link closes a reminder, but it does not carry the billing part that was asked for
- **THEN** the lane's owner reopens the reminder with a coded reason, the reopening is audited, the reopened reminder falls due 48 hours later, and the billing part is accepted when it arrives

#### Scenario: An item is found clinical while clinical routing is off
- **WHEN** clinical routing is disabled, and a biller finds that an urgent billing continuation describes chest pain
- **THEN** it is not handed to openChart; its pending agent answers are cancelled and its stored text removed, HealthLinc receives `guidance.instruct` with cause `clinical_routing_unavailable`, and the office manager owns an exception that holds no PHI

#### Scenario: A mixed item is found while clinical routing is off
- **WHEN** clinical routing is disabled, and a biller finds that a billing item also asks about a symptom
- **THEN** HealthLinc receives `guidance.instruct` and the office manager owns an exception, and a reminder with no text stays on the biller lane until the lane's owner has contacted the patient for the billing part

#### Scenario: A continuation of a long-closed handoff
- **WHEN** a continuation names an item that was handed to openChart as clinical and whose handoff closed 30 days ago, and no request for the item is open
- **THEN** it is accepted, and it is ordered and shown by its own arrival time, not the original's

#### Scenario: The billing part of an urgent mixed item comes back
- **WHEN** the administrative part of an urgent mixed item is resubmitted at `urgent`
- **THEN** openPractice applies `urgent` to the continuation in its administrative lane, sends HealthLinc no emergency-guidance instruction for it, and the clinical part keeps its urgency in openChart

#### Scenario: An urgent billing continuation turns out to be clinical
- **WHEN** an urgent billing continuation turns out to describe chest pain, the biller flags it clinical, it is handed to openChart, and openChart does not acknowledge it within the urgent window
- **THEN** ER-09's fallback runs as for any urgent clinical item: the office manager and the on-call clinician are alerted, HealthLinc is told to show its emergency guidance, and the handoff is retried

#### Scenario: The administrative part does not come back
- **WHEN** 48 hours pass after the request and no continuation has arrived
- **THEN** the lane's owner is alerted, and the reminder stays open until the part arrives or a human closes it

#### Scenario: Misrouted text had reached the agent layer
- **WHEN** an item that Hermes had worked is found clinical and its stored text is removed
- **THEN** the agent operator deletes its copies and returns a confirmation, recorded without PHI, and for the model provider openPractice records the zero-data-retention arrangement under which the text was processed

#### Scenario: The operator does not confirm the deletion
- **WHEN** 24 hours pass after a deletion request without the operator's confirmation
- **THEN** the office manager owns an exception and the agent's sponsor is alerted, with no PHI; at 72 hours PHI access on that operator's path is suspended until the confirmation is recorded

#### Scenario: An item with a pending agent answer is found clinical
- **WHEN** staff flag an item clinical while a read-only answer on it waits in its cancel window
- **THEN** the answer is cancelled and never sent, and the item is handed to openChart by reference

#### Scenario: A clinician returns an item as administrative
- **WHEN** openChart reports `returned_not_clinical` for a handoff
- **THEN** HealthLinc is asked to resubmit it as administrative, and the item stays owned meanwhile

#### Scenario: A re-route would lower urgency
- **WHEN** an `urgent` item is re-routed to another lane whose default is `routine`
- **THEN** the item keeps `urgent`

### Requirement: ER-13 Items route at every hour, with on-call cover
openPractice SHALL route items at every hour. Clinical items SHALL hand off to openChart at any hour; openChart owns after-hours clinical cover in its queue. openPractice SHALL keep a practice-hours calendar and an on-call roster. For every hour, the roster names the on-call clinician and the administrative cover (change A WI-05).

ER-09's fallback SHALL send its tracked alert to the on-call clinician for the hour. When no on-call clinician is rostered for the hour, the fallback SHALL send the tracked alert to the medical director and alert the office manager instead, and SHALL still instruct HealthLinc to show emergency guidance (fail closed). Routine administrative items received after hours SHALL wait for the lane's next opening, and their clocks SHALL show that. Urgent administrative items SHALL go to the administrative cover at once. A practice SHALL NOT enable clinical routing until the on-call roster covers every hour or names the fallback (design call DC-27). Clinical and urgent inquiry intake SHALL NOT go live until clinical routing is enabled, and while it is missing or disabled PG-10's defensive refusal applies (design call DC-33), except to an administrative continuation accepted under ER-12, which goes to its administrative lane and, when urgent, to the administrative cover at once (design call DC-61, PROPOSED).

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
Every routed item SHALL have exactly one owner at all times, and the owner SHALL be a roster entry (change A WI-02, AM-06). Until openChart accepts a clinical handoff, the office manager SHALL own its routing record. After acceptance, openChart owns the clinical queue, and the office manager owns any alert. An administrative item SHALL be owned by its lane owner, or by Hermes as a roster entry while it drafts or prepares a read-only answer, with Hermes's sponsoring human accountable (AM-04). Hermes SHALL NOT decide its own draft: when Hermes submits a draft at `propose`, the item passes to a named human in its lane, who decides it (change A WI-03, where the decider is never the proposer). A read-only answer under change A's AH-05 exception 1 needs no decision, but the lane can cancel it during its delay window, and the item then stays open under the lane's human owner. Reassignment and absence cover SHALL follow WI-05. When an owner is offboarded (AM-05), absent or unavailable, the item SHALL move to cover at once. No item SHALL close without a recorded outcome.

#### Scenario: An owner is offboarded
- **WHEN** the biller who owns an open item is offboarded the same day
- **THEN** the item moves at once to the biller lane's cover, or to the office manager when no cover exists

#### Scenario: Hermes tries to approve its own draft
- **WHEN** Hermes, as the owner of an administrative item, tries to approve the reply it drafted
- **THEN** the approval is refused, and the item passes to a named human in its lane

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
Routing records, logs, errors, diagnostics, alerts, push text, idempotency keys and coded reasons on both contracts MUST NOT contain names, dates of birth, contact details, question or reply text, or clinical content (EB-12; change A AO-12 and AH-04). Diagnostics SHALL reference items only by routing id, `message_id`, `gateway_link_id` and `link_id`. Alerts SHALL name only the routing id, the urgency and a coded cause. An exception for a refused inquiry, which has no routing id, SHALL name only the `message_id`, the coded reason and the time.

#### Scenario: An acknowledgment is overdue
- **WHEN** the office manager is alerted that an acknowledgment is overdue
- **THEN** the alert names the routing id, the urgency and the cause `clinical_ack_overdue`, and nothing else

#### Scenario: A response is overdue
- **WHEN** the on-call clinician is alerted that an urgent response is overdue
- **THEN** the alert names the routing id, the urgency and the cause `clinical_response_overdue`, and nothing else

#### Scenario: A handoff fails
- **WHEN** a handoff is rejected and a diagnostic bundle is made
- **THEN** the bundle holds the ids and the coded reason, and no patient detail

### Requirement: ER-17 The handoff boundary is covered by contract tests
The handoff boundary SHALL be covered by contract tests, written first (constitution Principle III), that validate every message against the clinical-handoff canonical schema at its recorded digest. They SHALL cover:
- the reference-only payload, and refusal of any content field;
- acknowledgment, rejection and retry;
- every alert trigger, including `clock_expired` and silence past `respond_by`, and the urgent fallback with its guidance instruction;
- every identity state, the `unlinked_identity` subset, and the absence of an offer for a routine item on an uncertain link;
- clinician urgency raises: acknowledgment, the shadowed deadline, the urgent path, and no lowering;
- tracked alerts: delivery, acknowledgment, re-sending and escalation to the medical director, and the priority alert;
- the conversation source and reference;
- the clinical-need marker, its attribution and the urgent-origin check;
- urgency raises, and the absence of any lowering;
- the link precondition;
- re-routing and splitting, with text removal; a continuation accepted only for an item on the same link, one per resubmission request while its reminder is open, or one for an item handed to openChart as clinical when no request is open and no continuation has been accepted since that handoff, and otherwise rejected with `routing_unknown`; a reminder reopened by the lane's owner with a coded reason, due 48 hours after reopening; an item found clinical while clinical routing is off, handled as PG-10's defensive refusal; a continuation linked to the original, ordered by the original's submitted time, or by its own arrival time once the original's handoff has been closed longer than the policy period, sending the patient no notice, not running ER-09's urgent path while it stays administrative, and governed by ER-07 to ER-09 once it is routed as clinical; an administrative continuation not refused for `clinical_routing_unavailable`; the lane reminder, its clock and alert; the operator's deletion confirmation, its deadline, and the zero-data-retention reference recorded for a model provider (ER-12);
- paused agents;
- no PHI in diagnostics;
- `clinician_acknowledged`: an owner holding the `clinician` or the `nurse` role, recorded, acknowledged, reported to HealthLinc as `inquiry.status` only while the routing record shows the item open, recorded but not forwarded once it shows `responded`, `returned_not_clinical` or `closed`, and changing no alert, fallback, retry or clock (ER-19);
- the office manager's alert when an urgent item has no `clinician_acknowledged` in time: raised once the window passes after `handoff.ack` `accepted`, or after the acknowledgment of a later raise to `urgent`, while the item is open, covered under WI-05, also sent to the hour's administrative cover outside the office manager's working hours, not raised after `clinician_acknowledged`, `responded`, `returned_not_clinical` or `closed`, never for a `priority` or `routine` item, carrying no clinical content, sending nothing to HealthLinc, and changing no routing, clock, fallback or retry (ER-20).

The tests SHALL run against openChart and HealthLinc contract doubles, with synthetic data only.

#### Scenario: CI runs the handoff suite
- **WHEN** CI runs the handoff contract tests
- **THEN** they pass against contract doubles with synthetic data only

#### Scenario: A test adds a content field
- **WHEN** a test builds a `handoff.offer` that carries message text
- **THEN** schema validation fails, and the test expects that failure

### Requirement: ER-18 A clinician's clinical-need marker reaches openPractice through the handoff contract
A clinician MAY record in openChart that a patient clinically needs an appointment (`appointment_needed`) or continued clinical messaging and medical-AI consults (`messaging_continuity`) (ruling 50; design call DC-32). openChart SHALL send each marker to openPractice as `clinical_need.marker` under `openpractice-clinical-handoff/1.0`, on the patient's EB-01 link. The marker SHALL carry:
- its id, a coded kind, and the action, `mark` or `withdraw`;
- openChart's opaque reference to the clinician who recorded it, so it is attributed;
- when it was recorded and, for `mark`, a `valid_until` time;
- for `mark`, its origin: `clinician_judgment`, or `urgent_escalation` with the routing id (`related_handoff_id`) of the urgent handoff it arose from.

The marker SHALL carry no reason, note or other clinical content (constitution Principle IV), and the schema refuses any other field. openPractice SHALL record each marker with its attribution, acknowledge it with `message.ack`, and honor it until `valid_until` or its withdrawal. openPractice SHALL honor an `urgent_escalation` origin only when its own routing record shows that routing id with applied urgency `urgent` on that link; otherwise it SHALL reject the marker with `origin_not_verified`, and the marker allows nothing. openPractice MUST NOT create, change or infer a marker itself, and no openPractice user or agent can record one.

#### Scenario: A clinician marks an appointment as needed
- **WHEN** a clinician in openChart marks that a held patient needs an appointment
- **THEN** openPractice receives `clinical_need.marker` with kind `appointment_needed`, the clinician reference and `valid_until`, records it and acknowledges it

#### Scenario: A marker claims an urgent origin openPractice cannot find
- **WHEN** a marker with origin `urgent_escalation` names a routing id that openPractice's routing record does not show as urgent on that link
- **THEN** openPractice rejects it with `origin_not_verified`, and the marker allows nothing

#### Scenario: A marker carries a note
- **WHEN** openChart sends a marker that includes free text
- **THEN** it fails the schema, and nothing is stored

#### Scenario: Staff try to mark a need in openPractice
- **WHEN** a staff member tries to mark an appointment as clinically needed in openPractice
- **THEN** no marker is created, because only a clinician in openChart can record one

### Requirement: ER-19 A clinician's acknowledgment in openChart reaches HealthLinc as a coded status, and changes no safety path
When the owner of a routed item in openChart's clinical inbox explicitly acknowledges it, openChart SHALL report `handoff.status` with status `clinician_acknowledged` under `openpractice-clinical-handoff/1.0` (ruling 93; design call DC-50, PROPOSED). The owner is a named member of the clinical team: a person holding openChart's `clinician` or `nurse` role, as openChart's inbox allows. The value keeps the name `clinician_acknowledged`, the owner's wording in ruling 93, and covers both roles. openChart's system acceptance of the offer (`handoff.ack` `accepted`) is not acknowledgment, and neither is the delivery of a notification. openChart SHALL send `clinician_acknowledged` once per handoff, at the first acknowledgment, and only while the item is open: never after `responded`, `returned_not_clinical` or `closed`. A `responded` status MAY arrive without an earlier `clinician_acknowledged`. These are companion items for openChart.

On `clinician_acknowledged`, openPractice SHALL:
- record its time on the routing record (ER-01);
- answer it with `message.ack`;
- send HealthLinc `inquiry.status` with status `clinician_acknowledged` for that routing id, under `openpractice-patient-gateway/1.0`, but only while its routing record shows the item open. A `clinician_acknowledged` that arrives after the record shows `responded`, `returned_not_clinical` or `closed`, such as a late retry, SHALL be recorded and acknowledged and SHALL NOT be forwarded, so the patient app never shows "acknowledged" after "answered".

Neither message carries clinical content, reply text, a note, or anyone's identity or role. The status SHALL NOT cancel, delay or close any alert, tracked alert, urgent fallback, retry or clock of ER-07 to ER-09, and SHALL NOT extend `respond_by` (design call DC-51, PROPOSED): a clinician's pickup is reported, and every safety path still runs on its own triggers. For an urgent item, its absence past ER-20's window raises only ER-20's alert to the office manager. HealthLinc SHALL report the status only when openPractice sends it, and never infer it (companion item).

#### Scenario: An acknowledgment arrives after the reply
- **WHEN** openChart's `clinician_acknowledged` for an item arrives after openPractice recorded `responded` for it
- **THEN** openPractice records it and answers `message.ack`, and sends HealthLinc no `inquiry.status` for it

#### Scenario: A nurse owner acknowledges an item
- **WHEN** the owner of an inbox item in the nurse lane acknowledges it
- **THEN** openChart sends `clinician_acknowledged`, and HealthLinc receives it with no name or role

#### Scenario: A clinician picks up an urgent item
- **WHEN** the owner of an urgent inbox item acknowledges it in openChart
- **THEN** openChart sends `handoff.status` `clinician_acknowledged`, openPractice records the time and answers `message.ack`, and HealthLinc receives `inquiry.status` `clinician_acknowledged` with no clinician identity

#### Scenario: openChart accepts an offer but no clinician has acknowledged it
- **WHEN** openChart answers `handoff.ack` `accepted` and no clinician has acknowledged the item
- **THEN** no `clinician_acknowledged` status is sent or reported

#### Scenario: The response deadline passes after an acknowledgment
- **WHEN** an urgent item was acknowledged by a clinician, and `respond_by` passes with neither `responded` nor `clock_expired`
- **THEN** ER-09's fallback still runs with cause `clinical_response_overdue`

#### Scenario: A status carries a clinician reference
- **WHEN** openChart sends `clinician_acknowledged` with a clinician reference or a note
- **THEN** the message fails the canonical schema and is rejected

### Requirement: ER-20 openPractice alerts the office manager when an urgent item has no clinician acknowledgment in time, and the alert changes no clinical path
When openChart has accepted an item whose applied urgency is, or is later raised to, `urgent` (`handoff.ack` `accepted`), and no `clinician_acknowledged` (ER-19) has arrived by the end of the acknowledgment-alert window while the routing record shows the item open, openPractice SHALL alert the office manager (ruling 101). The alert SHALL become work owned by the office manager (WI-02), covered under WI-05 when the office manager is absent, and SHALL reach the owner's mobile approver when one is registered (MA-02). Outside the office manager's working hours, the alert SHALL also reach that hour's administrative cover named by ER-13's roster, and the work item's owner does not change. The office manager's working hours are those the roster records for the office manager, or the practice hours of ER-13's calendar where it records none (design call DC-58, PROPOSED). It SHALL carry no clinical content: only the routing id, the urgency and a coded cause (ER-16). It SHALL send nothing to the patient or to HealthLinc, and SHALL change no routing, clock, fallback or retry (ruling 101): ER-07 to ER-09 run exactly as they would without it. No alert is raised when `clinician_acknowledged`, `responded`, `returned_not_clinical` or `closed` arrives before the window ends, and none for a `priority` or `routine` item. A `clinician_acknowledged` that arrives after the alert is recorded on the office manager's work item.

The window is a policy value (design call DC-58, PROPOSED). It belongs to the routing-clock policy of ER-08, and the medical director approves it with that policy. It is counted from openChart's `handoff.ack` `accepted`, or, for an item raised to `urgent` after that acceptance, from openChart's `message.ack` of `handoff.urgency_raised`, or from openPractice's receipt of a clinician's `handoff.clinician_urgency_raised`. Until an approved version sets it, it equals that policy's urgent acknowledgment window, 5 minutes by the fail-closed default.

#### Scenario: No clinician acknowledges an urgent item in time
- **WHEN** openChart accepts an urgent offer, and no `clinician_acknowledged` arrives within the window after that acceptance
- **THEN** the office manager is alerted with the routing id, the urgency and a coded cause, HealthLinc receives nothing, and no routing, clock, fallback or retry changes

#### Scenario: A clinician acknowledges in time
- **WHEN** the owner of an urgent item acknowledges it within the window
- **THEN** no ER-20 alert is raised

#### Scenario: An item is raised to urgent after acceptance
- **WHEN** openChart accepted a `priority` item an hour ago, the item is raised to `urgent`, and openChart acknowledges `handoff.urgency_raised`
- **THEN** ER-20's window starts at that acknowledgment, not at the original acceptance

#### Scenario: An urgent item at 03:00
- **WHEN** ER-20 raises an alert at 03:00, outside the office manager's working hours
- **THEN** the alert goes to the office manager and also to that hour's administrative cover, the work item stays the office manager's, and no clinical path changes

#### Scenario: The office manager is absent
- **WHEN** ER-20 raises an alert while the office manager is marked absent with a named cover
- **THEN** the alert's work item goes to the cover, still showing the office manager's name (WI-05)

#### Scenario: A priority item is not acknowledged by a clinician
- **WHEN** a `priority` item is accepted by openChart and no clinician acknowledges it
- **THEN** no ER-20 alert is raised, and ER-07's alerts still apply on their own triggers
