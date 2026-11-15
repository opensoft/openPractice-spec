## ADDED Requirements

### Requirement: TH-01 One thread per visit-intent
openPractice SHALL keep one thread for each visit the practice means to happen, from the moment that intent exists until nobody owes anybody anything for it (FD-7; design call CR-1, PROPOSED). A thread SHALL NOT be minted twice for one intent: a reschedule, a correction or a later payer's answer moves the same thread. A new intent, such as a follow-up visit, SHALL be a new thread linked to the first (TH-09). Charges, claims, remittances, ledger entries, Good Faith Estimates and statements SHALL be separate records that reference their thread; the thread is the index over them, not a copy of them.

#### Scenario: A visit is rescheduled
- **WHEN** a booked visit for the synthetic patient Pat Example is moved from Tuesday to Thursday
- **THEN** the same thread moves, and no second thread is minted for the visit

#### Scenario: A follow-up is booked at the visit
- **WHEN** the clinician asks for a follow-up visit in two weeks and staff book it
- **THEN** a new thread is minted for the follow-up, linked `spawned_from` the first visit's thread

### Requirement: TH-02 A thread is minted only by a stated event
A visit thread SHALL be minted by exactly one of these events (design call CR-1, PROPOSED):
- a booking (AP-02), at station `booked`;
- a walk-in arrival with no booking (AP-05), at station `arrived`;
- billable facts from the EHR, or a manual charge entry, for a visit that matches no thread (TH-10, CG-02), at station `seen`.

A non-visit case thread SHALL be minted at `intent`, and only when its case type's own event occurs (TH-08). A waitlist entry and an inbound page SHALL NOT mint a thread in this change; their types are reserved for a later governed change. No other event SHALL mint a thread, and the minting event SHALL be recorded on the thread.

#### Scenario: A walk-in
- **WHEN** a patient with no booking arrives and is added at the desk
- **THEN** a visit thread is minted at `arrived`, recording the walk-in as its minting event

#### Scenario: Facts arrive for an unbooked visit
- **WHEN** the EHR reports final billable facts for an encounter that matches no thread
- **THEN** a visit thread is minted at `seen`, and the facts are referenced from it

### Requirement: TH-03 Every thread carries five properties at every moment
Every thread SHALL carry, at every moment:
- **station and condition:** one station from TH-04 and one condition from TH-05, never empty;
- **age:** the time since the thread was minted, and the time at its current station;
- **clock:** the nearest deadline that costs money if missed, with its plain-language name and the time remaining, or empty where no such deadline exists. The clock SHALL be derived from dates and rules openPractice already holds, such as the date of service plus the payer's filing window, a Good Faith Estimate's issuance deadline or dispute window, a statement's due date, an authorization's expiry or a refund obligation, and SHALL never be typed. The next-nearest deadline SHALL remain retrievable;
- **amount:** the amount at risk now and the counterparty who owes it, derived from ledger entries (SL-08) by the rule the canonical schema states for each station (design call CR-6, PROPOSED). The thread SHALL NOT store a balance. Where more than one counterparty owes money on the thread, it shows the largest open amount and names that counterparty, and the ledger gives the rest;
- **owner:** the roster entry who must act next (AM-06). The owner SHALL never be a payer, a patient or "the system".

#### Scenario: Two deadlines on one visit
- **WHEN** a charged visit has a filing deadline in 40 days and an authorization that expires in 12 days
- **THEN** the clock shows the authorization expiry with 12 days remaining, and the filing deadline is retrievable as the next one

#### Scenario: A patient balance and an open secondary
- **WHEN** a thread has 30.00 owed by the guarantor account and 55.00 expected from a second payer
- **THEN** the thread shows 55.00 with the second payer as counterparty, and the ledger shows both amounts

### Requirement: TH-04 Stations and legal moves are fixed
A thread's station SHALL be one of ten. Visit threads use nine, every station but `intent`; case threads use five: `intent`, `claimed`, `remitted`, `banked` and `closed` (design call CR-3, PROPOSED):

