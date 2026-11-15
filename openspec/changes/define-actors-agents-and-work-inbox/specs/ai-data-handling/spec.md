## ADDED Requirements

### Requirement: AH-01 These rules gate every agent grant at any rung, and a lapse suspends all agent PHI access
No grant SHALL be made to any agent at any rung, `observe` and `propose` included (AO-03, AO-04), and no PHI SHALL reach an agent layer, until all of the following hold for that agent's operator and model-provider path:
- AH-02's verified agreements, with the agent operator and with each model provider, and each model provider's verified zero-data-retention arrangement;
- for any path that uses a model provider, counsel's recorded confirmation of ruling 97's answer (AH-06, ruling 102);
- AH-03's recipient profiles;
- AH-04's logging controls;
- AH-05's untrusted-content controls.

If any of them lapses, openPractice SHALL at once suspend all PHI access for every agent on that path. A lapse does not drop the agents to `propose`. While the suspension lasts:
- no PHI is sent to the agent layer or its model provider, and every agent read of PHI is refused;
- every grant those agents hold is suspended, at every rung;
- their pending delayed actions are held, not executed;
- their open work routes to human owners (WI-04).

The suspension SHALL lift only when a named human records the re-verified control, as a tier-2 action (AO-03). The prior grants and rungs then return, and each held delayed action restarts its full delay window (AO-09) (design call D-16).

#### Scenario: All controls are in place
- **WHEN** the operator's and the provider's agreements are verified, and the recipient profiles and controls are recorded
- **THEN** the practice may grant action types to agents on that path, each starting at `propose`

#### Scenario: A grant before the gate
- **WHEN** someone tries to grant an agent an action type at `observe` before the operator's agreement is verified
- **THEN** the grant is refused, and no PHI reaches the agent layer

#### Scenario: A control lapses
- **WHEN** the model provider's agreement expires
- **THEN** all PHI access for agents on that path is suspended at once, their grants are suspended, their delayed actions are held, and their work goes to human owners; no agent keeps working at `propose`

#### Scenario: The suspension lifts
- **WHEN** a named human records the re-verified agreement
- **THEN** the grants and rungs return, and each held delayed action restarts its full delay window

### Requirement: AH-02 Verified BAAs are required with the agent operator and each model provider, and each model provider keeps no data
Before PHI reaches an agent layer or its model provider, an authorized person SHALL record and verify a HIPAA Business Associate Agreement (BAA) with each of these:
- the agent operator, the company that runs the agent layer (for Hermes, the company that runs the Hermes layer);
- each model provider on that agent's path.

Where a model provider serves the operator rather than the practice, the record SHALL name both agreements in the chain, the provider's with the operator and the operator's with the practice, and each SHALL be verified. Each record follows the pattern of AI-14: counterparty, execution date, effective and renewal or expiry dates, scope statement, owner-local document reference, verifying person and time. Each operator and each provider SHALL be recorded separately. When any agreement on the path is missing or unverified, PHI SHALL NOT be sent anywhere on that path. The system MUST NOT assume that any operator or provider offers or has signed a BAA. This change asserts no legal rule; counsel confirms each agreement's form.

**Zero data retention at every model provider (ruling 104).** A model provider keeps nothing: besides its BAA, each model provider on a path that carries PHI SHALL be used only under a zero-data-retention arrangement, recorded and verified the way the BAA is (ruling 104). What the arrangement covers, and how it is kept current, are design calls under ruling 104 (D-30, PROPOSED):
- *zero data retention* means that the provider keeps no prompt, no completion and no content derived from them, such as embeddings, caches, transcripts or copies for human review, once the request ends, for any purpose, abuse monitoring and model training included;
- it does not forbid operational metadata that carries no content: request identifiers, timestamps, token counts, the model and endpoint used, latency, error codes and billing records. Any identifier, tag or label the caller supplies and the provider keeps SHALL be opaque and carry no PHI (AO-12), and a retained error record SHALL carry no content;
- a cache of prompt or completion content at the provider, however short-lived, is retention under this definition and fails it. Whether a volatile cache that is never written to durable storage or logs, and lives no longer than a stated short time, may be allowed is Open Question 10;
- an authorized person records and verifies it on the pattern above: the provider, the party it is made with (the practice or the operator), the endpoints and models it covers, its effective and expiry dates, an owner-local document reference, and the verifying person and time;
- it is re-verified before PHI goes to a different provider, endpoint or model, and when the provider changes its terms. A path whose exact provider, endpoint and model have no verified arrangement gets no PHI (AH-01).

