## ADDED Requirements

### Requirement: CP-01 One adapter interface, with every capability declared
openPractice SHALL reach payers only through claim-pathway adapters that implement one interface: submit a claim batch, receive acknowledgments, ask for a claim's status, receive remittances, and check eligibility (framework §8). Each adapter SHALL declare which of these it supports, and openPractice SHALL offer only what the configured adapter declares (framework §7.3).

#### Scenario: An adapter without status queries
- **WHEN** the configured adapter does not declare claim-status queries
- **THEN** openPractice offers no status query, and silence is handled by CL-09's work item

### Requirement: CP-02 Payer and jurisdiction rules live only in adapters
Everything specific to a payer, a claim format or a jurisdiction SHALL live in the adapter: the claim format and channel, code edits and required fields, the readiness checklist it fills in for each claim, its acknowledgment window, and the tables that normalize its codes (framework §8). An adapter's code edits SHALL only flag or hold a claim, and SHALL never change a code a biller confirmed (CG-02, CG-04); its normalization tables SHALL apply only to codes a payer sends back, such as adjustment reason and remark codes (RT-05). The core SHALL hold only claims, claim versions and lines, status events, remittances and the readiness checklist the adapter fills in. A change of jurisdiction or payer SHALL never require a change to the core.

#### Scenario: A second jurisdiction
- **WHEN** a practice in a different Medicare jurisdiction adopts openPractice
- **THEN** only its adapter's configuration differs, and the core is unchanged

### Requirement: CP-03 A simulated payer stands in for real payers in tests and training
openPractice SHALL ship a simulated-payer adapter that accepts, rejects, adjudicates, denies and pays claims according to scripted synthetic scenarios, and returns acknowledgments and remittances in the same shapes a live adapter would (ruling 118). It SHALL be usable only with synthetic data: it SHALL refuse configuration against any payer identity that is not marked synthetic, SHALL refuse any patient, coverage, claim or charge record that is not marked synthetic, and SHALL be unavailable on any site whose registry holds a patient record not marked synthetic, so that a simulated answer can never reach a real ledger (design call CR-51, PROPOSED).

#### Scenario: A training scenario with a partial denial
- **WHEN** the simulated payer runs a scenario that pays one line and denies another
- **THEN** openPractice receives an acknowledgment and a remittance in the same shapes a real one has; before feature 4 the remittance is recorded and not posted, and from feature 4 it posts exactly as a real one would

#### Scenario: A real patient on the simulated payer
- **WHEN** someone tries to send a claim for a patient record not marked synthetic to the simulated payer
- **THEN** it is refused, and nothing is recorded as submitted

### Requirement: CP-04 A manual path records a claim sent outside the system
openPractice SHALL let a biller record a claim that was sent outside the system, such as through a payer's portal or on paper (ruling 118). The record SHALL hold the claim version as sent, frozen (CL-07), who sent it, how and when, and, when known, the payer's claim control number and the payer's acceptance. It SHALL move the thread and post entries exactly as a built claim does (TH-04, CL-12), and SHALL count as a submission for the retention change's evidence (CL-11).

Because the claim has already left, openPractice SHALL record it, and SHALL also run CL-04's scrub on it and record the result (design call CR-77, PROPOSED). Where the thread is held, or the claim repeats a charge line already on a claim to the same coverage position (CL-03), openPractice SHALL keep the hold, mark the record "sent while held" or "possible duplicate", and escalate it to the practice manager. The recorder SHALL be a named human, and an agent SHALL stay at `propose` for recording a claim or its acceptance (TH-13).

#### Scenario: A portal submission is recorded
- **WHEN** a biller submits a commercial claim on the payer's portal and records it in openPractice with the portal's claim number
- **THEN** the thread moves to `claimed`, and the receivable moves to billed when the biller records the payer's acceptance

#### Scenario: A claim sent while held
- **WHEN** a biller records a portal claim for a visit held for `waiting_on_authorization`
- **THEN** the claim is recorded as sent, the hold stays, the record is marked "sent while held", and the practice manager is told

### Requirement: CP-05 The live clearinghouse comes later, behind a governed contract and a verified agreement
The live clearinghouse adapter SHALL come in feature 5 (ruling 118). Its contract SHALL be defined, with a canonical schema and digest, by a governed amendment before that feature starts (design call CR-24, PROPOSED). The adapter SHALL NOT be activated until a business associate agreement with the clearinghouse is recorded and verified, on the pattern of AI-14 (Principle I).

#### Scenario: Activation without an agreement
- **WHEN** an administrator tries to activate the clearinghouse adapter with no verified agreement recorded
- **THEN** activation is refused

### Requirement: CP-06 The Medicare adapter makes no jurisdiction assumption in the core
The Medicare adapter SHALL come in feature 5 and SHALL bill Medicare Part B as the primary payer (ruling 113). Its jurisdiction, enrollment, submission channel and formats SHALL be settled in that feature's planning with a certified biller and recorded in the adapter's configuration, never in the core (framework §8).

#### Scenario: Planning the Medicare adapter
- **WHEN** feature 5 is planned
- **THEN** the jurisdiction and channel are recorded in the adapter's configuration, and no core requirement changes

### Requirement: CP-07 Licensed formats stay inside the connector
Knowledge of licensed transaction formats SHALL stay inside the adapter or the clearinghouse connector, and the repository SHALL NOT redistribute licensed implementation guides (FD-11).

#### Scenario: A repository review
- **WHEN** a reviewer searches the repository for licensed implementation-guide text
- **THEN** none is found

### Requirement: CP-08 openPractice is one Frappe app, and each adapter is a separate package
openPractice SHALL be one Frappe app with a module per area of this change and the others (ruling 117). Every adapter, for claims, payments or accounting, SHALL be a separate package, loaded only when a configuration of its type exists, and the core SHALL never import an adapter or a vendor's library (AI-04's rule, extended to every adapter). openPractice SHALL NOT depend on ERPNext's own record types.

#### Scenario: openPractice with no adapter installed
- **WHEN** openPractice is installed with no claim, payment or accounting adapter
- **THEN** installation, startup and the full test suite succeed

### Requirement: CP-09 Adapters hold least-privileged credentials, log no PHI and are contract-tested
Each adapter's credentials SHALL be kept in the platform's secret store, scoped to what its operations need, recorded with an owner and an expiry, and kept out of logs, fixtures and commits (AI-18's rule). Adapter logs and diagnostics SHALL contain no PHI (TH-15). Each adapter SHALL pass contract tests against its contract's canonical schema, using a double or a disposable instance and synthetic data only (Principle III).

#### Scenario: CI runs the adapter tests
- **WHEN** CI runs the simulated-payer adapter's contract tests
- **THEN** they pass against synthetic data, with no access to any real payer or clearinghouse
