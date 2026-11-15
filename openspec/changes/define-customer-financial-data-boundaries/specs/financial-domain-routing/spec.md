# Spec Delta

## Purpose

Assign medical, membership and retail obligations to explicit financial owners
and reconcile their accounting effects without duplicate debt, revenue or cash.

## ADDED Requirements

### Requirement: Offerings determine financial routing at line level
Each financial offering SHALL identify its effective version, selling entity, billing route, authoritative obligation owner, category, currency, approved accounting/tax mapping and disclosure profile. Supported routes SHALL distinguish insurance-billed healthcare, direct-pay healthcare, memberships and retail or separately sold services. Mixed orders SHALL preserve those distinctions per line. Medical charge finalization SHALL check a line's route and membership entitlement before any medical invoice or claim, as `define-mvp-practice-foundation` BAB-16 requires.

#### Scenario: Mixed healthcare and retail order
- **WHEN** an order includes an insurance-billed service and a retail product
- **THEN** each line follows its declared owner and route and the retail line does not become an insurance claim merely because the purchaser is a patient

#### Scenario: Required routing is unresolved
- **WHEN** an offering lacks a required obligation owner or accounting mapping
- **THEN** financial posting is held as owned review work without inventing a default claim, invoice or tax policy

### Requirement: Medical receivable lineage stays in medical billing
Medical billing SHALL own insurance charge/claim lineage, adjudication and healthcare patient responsibility, including direct-pay healthcare receivables. Insurance claims SHALL contain required coded service information under the applicable contract. Direct payment SHALL preserve existing estimate/GFE, patient billing and financial-authority controls without requiring an insurance claim.

#### Scenario: Patient pays directly for healthcare
- **WHEN** a healthcare service is explicitly routed as direct-pay
- **THEN** its obligation remains traceable through medical billing and the system does not create an insurer claim solely to collect the payment

### Requirement: Membership and retail obligations use connected business ledgers
Connected subscription/accounting capabilities SHALL own membership agreements, renewal invoicing and corresponding commercial receivables. Connected commerce/accounting capabilities SHALL own retail sales obligations. Membership entitlements SHALL state whether a service is included, discounted or separately chargeable under approved terms. Subscription status SHALL NOT itself authorize money movement or insurance billing. openPractice's offering routing SHALL refuse to route a PHI-bearing membership obligation, such as a medical membership, to a connected accounting destination whose BAA gate is not satisfied in openPractice's accounting destination register (`define-mvp-practice-foundation` AI-14), and SHALL create owned review work instead. That routing is the enforcement point: a connected subscription or commerce capability receives such an obligation only through it. Because a connected ledger can issue renewals itself, openPractice SHALL also refuse at configuration time to activate a PHI-bearing offering, such as a medical membership, against a destination whose BAA gate is unsatisfied. Records that a connected ledger writes outside openPractice SHALL be listed as a deployment control in the deployment's risk analysis.

#### Scenario: Membership renews
- **WHEN** a valid agreement reaches its renewal date
- **THEN** its authoritative subscription/accounting ledger issues the permitted renewal invoice without creating a medical encounter or claim, and any automatic collection separately satisfies the recorded payment mandate

#### Scenario: Service is included in membership
- **WHEN** approved membership terms include a service without a separate service charge
- **THEN** the system does not create another collectible service obligation or automatic insurance claim for that service

#### Scenario: Medical membership is bound for an ungated destination
- **WHEN** a medical-membership obligation would route to an accounting destination with no satisfied BAA gate in openPractice's destination register
- **THEN** openPractice's routing refuses the route and creates owned review work, and no customer record for that membership is written to the destination

#### Scenario: A medical membership offering is activated against an ungated destination
- **WHEN** an administrator activates a medical-membership offering whose ledger is a destination with no satisfied BAA gate in openPractice's destination register
- **THEN** activation is refused, so the ledger cannot later issue renewals for it there, and the refusal is recorded

#### Scenario: Membership and insurer terms are unresolved
- **WHEN** a service's membership entitlement or permitted insurance treatment cannot be resolved
- **THEN** the system creates financial review work before claim/invoice posting rather than charging both paths by default

