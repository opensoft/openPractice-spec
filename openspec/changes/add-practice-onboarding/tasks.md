Status: draft

## 1. Specs

- [ ] 1.1 Author capability spec `practice-register` (KYB record, ownership, agreements, lifecycle)
- [ ] 1.2 Author capability spec `billing-entity-register` (identity, addresses, route decision record, banking ref)
- [ ] 1.3 Author capability spec `service-location-register` (site, POS, site licenses)
- [ ] 1.4 Author capability spec `provider-roster` (credential packet, expiry alerts, payer linkage)
- [ ] 1.5 Author capability spec `enrollment-tracker` (matrix, statuses, evidence, resolver)
- [ ] 1.6 Define readiness gates `intake-complete` and `billing-ready` in all affected specs

## 2. Data contracts

- [ ] 2.1 Canonical schema for the practice record, ownership set and lifecycle events
- [ ] 2.2 Canonical schema for billing entity, route decision record, service location
- [ ] 2.3 Canonical schema for provider credential packet and document references
- [ ] 2.4 Canonical schema for enrollment records and transitions
- [ ] 2.5 SHA256SUMS for all contracts; synthetic fixtures only

## 3. Verification and audit

- [ ] 3.1 NPPES lookup check for NPI-1 and NPI-2 with verified-display rule
- [ ] 3.2 IRS TIN match path or W-9 attestation fallback with signer
- [ ] 3.3 USPS address standardization for pay-to and site addresses
- [ ] 3.4 Audit of every change and read, mirroring `patient-registry`
- [ ] 3.5 FD-5 tier mapping for lifecycle transitions and gate releases

## 4. External paths

- [ ] 4.1 Stedi Enrollments API adapter: create + status -> `enrollment-tracker`
- [ ] 4.2 Office Ally participating-payer check producing the route decision record
- [ ] 4.3 Office Ally SFTP fallback path recorded as `officeally-sftp` enrollments
- [ ] 4.4 Bank account handoff into `billing-accounting-boundary` encrypted register

## 5. Documents and alerts

- [ ] 5.1 Credential document store with checksums and vault references
- [ ] 5.2 Expiry alert scheduler at 90/60/30 days for licenses, DEA, malpractice, CAQH attestation
- [ ] 5.3 Retention family assignment per `add-reimbursement-retention-control`

## 6. Closure

- [ ] 6.1 Owner rulings on O-1, O-2, O-3
- [ ] 6.2 Strict validation pass
- [ ] 6.3 `owner-ratification.md` completed before any Speckit feature
