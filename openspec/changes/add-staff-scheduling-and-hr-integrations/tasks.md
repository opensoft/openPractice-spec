# Tasks

## Contract

- [ ] T-01 Author the `openpractice-staff-schedule-provider/1.0` contract and canonical schema in `contracts/` for workers and skills, leave, roster revisions, approved attendance, clock events and open shifts.
- [ ] T-02 Define conformance test suite with synthetic fixtures every adapter must pass.
- [ ] T-03 Specify revision tracking for the published baseline and confirmed revised assignments. Define the readback for HR-approved worked intervals and unresolved attendance.

## Coverage engine

- [ ] T-04 Model visit-type resource requirements (duration per role, skill mix, equipment usage windows, room turnover, upfront work).
- [ ] T-05 Compute precise overlaps from the visit calendar's staff requirement windows (AP-10). Preserve peak simultaneous staffing and staff-minutes in the 30-minute views.
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

- [ ] T-26 Run the AP-10 and AP-11 callout test through the adapter against the openHR fork with synthetic staff and appointments. See the upstream Frappe HRMS pass below.
  - Book two procedures at one site. Both require a procedure-qualified RN from 09:10 to 09:20. Verify a peak demand of two and twenty RN-minutes.
  - Record one RN callout. Verify the exact ten-minute shortage, the affected procedures, and exclusion of an available RN who lacks the qualification. Post an open shift and verify the posting alone leaves coverage pending.
  - Confirm an eligible float RN in openHR for 09:10 to 09:20. Read the revised roster through the adapter. Verify the planned gap closes and the as-designed roster shows the original two assignments.
  - Reconcile the original RN's full interval and the replacement's 09:12 arrival in openHR. Read back the approved attendance through the adapter. Verify as-built records the replacement's 09:12 to 09:20 interval and the variance report shows one qualified RN short from 09:10 to 09:12, or two RN-minutes.
- [ ] T-27 Microsoft Partner package evaluation (Core $925/yr) as Shifts/Graph integration testbed tenant.
- [ ] T-28 Owner ratification (`owner-ratification.md`) after strict validation.

### T-26 validation evidence

**Passed on upstream Frappe HRMS on 2026-11-26.** The dedicated test site ran Frappe 15.122.0, ERPNext 15.121.6 and HRMS 15.64.3. The live `test_t26_against_openhr_test_site` case passed through Frappe REST with synthetic staff and appointments. The fixture submitted roster and attendance records to the scheduling DocTypes using a separate HR Manager account. [Acceptance test at adapter commit `dff8136`](https://github.com/opensoft/openPractice-code/blob/dff8136967e3493be46e2376ca980da471049b67/src/open_practice/tests/test_openhr_adapter.py#L224)

| Step | Verified result |
| --- | --- |
| Two overlapping procedures from 09:10 to 09:20 | Peak demand was two procedure-qualified RNs and 20 RN-minutes. |
| Callout, open-shift post and draft replacement | Both procedures were affected. The available RN without the qualification was excluded. The request stayed pending with one qualified RN short for the full ten minutes. |
| HR Manager submitted the roster revision | The adapter read back the confirmed float and the planned gap closed. The published roster retained both original assignments. |
| HR Manager approved the float's 09:12 arrival | The adapter read back the approved 09:12 to 09:20 interval. One qualified RN was short from 09:10 to 09:12, totaling two RN-minutes. |

**Pending on the openHR fork.** Run the same acceptance case against a dedicated fork instance and record its revision, confirmed roster readback and approved attendance readback before closing T-26.

## Seam and degraded operation

- [ ] T-29 Define staff reference cache schema (worker id, role, skill level, certifications with expiry, home clinic) and snapshot policy: rolling 28 days roster, 90 days approved leave.
- [ ] T-30 Implement provider sync: 15-minute polling, webhooks where supported, nightly full reconcile; staleness timestamp on cached reads; TTL 4 hours before the uncertainty flag fires.
- [ ] T-31 Implement the offline write outbox: postOpenShift and other write-backs queue when the provider is unreachable and drain on reconnect, following the ehr-boundary demographic-outbox pattern.
- [ ] T-32 Implement provisional mode for outages over 24 hours: manual staff-present capture and walk-in coverage behind tiered sign-off; provisional records persist post-resync as audit entries marked superseded.
- [ ] T-33 Implement reconciliation on reconnect: HR wins on employment facts with silent cache overwrite; provisional attendance advisory for variance only, never payroll; human review for provisional records touching patient-care documentation or pay; credential validation gets STRICTER, never looser, when the cache is stale beyond TTL.
