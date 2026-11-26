# Design: Staff Scheduling and HR Integrations

Research conducted 2026-11-26 with the product owner. Sources: Microsoft Learn and support documentation, Microsoft Dynamics 365 Licensing Guide, frappe/hrms and frappe/books repositories, OrangeHRM and Horilla project documentation, QuickBooks/Intuit product updates, and healthcare staffing literature (ANA staffing principles, Kaufman Hall, demand-based staffing research).

## 1. The three-state roster model

Staff scheduling in a medical practice is three data states, not one schedule:

1. **As-designed (roster).** The published staff plan, built from forecast visit volume and frozen some window before the shift. Maps to Shifts' shared schedule and Frappe HRMS Shift Assignments/Roster.
2. **As-revised (coverage plan).** The roster after day-of edits: who picked up a callout, who floated in, who was sent home. Comparing as-designed vs as-revised measures forecasting quality.
3. **As-built (actual).** Reconciled clock-in/out versus roster. Feeds payroll and cost analysis. Comparing as-revised vs as-built surfaces attendance problems.

Variance metric: hours variance % = (actual - scheduled) / scheduled. Recurring shortfalls on the same weekdays mean schedule-to-demand failures; recurring overtime tails mean coverage gaps.

Implication for openPractice: the EHR/practice-management layer needs all three states to do gap detection (which pair to compare depends on the question), so the provider contract MUST expose the roster revision history or at minimum revisions since a timestamp.

## 2. Demand derivation from the visit calendar

Coverage requirements are computed, not templated. Method:

- Each appointment type carries: total duration; duration per staff role (physician, RN, MA, tech — these overlap and differ); skill-mix requirement (minimum nurse level, required certifications); equipment + usage sub-window; room type + turnover buffer; and upfront work (pre-visit labs, eligibility checks) consumed before the slot starts.
- The engine converts the visit calendar into required staff-minutes per 30-minute interval per role and skill level. This is the timed-task approach from the nursing staffing literature; industry practice assigns processing times per task per visit type.
- Demand is left-skewed and bursty. Staffing exactly to mean demand fails on heavy days, so the model pairs a fixed baseline with a flexible reserve (float pool, per-diem). Research on rostering shows a baseline at average demand is only cost-effective when paired with flexible capacity.
- Skill mixing: a staff member splits time across visit types in one interval, and the scheduler accounts each piece against the right appointment.

## 3. Coverage gap resolution ladder

When a callout lands, gap detection compares confirmed staffing against the coverage requirement and emits a concrete deficit: role, skill, interval, minutes short. Suggested resolutions in cost order:

1. Float pool or cross-trained staff already on site.
2. Per-diem or open-shift offer cascade (postOpenShift on the provider contract).
3. Overtime.
4. Appointment triage: reschedule lowest-acuity visits last. This is the only ladder step that touches the patient calendar, and it MUST require tiered sign-off.

Backfill is skill-aware: a shortfall of a procedure-trained RN is not covered by any RN. The contract therefore requires skills-and-certifications with expirations so the engine can reject ineligible backfill and block scheduling credential-dependent work after a license lapses.

## 4. Boundary: openPractice never owns rostering

The worker master, skills, leave, roster and clock events are owned by external systems. All scheduling UX (open shifts, swaps, time-off approvals, clock-in) stays there too. openPractice exposes one vendor-neutral contract, `openpractice-staff-schedule-provider/1.0`:

- `getWorkers(location, dateRange)` - master with role, skill level, certifications + expirations.
- `getLeave(location, dateRange)` - approved PTO, subtracted from capacity before gap detection.
- `getRoster(location, dateRange, state)` - as-designed and as-revised rosters.
- `getClockEvents(location, dateRange)` - as-built attendance feed.
- `getOpenShifts(location, dateRange)` and `postOpenShift(shift)` - write-back for the resolution ladder.

Conformance tests use synthetic fixtures only. Adapters reach vendors through public APIs (REST/OData/Graph); no direct database access, matching the accounting-integration boundary in `define-mvp-practice-foundation`.

## 5. Adapter portfolio (top 10)

Five open source and five commercial, selected for US medical-practice relevance and coverage of the boundary feeds.

### Open source

| # | System | License | Coverage | Rationale |
|---|---|---|---|---|
| 1 | Frappe HRMS (openHR fork) | GPL-3.0 (verified `license.txt`) | Master, skills, PTO, roster, clock | Primary reference implementation. Shift Type/Assignment/Schedule/Location/Request doctypes, Roster grid with drag-and-drop and swap, mobile geolocation check-in -> auto-attendance, Employee Skill Map, staffing plans. v16.60.0 released 2026-11-17. |
| 2 | OrangeHRM Starter | GPL-3.0 | Master, PTO, time tracking, attendance | Most-established open-source HR; REST API; largest installed base. No rostering in Starter (Rostero is a separate commercial product). |
| 3 | Horilla | LGPL-2.1 | Master, PTO, payroll, training | Modern Django, very active (v2.4.0, 2026-11). Milder copyleft. |
| 4 | Odoo Community HR | LGPL-3.0 | Master, time off, attendance, recruitment | Huge ecosystem; payroll and GPS attendance are Enterprise-only. |
| 5 | TimeTrex Community | Open source CE | Roster, clock, payroll (US/Canada) | Deepest open-source scheduling/time-clock engine; thin as HR master; adapter targets roster + attendance feeds. |