| Station | Meaning |
|---|---|
| `intent` | An outcome is owed and nothing is committed yet (non-visit cases) |
| `booked` | Visit only: a time, provider and place exist |
| `ready` | Visit only: nothing is left that must be true before arrival |
| `arrived` | Visit only: the patient is here |
| `seen` | Visit only: the visit happened |
| `charged` | Our side is complete and priced |
| `claimed` | Something left the building and we wait on someone outside |
| `remitted` | The outside party answered |
| `banked` | The money owed on the thread was received by the practice |
| `closed` | Nobody owes anybody anything on the thread |

`banked` SHALL mean that the practice recorded receipt of the money; whether that money reached the bank is shown by deposit explanation (BAB-14), not by the thread.

A move from one station to another SHALL follow this table. Legality SHALL be judged by the pair of stations: the Occasion column gives examples, not limits. Any other move between stations SHALL be refused, not warned about. Three further kinds of transition SHALL always be legal (design call CR-70, PROPOSED):
- a transition that keeps the station and changes only the condition, such as placing or releasing a hold or opening or ending a dispute (TH-05, TH-06);
- the reverse of a tier-1 or tier-2 move, inside that move's undo window (TH-07), naming the transition it reverses;
- the undo of a thread's minting transition inside its undo window, which moves the thread to `closed` with the coded disposition `entered_in_error`.



| From | To | Occasion |
|---|---|---|
| `booked` | `ready` | Readiness found nothing missing (EV-04) |
| `ready` | `booked` | Readiness found something missing again |
| `booked`, `ready` | `booked` | Reschedule (AP-03) |
| `booked`, `ready` | `arrived` | The patient arrived; readiness never blocks this move |
| `booked`, `ready` | `closed` | Cancelled or a no-show, with its disposition (AP-04) |
| `booked`, `ready`, `arrived` | `banked` | Cancelled, a no-show or left unseen while money paid toward the visit is still held (AP-04) |
| `arrived` | `seen` | The visit happened |
| `arrived` | `closed` | Left without being seen, with its disposition |
| `seen` | `charged` | A charge set was finalized (CG-04) |
| `seen` | `closed` | Nothing to bill, with a coded disposition: the EHR withdrew the visit whole (EB-15), it was documented in error, or nothing in it is billable |
| `charged` | `claimed` | A claim was submitted (CL-05) |
| `charged` | `banked` | Self-pay, paid in full |
| `charged` | `closed` | Zero amount owed, or a write-off under RT-06 |
| `claimed` | `remitted` | The payer answered: payment, denial or determination |
| `claimed` | `charged` | Rejected before adjudication (CL-09) |
| `claimed` | `claimed` | A replacement or void was submitted (CL-08) |
| `remitted` | `claimed` | The next payer, an appeal, or a corrected claim |
| `remitted` | `banked` | The money owed was received |
| `remitted` | `closed` | The balance reached zero with no money moving |
| `banked` | `remitted` | A later answer on the same visit, such as the patient's share |
| `banked` | `closed` | Nothing is left |
| `remitted`, `banked` | `charged` | Coverage was found late, so a claim will be built (GF-11) |
| `closed` | any earlier station | Reopened by a later fact, such as a chargeback, a recoupment, late coverage or a missed-appointment fee charged after a no-show (AP-04) |
| `intent` | `claimed`, `remitted`, `banked`, `closed` | Non-visit cases, per their type (TH-08) |

#### Scenario: An illegal move
- **WHEN** a process tries to move a thread from `booked` straight to `remitted`
- **THEN** the move is refused and the thread stays at `booked`

#### Scenario: A self-pay visit paid at the desk
- **WHEN** a self-pay visit's charge is finalized and paid in full at checkout
- **THEN** the thread moves `charged` to `banked`, and on to `closed` when nothing is left

