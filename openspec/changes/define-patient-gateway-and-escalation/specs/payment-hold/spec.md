## ADDED Requirements

### Requirement: PH-01 A payment hold blocks only non-urgent clinical messaging, new non-urgent scheduling and non-urgent medical-AI consults
A payment hold SHALL block only three things: non-urgent clinical messaging and new non-urgent scheduling (ruling 41), and non-urgent consults with medXfactory's medical AI (ruling 52). Non-urgent means applied urgency `routine` only; a `priority` or `urgent` item is never blocked (PG-10, design call DC-30). In the gateway, a hold SHALL withhold only `clinical_messaging_nonurgent`, `scheduling_new_nonurgent` and `medical_ai_consult`. HealthLinc's coach is never gated by the practice, so no hold reaches it.

In openPractice's own scheduling, a hold SHALL refuse only a new non-urgent appointment for the held patient. This scheduling clause SHALL take effect only when a ratified scheduling change gives openPractice a booking function, and that change SHALL apply it with the exceptions below. Until then, a hold affects scheduling only through the gateway capability `scheduling_new_nonurgent`.

A hold SHALL NOT cancel or change an appointment already booked (design call DC-26). An appointment SHALL NOT be blocked when either of these holds:
- a clinician has marked it clinically needed: a valid `appointment_needed` marker, recorded and attributed in openChart and received through the clinical handoff contract (ER-18);
- it arises from an urgent escalation: the booking carries that escalation's routing id, and openPractice's own routing record shows that id with applied urgency `urgent` for the same patient.

No one in openPractice can mark an appointment clinically needed. Every other capability and function SHALL stay available, including administrative questions and demographic and coverage proposals.

#### Scenario: A held patient sends a routine clinical message
- **WHEN** a patient on a hold submits a clinical inquiry with routine urgency
- **THEN** it is rejected with `capability_withheld` and reason `payment_hold`, and HealthLinc shows the paths that stay available

#### Scenario: A held patient sends a priority clinical message
- **WHEN** a patient on a hold submits a clinical inquiry whose applied urgency is `priority`
- **THEN** it is accepted and routed, because a hold blocks only routine items

#### Scenario: Staff book a non-urgent appointment for a held patient
- **WHEN** a ratified scheduling change provides booking, and a front-desk staff member tries to book a new non-urgent appointment for a held patient
- **THEN** the booking is refused and the hold is shown, with the option to lift it under PH-04

#### Scenario: A clinician marks the appointment as needed
- **WHEN** a valid `appointment_needed` marker from openChart exists for a held patient, and staff book the appointment
- **THEN** the booking is allowed, and it records the marker's id

#### Scenario: Staff claim a clinical need without a marker
- **WHEN** staff try to book a non-urgent appointment for a held patient by stating that it is clinically needed, with no valid marker and no urgent routing id
- **THEN** the booking is refused, because only a clinician's marker or a verified urgent escalation lifts the block

#### Scenario: A hold is placed on a patient with a booked visit
- **WHEN** a hold takes effect for a patient who already has a booked appointment
- **THEN** the appointment stays booked and unchanged

### Requirement: PH-02 A payment hold never blocks urgent escalation, billing contact or own-records access
A payment hold MUST NEVER block an urgent escalation, billing contact, or access to the patient's own records (ruling 41). It SHALL NOT block payment, including FD-10(b)'s no-account pay link. It SHALL NOT block any statement, estimate, Good Faith Estimate (GFE) dispute or billing question. The entitlement result SHALL always grant `urgent_escalation`, `billing_contact` and `own_records_access` (PG-03).

#### Scenario: A held patient sends an urgent message
- **WHEN** a held patient submits an urgent clinical inquiry
- **THEN** it is accepted and routed with no delay (ER-09)

#### Scenario: A held patient asks about the bill
- **WHEN** a held patient submits a billing question
- **THEN** it is accepted and routed to the biller lane

#### Scenario: Someone configures a hold that blocks records
- **WHEN** a hold policy version lists own-records access among the things a hold blocks
- **THEN** the version is refused and cannot be approved

