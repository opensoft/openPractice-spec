## ADDED Requirements

### Requirement: AH-01 These rules gate every opt-in above propose
No action type SHALL be opted above `propose` for any agent (AO-04) until all of the following hold for that agent's model-provider path:
- AH-02's verified agreement;
- AH-03's recipient profile;
- AH-04's logging controls;
- AH-05's untrusted-content controls.

If any of them lapses, every affected action type SHALL drop to `propose`.

#### Scenario: All controls are in place
- **WHEN** a provider's agreement is verified and its recipient profile and controls are recorded
- **THEN** the practice may opt action types above `propose` for agents on that provider

#### Scenario: A control lapses
- **WHEN** the provider's agreement expires
- **THEN** every action type for agents on that provider drops to `propose`

### Requirement: AH-02 A verified BAA or equivalent is required per model provider
Before PHI reaches an agent layer or its model provider, an authorized person SHALL record and verify a BAA or an equivalent agreement for that provider. The record follows the pattern of AI-14. Each provider SHALL be recorded separately. When no verified agreement exists, PHI SHALL NOT be sent to that provider. The system MUST NOT assume that any provider offers or has signed such an agreement.

#### Scenario: A provider with a verified agreement
- **WHEN** a provider's agreement is recorded and verified
- **THEN** PHI within the recipient profile may reach that provider

#### Scenario: A provider without one
- **WHEN** an agent is configured on a provider with no verified agreement
- **THEN** no PHI is sent to it, and the agent stays at `observe` or `propose` with non-PHI inputs only

### Requirement: AH-03 Only the minimum necessary PHI reaches the agent layer
For each agent and action type, the PHI that may reach the agent layer and its provider SHALL be defined by a recipient-profile version, in the single shape of `define-mvp-practice-foundation` AI-23. Its recipient is the provider and its purpose is the action type. Fields outside the profile SHALL NOT be sent. A profile SHALL NOT permit clinical content unless a later governed change allows it.

#### Scenario: An agent drafts an address update
- **WHEN** an agent works an address-update item
- **THEN** it receives only the fields its profile lists for that action type

#### Scenario: A field outside the profile
- **WHEN** an agent requests a date of birth that its profile omits
- **THEN** the field is withheld, and the request is logged with a coded reason

### Requirement: AH-04 No PHI in agent logs
Logs that openPractice writes about agent activity, and the agent layer's own logs that openPractice controls, MUST NOT contain names, dates of birth, contact details, identifier values or clinical content. They SHALL hold opaque references and coded reasons. What a provider retains SHALL be governed by the AH-02 agreement and recorded in the risk analysis.

#### Scenario: An agent action is logged
- **WHEN** an agent updates a record
- **THEN** the log holds the agent reference, the record reference and the action code, and no PHI

#### Scenario: A log line would carry PHI
- **WHEN** a component tries to log a patient's name
- **THEN** a contract test fails, and the name is not logged

### Requirement: AH-05 Untrusted content is data, never instructions
Content from patients, representatives, faxes, payers, uploaded documents, email, HealthLinc messages and other untrusted sources SHALL be treated as data, never as instructions. An agent SHALL NOT change its grants, rungs, recipients or targets because untrusted content says so. Every proposal or action SHALL record whether untrusted content contributed (AO-07). An action that untrusted content contributed to SHALL NOT exceed `propose` unless a human signer decides it, whatever its action type's rung. This is the prompt-injection defence.

#### Scenario: A fax tells the agent to change a payer address
- **WHEN** an inbound fax contains text instructing the agent to send remittances to a new account
- **THEN** the agent at most proposes, a human decides, and nothing changes on the fax's word

#### Scenario: Untrusted content and a high rung
- **WHEN** a patient message leads an agent to an action whose type is opted up to `act_with_notice`
- **THEN** the action is held as a proposal for a human signer