### Commercial

| # | System | Coverage | Rationale |
|---|---|---|---|
| 6 | Microsoft D365 HR + Shifts | Master, skills/certs, PTO, roster, clock | Enterprise anchor. Shifts: Graph API `/teams/{id}/schedule`, Schedule.Read.All / Schedule.ReadWrite.All, draft vs shared shift states (maps to as-designed vs as-revised), open shifts with cross-team sharing, time clock. D365 HR: multi-legal-entity worker master with skills and certifications; one deployment holds all MedxMSO clinics. |
| 7 | QuickBooks Workforce/Time | Roster, clock, PTO, profiles (Elite) | Largest US small-business payroll footprint; scheduling rebuilt inside QBO (2026-08); GPS time clock; shares auth and plumbing with LedgerLinc. |
| 8 | BambooHR | Master, PTO, onboarding, documents | HR-first pick for small practices; commonly paired with Gusto/ADP/QuickBooks payroll. |
| 9 | Gusto | Master (payroll-centric), PTO, time | Most-used HR platform for 1-50-employee US businesses; multi-EIN support matches the multi-clinic MSO legal structure. |
| 10 | ADP RUN | Master, PTO, time, HR packages | Most entrenched payroll inside US medical practices. |

Bench (swap candidates): Deputy, Homebase, Connecteam (scheduling-first, disproportionately healthcare); HR for Health / CEDR (healthcare credential tracking); Rippling; UKG Ready; Paychex.

### Build order

1. Frappe HRMS (openHR) - ships with openPractice; demo environment.
2. QuickBooks Time - highest overlap with real US clinic operations; LedgerLinc reuse.
3. Microsoft Graph (Shifts/D365 HR) - enterprise story; adapter patterns already researched.
4. Gusto - simple API; multi-EIN for MSO.
5. OrangeHRM / Horilla - open-source goodwill; low effort.
6-10. ADP, BambooHR, Odoo, TimeTrex - built on adoption demand.

## 6. Companion forks: openHR and openBooks

License findings (verified from upstream repositories 2026-11-26):

| Product | Upstream | Upstream license | Fork license | Extra obligation |
|---|---|---|---|---|
| openHR | frappe/hrms | GPL-3.0 | GPL-3.0 | Source on distribution; network use does not trigger. |
| openBooks | frappe/books | AGPL-3.0 | AGPL-3.0 | Section 13: modified network service must prominently offer source (in-app source link). |

Note: the frappe.io marketing site calls Frappe HR "AGPL-3.0" but the repository `license.txt` is GPL-3.0; the repository file governs. The Frappe Framework itself is MIT.

Fork compliance checklist (both forks):

1. Keep the original LICENSE intact and verbatim; keep upstream copyright notices.
2. Add opensoft copyright notices on modified files, with modification dates (GPL-3 section 5a).
3. Add a NOTICE/FORK.md stating fork relationship and upstream attribution.
4. License the whole fork under the upstream license; no proprietary files mixed in.
5. Public repository satisfies source availability; openBooks adds an in-app source link for AGPL section 13.
6. Do not use "Frappe" in product names (trademark; GPL-3 section 7e permits enforcement). Verify openHR and openBooks names for collisions before committing.

Governance: keep forks thin. MedxMSO and openPractice-specific additions (clinic credential fields, coverage-related doctypes, US payroll stubs) live in companion Frappe apps (openhr-ext, openbooks-ext) installed on the same bench via hooks, custom DocTypes and fixtures, also GPL/AGPL. Direct fork edits are reserved for changes that cannot be expressed as hooks. Contribute generic improvements upstream to shrink the delta; treat each upstream release (hrms ships roughly monthly, with breaking changes) as a dependabot-style PR with CI.

## 7. Microsoft licensing findings (MedxMSO cost model)

D365 Human Resources commercial pricing (2026): full license $135/user/month, minimum purchase 5 full licenses (purchase floor on the base SKU only); Self Service $4/user/month covers employees AND managers approving leave for direct reports. A 10-clinic, 150-staff MSO: 5 x $135 + 150 x $4 = $1,275/month (~$15.3k/year). Multi-company is native via legal entities with shared worker master, cross-entity employment records, and per-entity data access restriction. Licenses are assigned through the Microsoft 365 admin center (required for F&O family since 2025-11).

Microsoft AI Cloud Partner Program benefit packages (Launch $350, Core $925, Advanced $3,000, Expanded $4,125/year) contain NO D365 HR licenses. Relevant contents: Teams Enterprise seats (Launch 5 / Core 15) include Shifts; Business Central Premium seats (Launch 4 / Core 15) include BC's light built-in HR (employee records, qualifications, absence registration). Partner licenses are internal-use only and lapse with membership. Conclusion: partner packages are a cheap integration testbed for the Shifts/Graph adapter, not a production HR path.

Comparison anchor: Frappe HRMS self-hosted is infrastructure-only (~$40/month VPS or Frappe Cloud from $5/site/month) with no per-user fees - the openHR path is ~30x cheaper than D365 HR at 150 staff, which is why it is the primary reference implementation.
