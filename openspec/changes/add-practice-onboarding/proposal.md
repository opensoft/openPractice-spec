Status: draft

## Why

openPractice has no ratified statement of what identifies the practices that license and run it. The existing changes govern what lives *inside* a practice once it exists: `define-mvp-practice-foundation` governs the administrative patient record and the billing-versus-accounting boundary; `define-customer-financial-data-boundaries` governs party roles in the financial flow; `add-staff-scheduling-and-hr-integrations` governs staff rosters; `add-reimbursement-retention-control` governs retention. None of them defines the practice record itself, and nothing can bill for a practice until its identifiers, banking and payer enrollments are known and verified.

The gap is load-bearing in three places:

- `billing-accounting-boundary` keys payer deposit links by payer **and billing entity**, yet no capability defines a billing entity, its Tax ID, its pay-to address or its clearinghouse assignment.
- The MSO operating model (multiple clinics under one MSO) presumes many billing entities and many service locations per onboarded practice, with per-entity clearinghouse routing (Stedi vs Office Ally) decided at enrollment time.
- Credentialing (CAQH, state licenses, DEA, malpractice, PTAN links) has no home: no capability today can store the documents whose expiry already blocks billing in every real practice.

This change is **practice onboarding**, not facility onboarding: the practice is the customer; facilities are service locations *of* the practice. The data recorded here is what credentialing and billing need to activate revenue for an onboarded practice.

## What changes

- **`practice-register`.** Know-your-business record for the practice: legal identity, tax identity, ownership and controlling individuals, administrative contacts, agreement and BAA status, lifecycle states (`prospect`, `intake`, `active`, `suspended`, `offboarded`).
- **`billing-entity-register`.** Per-Tax-ID billing identity under a practice: NPI type 2, taxonomies, pay-to/mail-to addresses, clearinghouse route per payer with the route decision record, submitter/payer IDs, bank account reference into the existing bank account register, fee schedule reference, lifecycle states.
- **`service-location-register`.** Physical sites rendering care: address, place-of-service code, phone/fax, operating hours, site-level licenses (pharmacy/regulated-site license, CLIA certificate, accreditation), linked to the billing entity that reaches them.
- **`provider-roster`.** Per-practice provider credential packet: NPI type 1, CAQH identity and attestation dates, state licenses, DEA registration, board certifications, malpractice policy, PTAN and Medicaid linkage, supervising relationships, and the document store with expiry alerts at 90/60/30 days.
- **`enrollment-tracker`.** Per (billing entity or provider) x payer x transaction type status of every enrollment (270/271 eligibility, 837P/I/D claims, 835 ERA, 276/277 status), with evidence links and a single resolver per enrollment.
- **Readiness gates.** Two governed states: `intake-complete` (identity data collected and checked) and `billing-ready` (enrollments green, banking registered). No service location of a practice may produce finalized charges for a payer whose enrollment trio is not green.

## Capabilities

### New Capabilities
- `practice-register` (P.O-01 to P.O-08): KYB record, ownership, contacts, agreements, lifecycle, vault pumps, audit.
- `billing-entity-register` (BE-01 to BE-09): entity identity, addresses, clearinghouse route and decision record, banking reference, lifecycle, audit.
- `service-location-register` (SL-01 to SL-07): site identity, place-of-service, site licenses, activity gating, audit.
- `provider-roster` (PV-01 to PV-10): credential packet, documents, expiry alerts, payer linkage, supervising relationships, audit.
- `enrollment-tracker` (EN-01 to EN-08): enrollment matrix, per-resolver ownership, evidence, re-enrollment events, audit.

### Modified Capabilities

None. This change depends on, and does not modify, `billing-accounting-boundary` (bank account register, payer deposit links), `add-reimbursement-retention-control` (record families for credential documents), and `add-staff-scheduling-and-hr-integrations` (providers here are a superset of schedulable staff). It references `define-mvp-practice-foundation` for audit, tiered sign-off (FD-5) and the synthetic-fixture rule.

## Impact

- **New data contracts:** practice, controlling individual (vaulted), billing entity, clearinghouse route decision, service location, provider credential packet, credential document reference, enrollment record, readiness gate state.
- **External systems:** Stedi (Enrollments API as the programmatic enrollment path) and Office Ally (SFTP/portal path, including its per-Tax-ID + rendering-NPI participation check). As with the MVP change, **any claim about a vendor's BAA availability is a non-goal**; BAA status is recorded as data, not asserted.
- **PHI/PII exposure:** no patient PHI is added. Ownership SSNs, DEA numbers and banking remain references into a vaulted store; the register holds verifiable facts only.
- **Governance:** audit of every change and read (as `patient-registry`), FD-5 tiered sign-offs for lifecycle transitions, synthetic fixtures only, tier-3 deadline escape preserved.