#### Scenario: A hold is placed at a station
- **WHEN** a claim is held for participation while its visit thread is at `charged`
- **THEN** a transition from `charged` to `charged` sets the condition to `held`, and it is legal although the table lists no such row

#### Scenario: The EHR withdraws a visit
- **WHEN** the EHR withdraws the whole of a visit that is at `seen` and has no charge set
- **THEN** the thread moves `seen` to `closed` with a coded disposition, and no zero-amount charge set is made

### Requirement: TH-05 Conditions say why a thread is not moving
Each thread SHALL show one condition (design call CR-3, PROPOSED):
- `moving`: nothing stops it;
- `held`: declared by a person or a shipped rule. A thread MAY carry several holds at once, each with its own reason from TH-06, structured release condition, owner and clock; it is held while any of them is in force, and each ends by itself when its release condition is met;
- `stuck`: never set by anyone. It SHALL be derived, and only derived, when the thread's clock has run out, or when the thread has no clock and has stayed at its station longer than that station's shipped dwell time. It ends when the thread moves;
- `disputed`: declared, naming an outside counterparty, a guarantor account or a payer coverage, and the dispute case or appeal that opened it, with its own clock. While a thread is `disputed`, no statement for the disputed money SHALL be issued and no collection step for it SHALL advance (PS-07). It SHALL end when that case or appeal records its outcome or a withdrawal (GF-10, RM-07).

When more than one applies, the thread SHALL show the first of `disputed`, `held` and `stuck`, in that order, because a dispute limits what the patient may be told and a hold stops money work; every hold SHALL stay listed while the thread is disputed, and the stuck test SHALL still be evaluated (design call CR-72, PROPOSED). A condition SHALL never replace the station: a held claim is still at `charged`, and every view SHALL show the station together with its condition.

#### Scenario: Nobody can mark work as stuck
- **WHEN** a user tries to set a thread's condition to `stuck`
- **THEN** the change is refused, because `stuck` is only derived

#### Scenario: A dispute stops a statement
- **WHEN** a Good Faith Estimate dispute marks a visit thread `disputed`
- **THEN** no statement for the disputed amount is issued until the dispute resolves

### Requirement: TH-06 Hold reasons are a closed list with structured release conditions
A hold SHALL use one reason from a closed list that the product ships and the practice cannot extend (FD-1; design call CR-4, PROPOSED). Each reason is placed by the requirement named here, which also states its release condition and its deadline escape (TH-12):
- `participation_not_effective`: placed by EG-03; it escapes through CL-06 only while the answer is `not_yet` (EG-06);
- `waiting_on_clinical_documentation`: placed by CG-07; it never escapes into a claim, and at the filing threshold it escalates (CG-07);
- `clinical_fact_retracted`: placed by CG-05, CG-08 and CL-04; it never escapes into a claim, and at the filing threshold it escalates (CL-06);
- `identity_review`: placed by EB-05 and EB-18; it escapes as EB-18 states;
- `offering_route_unresolved`: placed by CG-05; it escapes as BAB-16 states;
- `waiting_on_authorization`: placed by EV-02 and CL-04; it never escapes by itself, and at the filing threshold it escalates to a named human who may release the claim with a recorded reason (EV-02).

A dispute is the `disputed` condition (TH-05), not a hold. No thread hold SHALL stop a patient from being booked, arriving or being seen: a thread hold stops money work, never care (EV-04). A payment hold is a different thing, governed by PH-01 and AP-07.

Each hold SHALL record a release condition as structured data that a query can evaluate, never as free text. When a release condition is met, every thread it holds SHALL be released in one action, with a notification (tier 2; design call CR-87, PROPOSED). A new hold reason SHALL need a governed change.

#### Scenario: A free-text hold
- **WHEN** a user tries to hold a thread with the reason "waiting for Dana to call back"
- **THEN** the hold is refused, and the user is offered the snooze of TH-12 instead

#### Scenario: One release frees many threads
- **WHEN** a provider's enrollment with a payer becomes effective from a date on or before the dates of service of seven held claims
- **THEN** all seven threads are released in one action, and staff are notified with the total

