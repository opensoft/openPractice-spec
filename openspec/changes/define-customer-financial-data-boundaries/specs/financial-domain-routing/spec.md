# Spec Delta

## Purpose

Assign medical, membership and retail obligations to explicit financial owners
and reconcile their accounting effects without duplicate debt, revenue or cash.

## ADDED Requirements

### Requirement: Offerings determine financial routing at line level
Each financial offering SHALL identify its effective version, selling entity, billing route, authoritative obligation owner, category, currency, approved accounting/tax mapping and disclosure profile. Supported routes SHALL distinguish insurance-billed healthcare, direct-pay healthcare, memberships and retail or separately sold services. Mixed orders SHALL preserve those distinctions per line.

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
Connected subscription/accounting capabilities SHALL own membership agreements, renewal invoicing and corresponding commercial receivables. Connected commerce/accounting capabilities SHALL own retail sales obligations. Membership entitlements SHALL state whether a service is included, discounted or separately chargeable under approved terms. Subscription status SHALL NOT itself authorize money movement or insurance billing.

#### Scenario: Membership renews
- **WHEN** a valid agreement reaches its renewal date
- **THEN** its authoritative subscription/accounting ledger issues the permitted renewal invoice without creating a medical encounter or claim, and any automatic collection separately satisfies the recorded payment mandate

#### Scenario: Service is included in membership
- **WHEN** approved membership terms include a service without a separate service charge
- **THEN** the system does not create another collectible service obligation or automatic insurance claim for that service

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
The system SHALL identify accounting effects by source domain, immutable event identifier, entity, currency, version and destination mapping. It SHALL support the existing medical summary-journal route and an explicitly selected customer-detail route while ensuring an event's effect is recognized once. Retries SHALL reuse the original event identity. Invoicing, cash collection, deferred revenue, recognized revenue, taxes, fees and refunds SHALL remain distinguishable.

#### Scenario: Invoice and summary contain the same event
- **WHEN** an accounting destination has accepted an event through the detailed route and a later additive summary attempts to include it
- **THEN** the system prevents duplicate posting and exposes the original acknowledgement for reconciliation

#### Scenario: Posting is retried after timeout
- **WHEN** delivery of a financial posting is retried after an uncertain acknowledgement
- **THEN** the system reuses its original event identity and reconciles destination status before asserting success or creating a non-idempotent replacement

#### Scenario: Renewal invoice is paid and recognized
- **WHEN** accounting receives an invoice, its payment and the applicable recognition event
- **THEN** reporting distinguishes the three stages and does not add them together as three sales

### Requirement: Mixed payments and corrections preserve reconciliation controls
The system SHALL allocate a mixed receipt to compatible authoritative obligations and reconcile its processor gross, fees and net settlement once. It SHALL preserve entity, merchant and payer/guarantor boundaries, legacy run-off exclusions, dated reversals, immutable exported batches and existing financial-authority controls.

#### Scenario: One receipt pays membership and retail obligations
- **WHEN** a permitted payment covers a membership invoice and retail invoice for compatible entity and merchant scopes
- **THEN** the system records separate obligation allocations and one receipt/settlement identity whose allocations equal the received amount

#### Scenario: Refund follows an exported settlement
- **WHEN** an authorized refund corrects a payment in an already exported batch
- **THEN** the system records linked correcting entries in a later batch and preserves the original batch and financial-authority evidence
