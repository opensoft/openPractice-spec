## ADDED Requirements

### Requirement: AP-01 openPractice is the system of record for the administrative appointment
openPractice SHALL be the system of record for the administrative appointment, and the EHR SHALL own the clinical encounter (ruling 31, EB-19). openPractice SHALL book, reschedule and cancel appointments and record no-shows and walk-ins (ruling 115). The EHR MAY keep a reference to an openPractice appointment. Every appointment SHALL belong to exactly one visit thread (TH-01).

#### Scenario: The EHR keeps its own calendar
- **WHEN** the connected EHR also shows the visit on a calendar
- **THEN** openPractice's appointment is authoritative for the administrative booking, and no station of the thread is set by the EHR

### Requirement: AP-02 A booking names who, with whom, where, what and when, and mints the visit thread
A booking SHALL name:
- the patient, by an active registry record (PR-01);
- the rendering provider, by a roster entry;
- the location;
- a bookable catalog item (SC-01);
- the start time with its time zone, and the duration.

A booking SHALL mint the visit thread at `booked` (TH-02), or move a thread that already exists for the same intent. Booking SHALL be FD-5 tier 1, done and undoable from the record (design call CR-82, PROPOSED). An agent MAY book at most at `act_with_notice` (ruling 132, TH-13).

#### Scenario: Staff book a visit
- **WHEN** a front-desk staff member books an office visit for Pat Example with a provider at the main location next Tuesday at 9:00
- **THEN** an appointment is recorded with all five facts, and a visit thread is minted at `booked`

#### Scenario: A booking for an inactive record
- **WHEN** staff try to book an appointment for a deactivated patient record on a date after its deactivation
- **THEN** the booking is refused (AP-08)

### Requirement: AP-03 A reschedule is one move on the same thread, and only what changed is redone
A reschedule SHALL be a move to `booked`, from `booked` or `ready`, on the same thread, keeping the earlier slot as history (TH-04). It SHALL NOT be a cancellation followed by a new booking. Only what the change affects SHALL be redone (design call CR-22, PROPOSED):
- a new date SHALL re-check eligibility evidence when the date crosses a coverage boundary (EV-04), and the price when a price change falls between the dates (SC-03);
- a new provider SHALL re-check participation (EG-02) and the price;
- a new location SHALL re-check participation and the place of service;
- a new service SHALL re-check the price, the authorization requirement (EV-02) and whether a Good Faith Estimate is needed (GF-02).

A change that alters a Good Faith Estimate's service, price or provider SHALL lead to a replacement estimate (GF-06). Rescheduling SHALL be tier 1 (design call CR-83, PROPOSED), and an agent MAY reschedule at most at `act_with_notice` (ruling 132).

#### Scenario: The same day at a different time
- **WHEN** a visit moves from 9:00 to 14:00 on the same day with the same provider
- **THEN** the thread records one move, and its estimate, eligibility evidence and Good Faith Estimate stay as they were

#### Scenario: A different provider
- **WHEN** a visit moves to a provider who is not yet enrolled with the patient's payer
- **THEN** the booking warns that the claim will be held (EG-02), and the move is still allowed

### Requirement: AP-04 Cancellations, no-shows and patients who leave unseen are dispositions, never deletions
A cancellation, a no-show and a patient who leaves without being seen SHALL each be recorded as a disposition on the thread, with a coded reason, the time and the actor, and the thread SHALL move to `closed` (TH-04). No appointment SHALL be deleted. Where money paid toward the visit is still held when the disposition is recorded, its prepayment SHALL move to `unapplied_credit` with the disposition (`prepayment_to_credit`, PY-03), the thread SHALL move to `banked` instead of `closed`, and it SHALL close only when that credit has been applied, refunded or moved to a credit-balance case (PY-06; design call CR-69, PROPOSED). Undoing the disposition SHALL also reverse that `prepayment_to_credit`, and the undo SHALL be refused once the credit has been applied or refunded. A missed-appointment fee SHALL never be charged automatically: a person MAY charge it later from a catalog item routed as direct-pay healthcare, which moves the thread from `closed` or `banked` to `charged` (TH-04; design call CR-23, PROPOSED). A cancellation SHALL be FD-5 tier 2, done with a notification (design call CR-84, PROPOSED), and an agent SHALL stay at `propose` for it (ruling 132).

#### Scenario: A no-show
- **WHEN** a patient does not arrive and staff record a no-show
- **THEN** the thread closes with the disposition `no_show`, and no fee is charged unless a person charges one

#### Scenario: A prepaid visit is cancelled
- **WHEN** a patient who prepaid 100.00 for a self-pay procedure cancels it
- **THEN** the 100.00 moves to the guarantor account's `unapplied_credit` with the cancellation, the thread moves to `banked`, and it closes only once the credit is applied, refunded or moved to a credit-balance case

#### Scenario: An agent tries to cancel
- **WHEN** an agent opted up to `act_with_notice` for booking tries to cancel an appointment
- **THEN** it can only propose the cancellation, and a person decides

### Requirement: AP-05 A walk-in is recorded at arrival, not scheduled, and a visit with no appointment is still billable
A walk-in with no booking SHALL be recorded when the patient arrives, minting a visit thread at `arrived` (TH-02) with the same facts AP-02 requires; recording it is not scheduling a new appointment. A visit that the EHR documents with no openPractice appointment SHALL stay billable from its clinical facts or a manual charge, without an appointment (EB-19, TH-02).