### Requirement: TH-07 Every move is dated, attributed and justified, and never rewinds
Every transition SHALL record:
- the effective date and the time openPractice recorded it;
- the actor, actor type and principal (AM-03), and for a sign-off the role under which it was given (`define-practice-readiness` SR-02);
- a reference to the dated fact that justified it, such as a booking, a final clinical fact, a claim submission, a remittance line, a payment or a person's decision. A transition that cannot name its fact SHALL be refused;
- the FD-5 tier that governed it;
- whether its effect left the building.

A minting transition SHALL also record the thread type and the owner, and any other transition that changes the owner SHALL record the new one, so that a thread's snapshot can be rebuilt from its transitions alone. A thread SHALL never rewind. Undoing a tier-1 or tier-2 move SHALL append the reverse move (TH-04). A move's undo window SHALL last until anything derived from the move leaves the building, and never longer than 30 days (design call CR-71, PROPOSED). Reopening a `closed` thread SHALL be a forward move that appends. Once an effect has left the building, the only correction SHALL be a replacement or a void (CL-08), never an erasure.

#### Scenario: Undo of an arrival
- **WHEN** staff mark the wrong patient as arrived and undo it a minute later
- **THEN** a reverse transition is appended, and both transitions remain in the thread's history

#### Scenario: A late chargeback
- **WHEN** a card payment on a closed self-pay visit is charged back
- **THEN** the thread moves forward from `closed` to `charged`, citing the chargeback, and its earlier history is unchanged

### Requirement: TH-08 Non-visit cases are a closed set of types
openPractice SHALL support these non-visit case types, each a thread with its own minting event, stations, clock, amount and owner (design call CR-5, PROPOSED):

| Case type | Minted when | Clock | Amount |
|---|---|---|---|
| `credit_balance` | Money is held that is not the practice's: an unidentified receipt, a patient credit or a payer overpayment (PY-06) | The refund or reporting obligation for its cause | The amount, owed outward |
| `gfe_dispute` | A patient disputes a bill against a Good Faith Estimate (GF-10) | The dispute process's window | The disputed difference |
| `recoupment` | A payer asks for money back (RM-09) | The payer's decide-by date | The amount demanded |
| `enrollment_case` | A provider's participation with a payer is applied for or found missing (EG-01) | Derived from the claims it holds | The sum of the claims it holds |
| `old_system_balance` | A balance from the practice's previous system is brought over (SL-11) | The legacy filing or statute date where known | The open balance |

A case type's amount and clock that depend on what it gates SHALL be derived from the threads it holds, never typed. The types `unmatched_inbound_page`, `inbound_page_not_ours` and `payer_records_request` SHALL be reserved names, unused until a governed change defines them. Adding a type SHALL be an additive version of the thread contract (TH-11).

#### Scenario: An enrollment case's amount
- **WHEN** an enrollment case holds three claims of 120.00, 85.00 and 40.00, with the nearest filing deadline in 33 days
- **THEN** the case shows 245.00 and 33 days, and nobody typed either number

#### Scenario: A reserved type
- **WHEN** a process tries to mint a `payer_records_request` thread
- **THEN** it is refused, because the type is reserved

### Requirement: TH-09 Threads link to each other; they never inherit
Threads SHALL reference each other only through named link types: `spawned_from`, `replaces`, `recoups`, `offsets` and `held_by`. No link SHALL re-point a thread from one party to another: PR-15 records that two parties are the same person without moving any role, record or balance, so no thread moves. A full merge of parties, if a later change defines one, would need its own governed change to this contract. There SHALL be no parent-child inheritance and no cascade: a change to one thread SHALL NOT change a linked thread except through that thread's own transition.

#### Scenario: A recoupment links to its visit
- **WHEN** a payer demands money back on a paid visit
- **THEN** a `recoupment` case thread is minted and linked `recoups` to the visit thread, and the visit thread changes only when the recoupment posts

