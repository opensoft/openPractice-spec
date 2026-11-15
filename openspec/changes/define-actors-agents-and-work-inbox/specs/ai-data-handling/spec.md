## ADDED Requirements

### Requirement: AH-01 These rules gate every agent grant at any rung, and a lapse suspends all agent PHI access
No grant SHALL be made to any agent at any rung, `observe` and `propose` included (AO-03, AO-04), and no PHI SHALL reach an agent layer, until all of the following hold for that agent's operator and model-provider path:
- AH-02's verified agreements, with the agent operator and with each model provider;
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

### Requirement: AH-02 Verified BAAs are required with the agent operator and each model provider
Before PHI reaches an agent layer or its model provider, an authorized person SHALL record and verify a HIPAA Business Associate Agreement (BAA) with each of these:
- the agent operator, the company that runs the agent layer (for Hermes, the company that runs the Hermes layer);
- each model provider on that agent's path.

Where a model provider serves the operator rather than the practice, the record SHALL name both agreements in the chain, the provider's with the operator and the operator's with the practice, and each SHALL be verified. Each record follows the pattern of AI-14: counterparty, execution date, effective and renewal or expiry dates, scope statement, owner-local document reference, verifying person and time. Each operator and each provider SHALL be recorded separately. When any agreement on the path is missing or unverified, PHI SHALL NOT be sent anywhere on that path. The system MUST NOT assume that any operator or provider offers or has signed a BAA. This change asserts no legal rule; counsel confirms each agreement's form.

#### Scenario: Both agreements are verified
- **WHEN** the operator's BAA and the provider's BAA are recorded and verified
- **THEN** PHI within the recipient profiles may reach that path

#### Scenario: The operator has no agreement
- **WHEN** a provider's BAA is verified but the operator's is not
- **THEN** no PHI is sent to the operator or the provider, and no grant can be made to an agent on that path (AH-01)

#### Scenario: A provider without one
- **WHEN** an agent is configured on a provider with no verified BAA
- **THEN** no PHI is sent to it, and no grant can be made to the agent (AH-01)

### Requirement: AH-03 Only the minimum necessary PHI reaches the agent layer
For each agent and action type, the PHI that may reach the agent layer SHALL be defined by recipient-profile versions in the single shape of `define-mvp-practice-foundation` AI-23: one for the agent operator and one for each model provider, each with the action type as its purpose. Fields outside the profile SHALL NOT be sent, and nothing about any patient other than the work item's target patient SHALL be sent (AO-03). A profile SHALL NOT permit clinical content unless a later governed change allows it.

#### Scenario: An agent drafts an address update
- **WHEN** an agent works an address-update item
- **THEN** it receives only the fields its profile lists for that action type

#### Scenario: A field outside the profile
- **WHEN** an agent requests a date of birth that its profile omits
- **THEN** the field is withheld, and the request is logged with a coded reason

### Requirement: AH-04 No PHI in agent logs
Logs that openPractice writes about agent activity, and the agent layer's own logs that openPractice controls, MUST NOT contain names, dates of birth, contact details, identifier values or clinical content. They SHALL hold opaque references and coded reasons. What an operator or provider retains SHALL be governed by the AH-02 agreements and recorded in the risk analysis.

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