#### Scenario: Billing a visit with no booking
- **WHEN** the EHR reports final facts for an encounter that no appointment matches
- **THEN** a thread is minted at `seen` and the visit can be charged

### Requirement: AP-06 Capacity comes from the roster, and conflicts warn rather than refuse
Bookable time SHALL be each provider's working hours at each location as the roster records them (C-1; FD-1); this change SHALL NOT add a separate schedule to author. A booking outside a provider's recorded hours, or overlapping another booking for the same provider, SHALL warn and SHALL need a coded reason, and SHALL NOT be refused (design call CR-26, PROPOSED). Readiness SHALL never block a booking (EV-04).

#### Scenario: A deliberate double booking
- **WHEN** staff book a short visit overlapping another booking for the same provider
- **THEN** a warning is shown, staff record the reason, and the booking is made

### Requirement: AP-07 A booking applies the payment-hold clause of PH-01, with its exceptions
Because this change gives openPractice a booking function, it SHALL apply PH-01's scheduling clause (ruling 41). While a payment hold is in effect for a patient, openPractice SHALL refuse a new non-urgent appointment for that patient, show the hold, and offer to lift it under PH-04. It SHALL allow the booking when either of PH-01's exceptions holds: a valid `appointment_needed` marker from openChart (ER-18), recorded on the booking; or an urgent escalation whose routing id openPractice's own routing record shows with applied urgency `urgent` for the same patient. No one in openPractice SHALL be able to mark an appointment clinically needed. A hold SHALL NOT cancel or change an appointment already booked. A payment hold SHALL NOT refuse the arrival of a walk-in at the desk: recording the arrival of a patient who is present is not new scheduling, and it SHALL be recorded, with the hold shown to staff, who may offer to lift it under PH-04 (design call CR-80, PROPOSED). Until PH-01 itself says so (proposed amendment PA-10 in `design.md`), or if the owner rejects CR-80, this walk-in rule SHALL yield to PH-01 as written. Every lift offered by this requirement, from a booking or an arrival, SHALL be PH-04's `payment_hold.lift`, made and recorded as PH-04 states, including its rule on who may not lift a hold, whether a named human or an agent acting for its sponsor or its principal (AM-03), and PR-15's one-person alternative where that rule applies (design call F-7 of `define-mvp-practice-foundation`). The booking itself stays routine work that rule does not catch.

#### Scenario: A held patient asks for a routine visit
- **WHEN** staff try to book a routine visit for a patient on a payment hold, with no marker and no urgent routing id
- **THEN** the booking is refused, the hold is shown, and staff may lift the hold under PH-04

#### Scenario: A clinician's marker lets the visit through
- **WHEN** a valid `appointment_needed` marker exists for a held patient and staff book the visit
- **THEN** the booking is made and records the marker's id

#### Scenario: A staff member's own child is on a payment hold
- **WHEN** a receptionist books a routine visit for her child, whose balance she guarantees and on which a payment hold is in effect, with no marker and no urgent routing id
- **THEN** the booking is refused and the hold is shown; she cannot lift the hold herself while another named human who may lift it is on the roster, so the lift goes to that person, and any lift records who made it

#### Scenario: A staff member is the held patient
- **WHEN** a staff member who is an adult patient on a parent's guarantor account is refused a routine booking because of a payment hold
- **THEN** the staff member cannot lift the hold while another named human who may lift it is on the roster, and no agent sponsored by the staff member, or acting at the staff member's request, may lift it either

#### Scenario: A held patient walks in
- **WHEN** a patient on a payment hold arrives at the desk with no booking
- **THEN** the arrival is recorded and the walk-in's thread is minted, and staff see the hold

### Requirement: AP-08 No appointment after a patient's deactivation date
openPractice SHALL refuse an appointment dated after the deactivation date of the patient's record (PR-11). An appointment already booked for a date after a deactivation SHALL raise a work item and SHALL NOT be cancelled automatically (design call CR-25, PROPOSED). Recording an arrival on such an appointment SHALL be refused under PR-11 until a person reactivates the record under PR-11 or records the cancellation (CR-25).

#### Scenario: A patient is deactivated with a future booking
- **WHEN** a patient with a visit booked next week is deactivated today
- **THEN** a work item is raised for the booked visit, and the appointment stays until a person decides

#### Scenario: The patient arrives anyway
- **WHEN** the deactivated patient arrives for that booked visit
- **THEN** the arrival is refused until a person reactivates the record or cancels the visit, and the desk is told why

### Requirement: AP-09 The MVP books minimally and exchanges no appointment messages
In this change, appointment booking SHALL NOT include reminders, a waitlist or self-scheduling (ruling 115). No appointment create, update or cancel operation SHALL travel on `openpractice-ehr-patient-link/1.0`, `openpractice-ehr-clinical-facts/1.0` or `openpractice-patient-gateway/1.0` (EB-19, PG-12); each waits for a governed MINOR version. Once this change's booking is built and ratified, the read-only answer `patient_answer.appointment_time` may be switched on under AO-11.

#### Scenario: A plan adds reminders
- **WHEN** a feature plan proposes automatic appointment reminders
- **THEN** it is out of scope for this change and needs a governed change