### Requirement: TH-10 Inbound records match a thread by a key set and are never attached silently
Because outside systems do not carry the thread's identifier, each thread SHALL publish a key set: the patient's link or registry identity, the payer and member identifier of the coverage version, the date of service, the rendering provider, the billed amount, the EHR encounter reference when known, and the payer's claim control number once known (design call CR-7, PROPOSED). An inbound record, such as a set of clinical facts or a remittance line, SHALL attach to a thread only when it matches exactly one thread on at least the keys required for its kind, and when no key it carries conflicts with that thread's value (design call CR-7, PROPOSED). The required keys SHALL be:
- for clinical facts: the patient's link, the date of service and the rendering provider; and also the EHR encounter reference wherever the thread already holds one, or wherever more than one encounter is known for that patient, date and provider;
- for a remittance's claim payment: the payer's claim control number; or, where the payer gave none, the patient's registry identity, the payer and member coverage, the date of service and the billed amount;
- for any other inbound record: the patient's identity, the date of service, and every other key of the thread that the record carries.

A key the record carries that differs from the thread's value SHALL exclude that thread, however many other keys match. A match SHALL be a dated transition recording the keys that matched, and SHALL be undoable at tier 1 for 30 days. Once anything derived from the match has left the building, such as a claim submitted or a statement issued, the undo SHALL be refused and SHALL become correction work under CL-08. A record that matches no thread SHALL become a work item and SHALL NOT be attached anywhere, except that clinical facts for a visit with no thread mint one (TH-02). A record that matches more than one thread SHALL become a work item naming every candidate.

#### Scenario: Facts match a booked visit
- **WHEN** final facts arrive for an encounter with the patient's link, the same date and the same rendering provider as one booked thread
- **THEN** the facts attach to that thread, and the transition records the keys that matched

#### Scenario: Two candidate threads
- **WHEN** a remittance line matches two threads for the same patient on the same day
- **THEN** it attaches to neither, and a work item names both threads for a person to decide

#### Scenario: A second encounter on the same day
- **WHEN** a patient seen in the morning, whose thread now holds encounter E1, is seen again that afternoon with no booking, and facts arrive for encounter E2 with the same patient, date and provider
- **THEN** the conflicting encounter reference excludes the morning thread, and a new thread is minted at `seen` for E2

### Requirement: TH-11 The thread contract is versioned and has one canonical schema
The thread SHALL be defined by the contract `openpractice-thread/1.0`, with one canonical schema, `contracts/openpractice-thread-1.0.schema.json` in this change, identified by the SHA-256 digest of its bytes (FD-7; design call CR-2, PROPOSED). The contract SHALL be versioned before any feature reads it. Every thread SHALL be stamped at minting with the contract version it was minted under, and the stamp SHALL never change. Every feature that reads threads SHALL declare the MAJOR version it reads, and SHALL treat a station or condition it does not know as "show it, do nothing". Additive changes, such as a new case type, hold reason, link type or legal move, SHALL increment MINOR. A breaking change, such as removing a station or changing what a station's amount means, SHALL need a new MAJOR version, a new digest, and every reader able to read both versions for as long as threads minted under the old one exist.

#### Scenario: A reader meets an unknown station
- **WHEN** a feature built against 1.0 reads a thread whose station a later MINOR version added
- **THEN** it shows the station as it is and takes no action on it

#### Scenario: A message fails the schema
- **WHEN** a transition record does not validate against the canonical schema at its recorded digest
- **THEN** it is refused, and nothing changes

### Requirement: TH-12 Held and stuck threads are owned work, and every gate keeps its deadline escape
Every thread that is `held` or `stuck` SHALL appear as a work item in the one queue of WI-01, with its owner, clock, FD-5 tier and source. A user MAY snooze a stuck thread to a date with a reason; snoozing SHALL move the next review date and SHALL NOT change the thread's age or its deadline. Every hold that could stall a claim past its filing deadline SHALL name its FD-5 deadline escape (WI-06), which SHALL be the escape stated by the requirement that defines the hold.

