# Tasks

## Contract

- [ ] T-01 Author `openpractice-staff-schedule-provider/1.0` contract and canonical schema in `contracts/` (workers+skills, leave, roster with revision state, clock events, open shifts, postOpenShift).
- [ ] T-02 Define conformance test suite with synthetic fixtures every adapter must pass.
- [ ] T-03 Specify revision tracking: roster read MUST support as-designed vs as-revised distinction or revisions-since-timestamp.

## Coverage engine

- [ ] T-04 Model visit-type resource requirements (duration per role, skill mix, equipment usage windows, room turnover, upfront work).
- [ ] T-05 Compute coverage requirements per 30-minute interval per role and skill from the visit calendar.
- [ ] T-06 Define baseline + flexible-reserve staffing model (float pool, per-diem tiers).

## Variance and gap resolution

- [ ] T-07 Implement three-state roster comparison and deficit emission (role, skill, interval, minutes).
- [ ] T-08 Implement resolution ladder: float -> open-shift cascade via postOpenShift -> overtime -> appointment triage (tiered sign-off required for triage).
- [ ] T-09 Skill-aware backfill validation against certifications with expiration blocking.
- [ ] T-10 Variance analytics: hours variance %, recurring-gap detection, forecast accuracy feedback.

## Adapters

- [ ] T-11 openHR (Frappe HRMS REST) adapter - reference implementation for MVP.
- [ ] T-12 QuickBooks Time API adapter (share auth/plumbing design with LedgerLinc).
- [ ] T-13 Microsoft Graph Shifts adapter (Schedule.Read.All / Schedule.ReadWrite.All; draft vs shared shift mapping; guard against known copied-shift userId API bug).
- [ ] T-14 Gusto API adapter (document multi-EIN handling for MedxMSO clinic entities).
- [ ] T-15 OrangeHRM REST adapter; T-16 Horilla adapter; T-17 ADP; T-18 BambooHR; T-19 Odoo; T-20 TimeTrex - built on adoption demand, in build order from design.md section 5.

## Companion forks

- [ ] T-21 Fork frappe/hrms -> opensoft/openHR: preserve LICENSE/notices, add FORK.md, license GPL-3.0.
- [ ] T-22 Fork frappe/books -> opensoft/openBooks: preserve LICENSE/notices, add FORK.md, in-app source link (AGPL s13), license AGPL-3.0.
- [ ] T-23 Name-collision and trademark check for openHR and openBooks before public use.
- [ ] T-24 Scaffold companion apps openhr-ext and openbooks-ext (hooks/custom doctypes/fixtures) to keep forks thin.
- [ ] T-25 Set up upstream-merge CI cadence tracking frappe/hrms monthly releases.

## Validation

- [ ] T-26 End-to-end demo: visit book -> coverage requirements -> callout -> deficit -> open shift posted to openHR -> filled -> variance report.
- [ ] T-27 Microsoft Partner package evaluation (Core $925/yr) as Shifts/Graph integration testbed tenant.
- [ ] T-28 Owner ratification (`owner-ratification.md`) after strict validation.

## Seam and degraded operation

- [ ] T-29 Define staff reference cache schema (worker id, role, skill level, certifications with expiry, home clinic) and snapshot policy: rolling 28 days roster, 90 days approved leave.
- [ ] T-30 Implement provider sync: 15-minute polling, webhooks where supported, nightly full reconcile; staleness timestamp on cached reads; TTL 4 hours before the uncertainty flag fires.
- [ ] T-31 Implement the offline write outbox: postOpenShift and other write-backs queue when the provider is unreachable and drain on reconnect, following the ehr-boundary demographic-outbox pattern.
- [ ] T-32 Implement provisional mode for outages over 24 hours: manual staff-present capture and walk-in coverage behind tiered sign-off; provisional records persist post-resync as audit entries marked superseded.
- [ ] T-33 Implement reconciliation on reconnect: HR wins on employment facts with silent cache overwrite; provisional attendance advisory for variance only, never payroll; human review for provisional records touching patient-care documentation or pay; credential validation gets STRICTER, never looser, when the cache is stale beyond TTL.
