# Tasks

## Contract

- [x] T-01 Author the `openpractice-staff-schedule-provider/1.0` contract and canonical schema in `contracts/` for workers and skills, leave, roster revisions, approved attendance, clock events and open shifts.
- [x] T-02 Define conformance test suite with synthetic fixtures every adapter must pass.
- [ ] T-03 Specify revision tracking for the published baseline and confirmed revised assignments. Define the readback for HR-approved worked intervals and unresolved attendance.

The [provider contract and schema](../../../contracts/staff-schedule-provider/1.0/README.md) define the canonical workforce messages. The shared Python suite checks each feed's schema with synthetic examples. The T-26 loopback case uses the same conformance harness as the native openHR case.

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
- [x] T-12 QuickBooks Time API adapter (share auth/plumbing design with LedgerLinc).
- [x] T-13 Microsoft Graph Shifts adapter and live QA acceptance (Schedule.Read.All / Schedule.ReadWrite.All, draft and shared shift mapping, and copied shifts without a user ID). [Validation evidence](#t-13-microsoft-teams-shifts-validation-evidence)
- [ ] T-14 Gusto API adapter (document multi-EIN handling for MedxMSO clinic entities).
- [ ] T-15 OrangeHRM REST adapter; T-16 Horilla adapter; T-17 ADP; T-18 BambooHR; T-19 Odoo; T-20 TimeTrex - built on adoption demand, in build order from design.md section 5.

### T-12 QuickBooks Time validation evidence

The adapter passed nine synthetic cases against a loopback HTTP fixture built
from the documented QuickBooks Time v1 wire formats. The fixture ran the
shared contract checks for workers, timed approved leave, clock events,
roster revisions, attendance and open shifts. The fixture reproduced the
T-26 staffing scenario with demand for two qualified RNs and 20 RN-minutes
from 09:10 to 09:20. The callout left one qualified
RN short for ten minutes. The draft and unqualified assignment kept the
request pending. Publishing the eligible float preserved the frozen roster
and closed the planned gap. Approving the closed 09:12 timecard left two
RN-minutes short. The full 23-test regression passed, including both live
openHR cases.

**Live QuickBooks Time v1 T-12 remains pending.** Run it on an authenticated
Time v1 test tenant with synthetic staff. Verify that coverage stays pending
until the provider publishes the qualified float, the published baseline
retains the original assignments, and the approved 09:12 arrival leaves
two RN-minutes short.

### QuickBooks Online sandbox TimeActivity evidence

**Passed on 2026-11-27.** The QA runner used Azure Key Vault credentials to
refresh Intuit authorization, created one synthetic employee TimeActivity
through the QuickBooks Online Accounting sandbox API, and fetched it by ID.
The readback matched TimeActivity `1073741824`, dated 2026-11-27, with eight
nonbillable minutes. A second run fetched the same ID, kept the original
receipt intact, and made no second create request. The repository runner
also passed the readback against that receipt. This verifies the Accounting
API write and read of a time entry. Live staff scheduling and approved
attendance still require the QuickBooks Time v1 test above.
[Sandbox runner at code commit `a972395`](https://github.com/opensoft/openPractice-code/blob/a972395c031860313aa23c8e2ea5c8f1378f1ad7/scripts/qbo_sandbox/timeactivity_smoke.py)

### T-13 Microsoft Teams Shifts validation evidence

**Local conformance passed** against a loopback fixture using Microsoft Graph
v1.0 response shapes and the same synthetic three RNs as T-12. The two
procedures needed two qualified RNs from 09:10 to 09:20, totaling 20 RN-minutes.
The callout left a ten-minute gap. Posting the open shift and approving a
claim with a draft assignment kept coverage pending. Sharing the float's
assignment closed the planned gap and preserved the original published roster.
The employee-confirmed float timecard was visible as raw clock evidence.
Manager confirmation made the approved 09:12–09:20 interval available and left
one RN short from 09:10 to 09:12, totaling two RN-minutes. The fixture also
covered missing copied-shift assignees, an ambiguous acknowledgment, competing
claims, and a recorded break.

The QA roster fixture defines two VAs, two LVNs, three RNs, two NPs, and two MDs.
Each run selects each participant's specialty and scheduled or float assignment.
A second loopback case rotated the RN assignments and verified that publishing
a float with the wrong specialty left coverage pending and the full ten-minute
gap open. [QA roster and loopback tests](https://github.com/opensoft/openPractice-code/tree/3e83272547ab21a81fe530766facd15c74ff34ad/src/open_practice/tests)

**Live T-13 passed on 2026-11-28.** The dedicated `openPracticeQA1` team had
Shifts and Time Clock enabled. The test used Microsoft Graph v1.0, three
synthetic accounts, and a 2026-11-26 fixture in the America/Denver time zone.

| Check | Live result |
| --- | --- |
| Publish the original roster | RN-01 and RN-02 met peak demand of two procedure-qualified RNs from 09:10 to 09:20, totaling 20 RN-minutes. |
| Publish the callout and post the open shift | The callout left a 10 RN-minute gap. Coverage stayed pending after the draft post and after RN-03 claimed the open shift with no published replacement assignment. |
| Approve and publish the float assignment | Microsoft Graph returned the approved claim and published RN-03 assignment. Coverage became confirmed and the revised roster showed zero planned shortfall. The frozen roster retained RN-01 and RN-02, and the revised roster showed RN-01 and RN-03. |
| Confirm attendance in Teams Shifts | Microsoft Graph returned `confirmedBy=manager` for the closed float timecard. The approved 09:12–09:20 interval left one qualified RN short from 09:10 to 09:12, totaling two RN-minutes. |

The live receipt recorded a pass at 15:27:17 UTC under run
`aa7db68b773042ca900ccff2499d4286`. Its SHA-256 digest is
`5282c171ee59a0f848c485cd6778265ff8de7132f4bd293940af8595ceb5a149`.

## Companion forks

- [ ] T-21 Fork frappe/hrms -> opensoft/openHR: preserve LICENSE/notices, add FORK.md, license GPL-3.0.
- [ ] T-22 Fork frappe/books -> opensoft/openBooks: preserve LICENSE/notices, add FORK.md, in-app source link (AGPL s13), license AGPL-3.0.
- [ ] T-23 Name-collision and trademark check for openHR and openBooks before public use.
- [ ] T-24 Scaffold companion apps openhr-ext and openbooks-ext (hooks/custom doctypes/fixtures) to keep forks thin.
- [ ] T-25 Set up upstream-merge CI cadence tracking frappe/hrms monthly releases.

## Validation

- [x] T-26 Run the AP-10 and AP-11 callout test through the adapter against the openHR fork with synthetic staff and appointments.
  - Book two procedures at one site. Both require a procedure-qualified RN from 09:10 to 09:20. Verify a peak demand of two and twenty RN-minutes.
  - Record one RN callout. Verify the exact ten-minute shortage, the affected procedures, and exclusion of an available RN who lacks the qualification. Post an open shift and verify the posting alone leaves coverage pending.
  - Confirm an eligible float RN in openHR for 09:10 to 09:20. Read the revised roster through the adapter. Verify the planned gap closes and the as-designed roster shows the original two assignments.
  - Reconcile the original RN's full interval and the replacement's 09:12 arrival in openHR. Read back the approved attendance through the adapter. Verify as-built records the replacement's 09:12 to 09:20 interval and the variance report shows one qualified RN short from 09:10 to 09:12, or two RN-minutes.
- [ ] T-27 Microsoft Partner package evaluation (Core $925/yr) as Shifts/Graph integration testbed tenant.
- [ ] T-28 Owner ratification (`owner-ratification.md`) after strict validation.

### T-26 validation evidence

**Passed on upstream Frappe HRMS on 2026-11-26.** The dedicated test site ran Frappe 15.122.0, ERPNext 15.121.6 and HRMS 15.64.3. The live `test_t26_against_openhr_test_site` case passed through Frappe REST with synthetic staff and appointments. The fixture submitted roster and attendance records to the scheduling DocTypes using a separate HR Manager account. [Acceptance test at adapter commit `dff8136`](https://github.com/opensoft/openPractice-code/blob/dff8136967e3493be46e2376ca980da471049b67/src/open_practice/tests/test_openhr_adapter.py#L224)

**Passed on the openHR fork on 2026-11-26.** A separate test site built from `opensoft/openHR` at `v15.64.3` (`0a54d2c`) passed the same live test case with adapter revision `dff8136` and the same synthetic staff and appointments. The adapter read back the confirmed roster revision and approved attendance through the fork site's REST API. [Tested openHR revision](https://github.com/opensoft/openHR/commit/0a54d2c7b8b6043e2705b6f557b6ed65260670dd)

**Passed with native openHR Shift Assignments and Attendance on 2026-11-26.** The same synthetic fixture ran on the openHR fork at `0a54d2c` with the `openhr_ext` companion app at version 0.1.0. The companion app fed the adapter from submitted native records. Posting the shift and saving the draft float assignment left coverage pending. Submitting the float's Shift Assignment closed the planned gap and preserved the published roster. Submitted Attendance showed the float arriving at 09:12, leaving a two-RN-minute shortfall. Canceling that Attendance withdrew the approval. Submitting a replacement Attendance record with the same times restored the two-minute result. [Native acceptance test at code commit `9949377`](https://github.com/opensoft/openPractice-code/blob/99493772675812edfddaff46b9dddc753cf3bf3e/src/open_practice/tests/test_openhr_native.py#L36)

**Shared provider conformance passed on the openHR fork on 2026-11-26.** The suite ran against a dedicated site rebuilt from openHR `0a54d2c` with Frappe 15.122.0, ERPNext 15.121.6, HRMS 15.64.3, and `openhr_ext` 0.1.0. All 14 tests passed, including the live projection and native T-26 cases. The native readbacks conformed to `openpractice-staff-schedule-provider/1.0` before and after float confirmation, during partial attendance reconciliation, and after attendance cancellation and correction. The roster stayed frozen and the approved 09:12 arrival left a two-RN-minute shortfall. Worker, leave, and raw clock-event messages were verified with synthetic schema fixtures.

| Step | Verified on both sites |
| --- | --- |
| Two overlapping procedures from 09:10 to 09:20 | Peak demand was two procedure-qualified RNs and 20 RN-minutes. |
| Callout, open-shift post and draft replacement | Both procedures were affected. The available RN without the qualification was excluded. The request stayed pending with one qualified RN short for the full ten minutes. |
| HR Manager submitted the roster revision | The adapter read back the confirmed float and the planned gap closed. The published roster retained both original assignments. |
| HR Manager approved the float's 09:12 arrival | The adapter read back the approved 09:12 to 09:20 interval. One qualified RN was short from 09:10 to 09:12, totaling two RN-minutes. |

## Seam and degraded operation

- [ ] T-29 Define staff reference cache schema (worker id, role, skill level, certifications with expiry, home clinic) and snapshot policy: rolling 28 days roster, 90 days approved leave.
- [ ] T-30 Implement provider sync: 15-minute polling, webhooks where supported, nightly full reconcile; staleness timestamp on cached reads; TTL 4 hours before the uncertainty flag fires.
- [ ] T-31 Implement the offline write outbox: postOpenShift and other write-backs queue when the provider is unreachable and drain on reconnect, following the ehr-boundary demographic-outbox pattern.
- [ ] T-32 Implement provisional mode for outages over 24 hours: manual staff-present capture and walk-in coverage behind tiered sign-off; provisional records persist post-resync as audit entries marked superseded.
- [ ] T-33 Implement reconciliation on reconnect: HR wins on employment facts with silent cache overwrite; provisional attendance advisory for variance only, never payroll; human review for provisional records touching patient-care documentation or pay; credential validation gets STRICTER, never looser, when the cache is stale beyond TTL.