**The declared path (design call D-32, PROPOSED).** The agent operator SHALL declare on each agent's roster record (AM-04) the model provider, endpoint and model the agent uses, and SHALL declare again before any change. Its agreement SHALL forbid failing over to an endpoint or model it has not declared. openPractice SHALL send no PHI to an agent whose declared path lacks a verified BAA or a verified zero-data-retention arrangement for that exact provider, endpoint and model, and change `define-patient-gateway-and-escalation` ER-12 records the declared path.

The system MUST NOT assume that any provider offers zero data retention. Deletion with a receipt applies to the agent operator only (ruling 104; change `define-patient-gateway-and-escalation` ER-12).

#### Scenario: Both agreements are verified
- **WHEN** the operator's BAA and the provider's BAA are recorded and verified
- **THEN** PHI within the recipient profiles may reach that path

#### Scenario: The operator has no agreement
- **WHEN** a provider's BAA is verified but the operator's is not
- **THEN** no PHI is sent to the operator or the provider, and no grant can be made to an agent on that path (AH-01)

#### Scenario: A provider without one
- **WHEN** an agent is configured on a provider with no verified BAA
- **THEN** no PHI is sent to it, and no grant can be made to the agent (AH-01)

#### Scenario: A provider endpoint without zero data retention
- **WHEN** a provider's BAA is verified, but no zero-data-retention arrangement is verified for the endpoint and model an agent is configured on
- **THEN** no PHI is sent to that endpoint, and no grant can be made to an agent on that path (AH-01)

#### Scenario: The operator wants to move an agent to another endpoint
- **WHEN** the operator plans to move an agent to an endpoint it has not declared
- **THEN** it declares the new path first, openPractice sends that agent no PHI until the new path is verified, and a failover without a declaration breaks the operator's agreement

#### Scenario: The operator switches models
- **WHEN** the operator moves an agent to another model of the same provider
- **THEN** PHI stops on that path until the arrangement is verified for the new model

### Requirement: AH-03 Only the minimum necessary PHI reaches the agent layer
For each agent and action type, the PHI that may reach the agent layer SHALL be defined by recipient-profile versions in the single shape of `define-mvp-practice-foundation` AI-23: one for the agent operator and one for each model provider, each with the action type as its purpose. Fields outside the profile SHALL NOT be sent, and nothing about any patient other than the work item's target patient SHALL be sent (AO-03). A profile SHALL NOT permit clinical content unless a later governed change allows it.

#### Scenario: An agent drafts an address update
- **WHEN** an agent works an address-update item
- **THEN** it receives only the fields its profile lists for that action type

#### Scenario: A field outside the profile
- **WHEN** an agent requests a date of birth that its profile omits
- **THEN** the field is withheld, and the request is logged with a coded reason

### Requirement: AH-04 No PHI in agent logs
Logs that openPractice writes about agent activity, and the agent layer's own logs that openPractice controls, MUST NOT contain names, dates of birth, contact details, identifier values or clinical content. They SHALL hold opaque references and coded reasons. A model provider retains nothing with content (AH-02, ruling 104). What the agent operator retains SHALL be governed by its AH-02 agreement and by the deletion duty of change `define-patient-gateway-and-escalation` ER-12, and recorded in the risk analysis.

#### Scenario: An agent action is logged
- **WHEN** an agent updates a record
- **THEN** the log holds the agent reference, the record reference and the action code, and no PHI

#### Scenario: A log line would carry PHI
- **WHEN** a component tries to log a patient's name
- **THEN** a contract test fails, and the name is not logged