### Requirement: PH-03 No hold is placed without an approved, counsel-reviewed, versioned policy
openPractice SHALL refuse every attempt to place a payment hold until the practice has an approved, versioned hold policy (ruling 41). The practice's medical director, a named human, SHALL approve each policy version. The approval SHALL NOT be opted in for agent signing (design call DC-28). Counsel's review SHALL be recorded on each version, by reference, naming the counsel who gave it, with counsel's state bar, admission number, and the date and result of a check against that bar's public record, before approval, and SHALL NOT be self-approved (`define-practice-readiness` SR-04; ruling 149). The fields recorded are design call DC-65 (PROPOSED). Counsel SHALL be checked against the state bar's public record and matched against the practice's staff and owners (ruling 149); a match, or an admission that cannot be checked there or is not active, refuses it (`define-practice-readiness` SR-04).

Each version SHALL state:
- the coded criteria for placing a hold;
- the treatment of disputed balances, and of balances inside an open GFE dispute window (PH-06);
- the treatment of membership obligations (PH-07);
- the length of the tier-3 countdown;
- the wording of the patient notice;
- the lift conditions and the review interval.

A version that omits any of these SHALL NOT be approvable. This change asserts no legal rule. Counsel's review decides what the policy may lawfully contain.

#### Scenario: No policy is approved
- **WHEN** a staff member tries to place a hold before any policy version is approved
- **THEN** the placement is refused (fail closed)

#### Scenario: An approved policy applies
- **WHEN** the medical director approves a policy version that records counsel's review and states every required item
- **THEN** holds may be placed under that version, and each records it

#### Scenario: An agent tries to approve the policy
- **WHEN** a named AI agent tries to approve a hold policy version
- **THEN** the approval is refused whatever the opt-in settings

#### Scenario: A version leaves out the GFE treatment
- **WHEN** a draft policy version does not state how balances inside an open GFE dispute window are treated
- **THEN** the version cannot be approved

### Requirement: PH-04 Placing and lifting a hold are logged, tiered actions, and agents start at propose
Placing and lifting a hold SHALL each be a logged, tiered action, with the action types `payment_hold.place` and `payment_hold.lift` (ruling 41). Placing SHALL be FD-5 tier 3: a visible countdown with a cancel window, after which the hold takes effect. The countdown starts only once the patient notice is delivered or its delivery is recorded (PH-05). Lifting SHALL be tier 2: done at once, with notification. Both tiers are design call DC-5.

Both action types SHALL start at `propose` for agents (ruling 37; change A AO-04). An agent can never act with less friction than the tier requires. Above `propose`, an agent may place a hold only at `act_after_delay`, so the countdown and cancel window always run, and may lift a hold at `act_with_notice` or `act_after_delay`. Each rung above `propose` is reached only through that action type's BAB-17 opt-in. Agent placement SHALL obey AO-08's rate and blast-radius limits.

Every placement and lift SHALL record the policy version, the coded criteria met, the actor and actor type (AM-03), the named human who placed or lifted it where a human did, the signer where one signs (BAB-17), the time and the provenance. A named human SHALL NOT place or lift a hold on a patient who is their own party or in its same-person group, or whom their own party, or a party in that group, represents, or on a balance that their own party, or a party in that group, owes or guarantees, while another named human who may do so is on the roster, and an agent SHALL NOT do so where any of those holds for its sponsor's party or for the party of a person on whose request or behalf it acts (its principal, change A AM-03), or for either's group; `define-mvp-practice-foundation` PR-15's own-record rule and its one-person alternative govern, and the record then keeps both roles and that fact (its design call F-7, PROPOSED). It MUST NOT log an amount. When a placement's criteria stop being met during the countdown, the placement SHALL be cancelled.

#### Scenario: A staff member places a hold
- **WHEN** a biller places a hold under an approved policy
- **THEN** the patient is sent a notice (PH-05), the countdown starts once its delivery is confirmed or recorded, and the hold takes effect when the countdown ends unless it is cancelled

#### Scenario: The hold is cancelled in the window
- **WHEN** a staff member cancels a hold during its countdown
- **THEN** no hold takes effect, and the cancellation is logged

#### Scenario: An agent proposes a hold
- **WHEN** an agent at `propose` finds a patient who meets the policy criteria
- **THEN** it drafts a placement in the work inbox, and nothing happens until a named human approves it

#### Scenario: The balance is paid during the countdown
- **WHEN** the balance a pending hold relies on is paid before the countdown ends
- **THEN** the placement is cancelled and the patient is told