#### Scenario: The payer says call back
- **WHEN** a biller snoozes a stuck claim to the 12th because the payer asked them to call back then
- **THEN** the work item returns on the 12th, and the thread's age and filing clock keep running

### Requirement: TH-13 Every action type this change defines starts at propose, with the ceilings ruling 132 sets
Every action type this change defines SHALL start at `propose` for agents (ruling 37, AO-04). A practice MAY raise an action type's rung only through that action type's BAB-17 opt-in, and never above this ceiling (ruling 132; action type names, design call CR-8, PROPOSED):

| Action type | Highest rung a practice may opt up to |
|---|---|
| `appointment.book`, `appointment.reschedule` | `act_with_notice` |
| `charge.draft` | `act_with_notice` |
| `payment.allocate` | `act_with_notice` |
| `claim.build` | `act_with_notice` |
| `statement.send` | `act_after_delay` |
| `appointment.cancel`, `charge.finalize`, `gfe.issue`, `claim_batch.release`, `write_off.record`, `refund.request`, `estimator_version.propose` | `propose` |

An action type this change defines that the table does not name SHALL stay at `propose` until a governed change sets its ceiling. An agent SHALL never act with less friction than the action's FD-5 tier requires (AO-04). There SHALL be no request mode below the rung the practice has set; the practice lowers the rung instead (ruling 100, AO-04). Each feature SHALL add its action types to `openpractice-agent-operations` as a MINOR version (AO-11). An estimator version SHALL be approved only by the practice's accountant (RE-02), whatever an agent proposes.

#### Scenario: A practice tries to let an agent release claims
- **WHEN** a practice manager tries to raise `claim_batch.release` to `act_with_notice`
- **THEN** the change is refused, because ruling 132 caps it at `propose`

#### Scenario: An agent sends statements after a delay
- **WHEN** `statement.send` is opted up to `act_after_delay` and an agent schedules this month's statements
- **THEN** they wait out the tier-3 cancel window before they are issued, and a person can stop them

### Requirement: TH-14 A thread holds references, never clinical content
A thread and the records it indexes SHALL hold no clinical content (Principle IV, PR-01, EB-16). They SHALL reference clinical facts by the EHR's issuer-qualified encounter and fact references and versions (EB-02, EB-13), and SHALL store only what is needed to bill. The only frozen copies SHALL be the documents a third party judges the practice on: the claim as submitted, the Good Faith Estimate as issued, the statement as issued, the receipt as issued and the remittance as received. Everything else, such as an address or a payer's current name, SHALL be referenced, so that correcting it once corrects it everywhere without falsifying history.

#### Scenario: A note is offered to a thread
- **WHEN** an integration tries to attach visit note text to a thread
- **THEN** it is refused, and nothing is stored

### Requirement: TH-15 No PHI in logs, errors, keys or diagnostics
Logs, error messages, diagnostic bundles, idempotency keys and coded reasons from every capability of this change SHALL NOT contain names, dates of birth, contact details, member identifiers, amounts tied to a person, codes tied to a person, or clinical content (EB-12, AI-19). They SHALL reference records only by opaque identifiers.

#### Scenario: A transition fails
- **WHEN** a transition is refused and a diagnostic is written
- **THEN** it names the thread's opaque identifier, the attempted move and a coded reason, and no personal data

### Requirement: TH-16 Fixtures and examples are synthetic
Test fixtures, seed data, examples and documentation for every capability of this change SHALL use synthetic data, visibly marked as synthetic. They SHALL NOT contain real PHI, real member or claim identifiers, real payer credentials, real bank or card numbers, or real contact details (PR-14).

#### Scenario: Fixture review
- **WHEN** a reviewer inspects the fixtures of any feature of this change
- **THEN** every person, identifier, amount source and account value is marked synthetic and none is real