### Requirement: AH-05 Untrusted content is data, never instructions
Content from patients, representatives, faxes, payers, uploaded documents, email, HealthLinc messages and other untrusted sources SHALL be treated as data, never as instructions. An agent SHALL NOT change its grants, rungs, recipients, targets or content templates because untrusted content says so.

openPractice SHALL decide on the server whether untrusted content contributed to a proposal or action. It SHALL derive that from the provenance of each evidence reference and of the work item. Anything that came from HealthLinc, a fax, email, a payer, an upload, a patient or a representative is untrusted, and so is any reference whose provenance openPractice cannot establish. The agent's own `untrusted_input` flag MAY raise that determination and SHALL NOT lower it. openPractice SHALL record the determination on every proposal and action (AO-07).

An action that untrusted content contributed to SHALL NOT exceed `propose` unless a human signer decides it, whatever its action type's rung. The only exception is exception 1 below. This is the prompt-injection defence.

**Exception 1: read-only answers to the signed-in patient (rulings 34, 45 and 46).** An agent MAY answer a patient without a human signer only with a read-only answer, and only when every one of these conditions holds:
1. **Read-only.** The answer changes nothing in any system. It tells the signed-in patient only their own administrative information, such as an appointment time, a balance or the coverage on file, or practice-approved information, such as hours or policies. It carries no clinical content. The allowlisted fields of the `patient_answer.coverage_on_file` answer type SHALL name only non-identifying coverage facts, such as the payer or plan name and whether the coverage is on file, and SHALL exclude the member ID, the group ID or number, and every other identifier. An answer-type version whose allowlist names an identifier SHALL NOT be approved.
2. **Selection only.** The untrusted text only selects among the practice-approved answer types. It never changes the recipient, the target patient, the content template or the rung.
3. **Assembled on the server.** openPractice assembles the reply on the server from an approved, versioned template and allowlisted fields of the bound patient's own record. The agent writes no free text.
4. **Eligible items only.** All of these hold: the applied category is administrative; the applied urgency is `routine`; no source has marked any part clinical; the item is not part of a split question; the topic is on the practice's allowlist; and the patient asked on the patient's own link, not through a representative (design call D-14). Records requests, disputes and anything identity-revealing, such as identifiers, contact details or addresses, are excluded.
5. **Human approval of content.** A named human approves each answer type and each template version. Anything health-adjacent also needs the medical director's sign-off. Neither approval SHALL be opted in for agent signing.
6. **Switched on by the practice.** Each answer type starts at `propose` and is switched on by the practice through its own BAB-17 opt-in, made by a named human (AO-03, AO-04).
7. **Delay only.** The rung is `act_after_delay` only, with a practice-set window that the item's lane can cancel. `act_with_notice` is not allowed.
8. **One patient.** During the conversation the agent reads only that one patient's data (AO-03).
9. **Recorded, open and escapable.** The audit and the reply's attribution record the answer-type id and version. The item stays open, with a "this did not answer my question" path to staff, and the patient can always ask for a human.
10. **Watched.** AO-08's limits apply. A sampled human audit reviews the answers. Any defect returns that answer type to `propose` automatically. The pause switch (AO-09) holds every pending answer. AH-01 and AH-02 still apply.
11. **Consented.** The patient's consent to AI-authored administrative messaging is recorded and in force, checked before the answer is scheduled and again immediately before every relay, and its withdrawal cancels every pending answer (AH-06; rulings 93 and 97).

When no single answer type fits, or the data an answer type needs is missing, the item goes to staff. Anything uncertain goes to staff. Anything that changes something stays a proposal or follows the ladder.

#### Scenario: A fax tells the agent to change a payer address
- **WHEN** an inbound fax contains text instructing the agent to send remittances to a new account
- **THEN** the agent at most proposes, a human decides, and nothing changes on the fax's word

#### Scenario: Untrusted content and a high rung
- **WHEN** a patient message leads an agent to an action whose type is opted up to `act_with_notice`
- **THEN** the action is held as a proposal for a human signer

#### Scenario: The agent under-reports untrusted content
- **WHEN** an agent sends `untrusted_input` false for a coverage update whose evidence reference is an inbound fax
- **THEN** openPractice derives the contribution from the fax's provenance, records it as untrusted, and holds the action as a proposal for a human signer