#### Scenario: A staff member's own balance is on hold
- **WHEN** a biller tries to lift a hold on a balance the biller owes as guarantor, and another named human may lift it
- **THEN** the lift is routed to that other person, and the attempt is recorded with both roles

#### Scenario: A staff member is the held patient but not the guarantor
- **WHEN** a staff member who is an adult patient on a parent's guarantor account tries to lift the hold that blocks their own booking, and another named human may lift it
- **THEN** it is an own-record act: the lift is routed to that other person

#### Scenario: An agent lifts its sponsor's hold
- **WHEN** an agent at `act_with_notice` for lifts tries to lift a hold on its sponsor's own patient record
- **THEN** the lift is refused as an own-record act of the sponsor, and the item goes to another named human

#### Scenario: An agent lifts the hold of the person who asked it
- **WHEN** an agent sponsored by one staff member, acting at the request of another staff member, tries to lift the hold on the requesting staff member's own patient record
- **THEN** the lift is refused as an own-record act of the person who asked, and the item goes to another named human

#### Scenario: A staff member represents the held patient
- **WHEN** a staff member who is the confirmed representative of a patient on hold tries to lift that hold, and another named human may lift it
- **THEN** it is an own-record act: the lift is routed to that other person

### Requirement: PH-05 The patient is told, with no PHI in push or text content
openPractice SHALL tell the patient when a hold's countdown starts and when a hold lifts (ruling 41), with a `patient.notice` of kind `hold_countdown_started` or `hold_lifted` through HealthLinc. Its template is the hold policy's approved notice wording (PH-03), and its `effective_at` is when the hold would take effect or when it lifted. The notice SHALL say what the hold blocks, what it never blocks (urgent help, billing contact and own records), and how to resolve it.

The countdown SHALL start only when openPractice holds a `notice.ack` with outcome `delivered` for the countdown notice, or has recorded a delivery through another channel that the registry's contact rules allow (PR-05), with the channel, the time and who recorded it. Without either, the countdown does not start, nothing is withheld, and the placement becomes work owned by the biller lane (design call DC-41). The hold takes effect only after the full countdown has run from that delivery (design call DC-6).

A hold notice SHALL go to the patient's own link, and to a representative only where the representative's proxy policy includes billing (PG-06, DC-34). Notices SHALL honor the patient's confidential-communication instructions (PR-05), carried as the notice's `restriction_codes`, which include every restriction that crosses a same-person link (`define-mvp-practice-foundation` PR-15), as flags only, kept after the link ends until that change's end-of-link review decides the party's own, and, for a representative link, the proxy policy (PG-06). Where the instructions leave no permitted channel, nothing SHALL be sent: HealthLinc answers `failed` with `no_permitted_channel` (companion item), openPractice sends nothing by a channel the instructions bar, and the countdown does not start. Push-notification and text-message content MUST NOT contain PHI, an amount or the reason for the hold. The detail SHALL appear only after sign-in, or in a statement sent through a channel the instructions allow.

#### Scenario: The countdown notice is delivered
- **WHEN** HealthLinc answers a `hold_countdown_started` notice with `notice.ack` outcome `delivered`
- **THEN** the countdown starts at the delivery time, the patient sees the explanation in the app after sign-in, and any push text is generic

#### Scenario: The countdown notice fails, then reaches the patient another way
- **WHEN** HealthLinc answers `failed`, and staff record a statement sent through a channel the patient's instructions allow
- **THEN** the countdown starts at the recorded delivery, and nothing is withheld before it ends

#### Scenario: The countdown notice fails and no permitted channel exists
- **WHEN** HealthLinc answers `failed` with `no_permitted_channel`, and the registry's contact rules allow no other channel
- **THEN** the countdown does not start, no hold takes effect, and the placement becomes work owned by the biller lane

#### Scenario: The patient has asked for no text messages
- **WHEN** a patient flagged `no_text_messages` is told about a hold
- **THEN** no text message is sent, and the notice goes only through allowed channels

#### Scenario: A hold lifts
- **WHEN** a hold is lifted
- **THEN** the patient receives a `hold_lifted` notice saying that every capability is restored