### Requirement: One obligation has one collectible balance
Every obligation SHALL retain its authoritative domain and immutable source identifier. Any representation in another ledger SHALL reference that same obligation and SHALL NOT create a second collectible balance. Reclassification or responsibility transfer SHALL be linked and balanced rather than treated as an unrelated sale.

#### Scenario: Medical receivable is represented in accounting
- **WHEN** a detailed connector creates an accounting representation of an existing medical receivable
- **THEN** both representations retain the source link and collection views count the obligation once

#### Scenario: Financial responsibility transfers
- **WHEN** an approved adjustment transfers responsibility between payer and patient
- **THEN** linked entries preserve the original obligation and adjustment history without reporting a new sale solely because its responsible party changed

### Requirement: Each economic event follows one accounting posting path
The system SHALL tie each accounting effect to its source domain, immutable event identifier and entity, and SHALL assign each economic event to one posting path so that its effect is recognized once. Posting identity and idempotency, export batches and their freeze and release, retries, the reconciliation report, the `summary-journal` and `customer-detail` posting modes and the BAA gate SHALL be those of `openpractice-accounting-integration/1.0`, owned by change `define-mvp-practice-foundation`; this change SHALL NOT define a second posting mechanism. Invoicing, cash collection, deferred revenue, recognized revenue, taxes, fees and refunds SHALL remain distinguishable.

#### Scenario: An event could reach accounting twice
- **WHEN** an event has been posted through one path, and either a batch in the other posting mode for the same destination or another route would post it again
- **THEN** the contract refuses any batch in a second mode for that destination (`define-mvp-practice-foundation` AI-09), and routing has given the event exactly one owner, so no other route posts it

#### Scenario: Posting is retried after timeout
- **WHEN** delivery of a financial posting is retried after an uncertain acknowledgement
- **THEN** the retry follows the contract's idempotency and indeterminate-outcome lookup under the original posting identity before asserting success or creating a replacement

#### Scenario: Renewal invoice is paid and recognized
- **WHEN** accounting receives an invoice, its payment and the applicable recognition event
- **THEN** reporting distinguishes the three stages and does not add them together as three sales

### Requirement: Mixed payments and corrections preserve reconciliation controls
The system SHALL allocate a mixed receipt to compatible authoritative obligations and reconcile its processor gross, fees and net settlement once. Each settlement SHALL name one deposit poster. For a deposit openPractice explains, openPractice is that poster (`define-mvp-practice-foundation` BAB-14): it posts the share that funds membership or retail balances as `other_route_share`, and each owning route records only its obligation allocation. It SHALL preserve entity, merchant and payer/guarantor boundaries, legacy run-off exclusions, dated reversals and existing financial-authority controls. Any approval, release decision or attestation this change requires SHALL follow the FD-5 tier of its action under `define-mvp-practice-foundation` BAB-17 (ruling 33): a named AI agent identified by an openWallet ID MAY sign only an action type opted in for agent signing, and tier-4 actions, including record merges and refunds leaving the practice, SHALL be signed by a named human only. Export batches, their freeze and the reconciliation report SHALL follow `openpractice-accounting-integration/1.0`, owned by change `define-mvp-practice-foundation`.

#### Scenario: One receipt pays membership and retail obligations
- **WHEN** a permitted payment covers a membership invoice and retail invoice for compatible entity and merchant scopes
- **THEN** the system records separate obligation allocations and one receipt/settlement identity whose allocations equal the received amount

#### Scenario: A card settlement funds medical and membership balances
- **WHEN** one card settlement funds a medical patient payment and a membership invoice payment
- **THEN** openPractice is the named deposit poster and posts one deposit entry equal to the settlement, with the membership share as `other_route_share`, and the membership route records its allocation without a second deposit entry

#### Scenario: Refund follows an exported settlement
- **WHEN** an authorized refund corrects a payment in an already exported batch
- **THEN** the system records linked correcting entries in a later batch under the contract's correction rules and preserves the original frozen batch and financial-authority evidence