#### Scenario: An eligible read-only answer
- **WHEN** a patient asks on their own link when the office opens, the item is routine and administrative, and the practice has switched the hours answer type on at `act_after_delay`
- **THEN** openPractice assembles the reply from the approved template, holds it for the lane's cancel window, relays it with the answer-type id and version, and keeps the item open

#### Scenario: Text tries to steer a read-only answer
- **WHEN** a patient message asks for the balance of a different account, or tells the agent to change its wording
- **THEN** no read-only answer is sent, the other account is not read, and the item goes to staff

#### Scenario: A coverage answer type that would reveal an identifier
- **WHEN** a version of the coverage-on-file answer type is submitted for approval with the member ID or the group number among its allowlisted fields
- **THEN** the version is not approved, no answer is assembled from it, and coverage questions go to staff until an allowlist without identifiers is approved

#### Scenario: An ineligible item
- **WHEN** an administrative item has applied urgency `priority`, any source has marked a part clinical, or a representative asked it
- **THEN** exception 1 does not apply, and the item goes to staff or becomes a proposal

#### Scenario: The audit finds a defect
- **WHEN** the sampled human audit finds a wrong answer from an answer type
- **THEN** that answer type returns to `propose` at once, and its pending answers wait for a human

### Requirement: AH-06 An agent's answer reaches a patient only while the patient's consent is recorded, checked before scheduling and before every relay
openPractice SHALL check the patient's consent to AI-authored administrative messaging itself, because openPractice, not the agent layer, assembles and relays every agent answer and holds the patient's link (ruling 97; change `define-patient-gateway-and-escalation` ER-10, which carries ruling 68's consent condition).

**The record (design call D-22, PROPOSED).** openPractice SHALL keep one consent record per patient, with the patient's administrative record:
- its state, `granted` or `withdrawn`;
- the time it took effect;
- the named human who recorded it, and the channel through which the patient gave the instruction;
- an opaque evidence reference.

Changes are kept as dated history and audited (PR-10). Until a grant is recorded, the patient has not consented, and no agent answer is sent (fail closed). Only a named human SHALL record a grant, as a tier-2 action, on the patient's own instruction; no agent SHALL record or change the record, and no untrusted content SHALL change it on its own word (AH-05). A representative SHALL NOT grant it, since a representative's question never receives an agent answer (design call D-14).

**Withdrawal (design calls D-27 and D-28, PROPOSED).** Withdrawal only ever reduces what is sent, so it has more paths than a grant:
- any staff member SHALL record a withdrawal at once, as a tier-1 action, on the patient's request through any channel; it takes effect when recorded;
- a representative whose authority on the patient's link is confirmed (PG-06 of change `define-patient-gateway-and-escalation`) MAY withdraw it the same way, and SHALL NOT grant it;
- when a patient tells an agent, in an item's text, to stop AI-authored answers, the agent SHALL release the item with `ai_messaging_withdrawal_requested` (AO-13). From that release, openPractice SHALL hold every agent answer to that patient, refuse new ones with `consent_not_recorded`, and give a staff member the work of recording the withdrawal or recording that consent stands. If consent stands, each held answer restarts its full cancel window; if it is withdrawn, the held answers are cancelled.

**The check.** openPractice SHALL check the record for every agent answer to a patient:
- before it schedules the answer. When no grant is in force, or answers are held, the request is refused with `consent_not_recorded`, and the item goes to its lane's staff;
- again immediately before every relay of the answer, however it was released: at the end of its cancel window, on a human's approval of an answer at `propose`, or when a pause or a suspension that held it lifts. When no grant is in force, or answers are held, the answer is not sent. A withdrawal cancels it; a hold keeps it held.

The relay of an agent answer and the recording of a withdrawal SHALL be serialized for each patient: whichever openPractice records first decides, so an answer relayed after a withdrawal was recorded is impossible, and a withdrawal recorded first always wins.

**On withdrawal (design call D-29, PROPOSED under ruling 93).** Ruling 93 names the consent check; cancelling pending answers is how this change carries it out. When a withdrawal is recorded, openPractice SHALL at once cancel every pending or held agent answer to that patient, on every item and every link, before any of them is sent. Each item stays open under its lane's human owner, and each cancellation is recorded with no PHI.

**Scope (design call D-23, PROPOSED).** The check applies to every agent-authored reply that reaches a patient: the read-only answers of AH-05 exception 1 in contract 1.0, and, once a governed MINOR version of `openpractice-agent-operations` carries it, an agent-drafted reply that a human approves. A reply a staff member writes is not AI-authored and is not gated by this record.

**What it does not gate (ruling 97).** This consent governs only AI answers that reach the patient directly. It does not gate an agent's processing of administrative data with a model provider for practice operations: the verified agreements with the agent operator and each model provider (AH-02) are enough for that. This design rests on ruling 97's answer now (ruling 102). Every other control of AH-01 to AH-05 still applies. This change asserts no legal rule; counsel confirms the consent's form and wording.

**Counsel's confirmation is a go-live condition (ruling 102).** Before any PHI reaches any model provider, an authorized person SHALL record counsel's confirmation of ruling 97's answer, on the pattern of AH-02 and `define-mvp-practice-foundation` AI-14: the confirming counsel, the date, the scope (the processing for practice operations it covers, naming the agent operators and model providers by their AH-02 records), an owner-local document reference, and the recording person and time (design call D-31, PROPOSED). Until it is recorded, AH-01's gate is not met for any path that uses a model provider: no PHI reaches a model provider, and no grant is made to an agent on such a path (fail closed). When counsel withdraws or narrows the confirmation, the recorded change is a lapse under AH-01.

#### Scenario: No consent is recorded
- **WHEN** an agent selects an eligible read-only answer type for a patient who has no recorded grant
- **THEN** the request is refused with `consent_not_recorded`, no answer is scheduled, and the item goes to its lane's staff

#### Scenario: Consent is withdrawn while answers are pending
- **WHEN** a staff member records a patient's withdrawal while two of that patient's read-only answers wait in their cancel windows
- **THEN** both answers are cancelled at once and never sent, each item stays open under its lane's human owner, and the cancellations are recorded with no PHI

#### Scenario: Consent lapses inside the window
- **WHEN** a patient's consent is withdrawn after an answer was scheduled but before its window ends
- **THEN** the check before sending finds no grant in force, and the answer is not sent

#### Scenario: A withdrawal and a relay race
- **WHEN** a staff member records a withdrawal at the moment an answer's cancel window ends
- **THEN** openPractice serializes the two: if the withdrawal is recorded first, the answer is cancelled and never sent

#### Scenario: A human approves an answer after consent is withdrawn
- **WHEN** a lane human approves an agent answer at `propose` for a patient whose consent was withdrawn after the answer was prepared
- **THEN** the check before the relay finds no grant in force, and the answer is not sent

#### Scenario: The patient tells the agent to stop
- **WHEN** an agent releases an item with `ai_messaging_withdrawal_requested`
- **THEN** every agent answer to that patient is held, new ones are refused with `consent_not_recorded`, and a staff member records the withdrawal or records that consent stands

#### Scenario: Counsel has not yet confirmed
- **WHEN** the operator's and the provider's agreements and the provider's zero-data-retention arrangement are verified, but counsel's confirmation of ruling 97's answer is not recorded
- **THEN** no PHI reaches the model provider, and no grant can be made to an agent on that path

#### Scenario: A representative withdraws
- **WHEN** a parent whose authority on the patient's link is confirmed asks staff to stop AI-authored answers
- **THEN** the withdrawal is recorded, every pending agent answer to the patient is cancelled, and the parent cannot later grant consent in the patient's place

#### Scenario: An agent tries to record consent
- **WHEN** an agent, or a patient's message read by an agent, tries to record a grant of consent
- **THEN** the change is refused, and only a named human may record a grant

#### Scenario: Registry work with no per-patient consent
- **WHEN** an agent on a path whose operator and provider agreements are verified prepares a registry proposal for a patient who has recorded no consent
- **THEN** the work proceeds under AH-01 to AH-05, because the consent governs only AI answers that reach the patient (ruling 97)