### Requirement: PH-06 Disputed balances and GFE dispute windows never count until the policy states their treatment
A hold policy SHALL state how disputed balances, and balances inside an open GFE dispute window (FD-4), are treated (ruling 41). Until an approved policy states that treatment, a disputed amount SHALL never count toward a hold. An amount inside an open GFE dispute window SHALL count as disputed (design call DC-8). When a dispute opens on an amount that a hold relies on, openPractice SHALL re-evaluate the hold at once, and SHALL do the same when a same-person reconciliation opens on an amount the hold relies on, which `define-revenue-cycle-core` PS-09, a draft not yet on `main`, leaves out of the balance it publishes (design call DC-68, PROPOSED). When the remaining criteria are no longer met, the hold SHALL lift under PH-09.

#### Scenario: A disputed amount
- **WHEN** a patient's only overdue amount is under dispute
- **THEN** it does not count toward a hold, and no hold can be placed on it

#### Scenario: A GFE dispute window is open
- **WHEN** a self-pay balance is still inside its GFE dispute window
- **THEN** that balance counts as disputed and does not count toward a hold

#### Scenario: A dispute opens on a held balance
- **WHEN** a patient disputes the amount an active hold relies on
- **THEN** the hold is re-evaluated at once and lifts if the criteria are no longer met

#### Scenario: A same-person reconciliation opens on a held balance
- **WHEN** a same-person reconciliation opens on the only overdue charge an active hold relies on
- **THEN** the hold is re-evaluated at once and lifts under PH-09, because that charge no longer counts toward it

### Requirement: PH-07 A hold counts only medical-billing balances unless the policy says otherwise, and a lapsed membership is not a hold
A hold SHALL count only balances that openPractice medical billing owns, including self-pay and direct-pay healthcare charges (FD-4). It MAY count membership obligations only when the approved policy explicitly includes them, and then only as change `define-customer-financial-data-boundaries`, merged on `main`, assigns their ownership. A lapsed or unpaid membership SHALL NOT by itself place a payment hold; membership affects access only through the entitlement check (PG-04, ruling 42) (design call DC-9). A hold SHALL NOT create, move or duplicate a collectible balance (AI-22).

#### Scenario: An overdue self-pay balance
- **WHEN** a self-pay patient's undisputed balance meets the approved policy's criteria
- **THEN** a hold may be placed under PH-04

#### Scenario: A membership has lapsed
- **WHEN** a member's subscription lapses
- **THEN** no payment hold is placed, and the entitlement check reports `membership_not_current`

#### Scenario: The policy is silent on memberships
- **WHEN** an approved policy does not include membership obligations
- **THEN** unpaid membership dues never count toward a hold

### Requirement: PH-08 A payment hold stalls no claim
A payment hold SHALL stall no claim (FD-5). It SHALL NOT hold charge finalization, claim creation or submission, remittance posting, statements, refunds or accounting postings. Because nothing deadline-bearing waits on it, it needs no FD-5 deadline escape, and no exception is recorded. A hold SHALL affect only the three capabilities in PH-01.

#### Scenario: A held patient's visit is billed
- **WHEN** a held patient's earlier visit is ready to claim
- **THEN** the charge is finalized and the claim is submitted as usual

#### Scenario: A plan tries to make a hold stop claims
- **WHEN** a feature plan proposes that a hold delay claim submission
- **THEN** the proposal conflicts with PH-08 and needs a governed change and a recorded FD-5 exception first

### Requirement: PH-09 A hold lifts when its basis ends, and every hold has a review date
A hold SHALL lift when its basis ends. openPractice SHALL lift a hold automatically, as a tier-2 action with notification, when any of these happens (design call DC-7):
- the balance it relies on is paid or resolved;
- a dispute leaves the policy's criteria unmet (PH-06);
- a same-person reconciliation leaves the policy's criteria unmet (PH-06; `define-revenue-cycle-core` PS-09, a draft not yet on `main`; design call DC-68, PROPOSED);
- the policy version it relied on is withdrawn or expires.

Every hold SHALL carry a review date from its policy. At that date it SHALL become work owned by the biller lane. A hold SHALL NOT be extended past its review date without a recorded review.

#### Scenario: The balance is paid
- **WHEN** a held patient pays the balance in full
- **THEN** the hold lifts automatically, every capability is restored, and the patient is told

#### Scenario: The review date passes
- **WHEN** a hold reaches its review date
- **THEN** it becomes work owned by the biller lane, and it is not extended silently

#### Scenario: The policy is withdrawn
- **WHEN** the medical director withdraws the policy version that active holds rely on
- **THEN** those holds lift automatically and each patient is told
