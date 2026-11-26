Status: draft

## Why

openPractice must schedule patient visits against real staffing, but owning staff scheduling inside openPractice duplicates mature HR systems and adds nothing to the product's core value. A research session with the product owner on 2026-11-26, framed around the MedxMSO operating model (10 clinics, roughly 15 staff each), established four facts that this change records as governed requirements:

- **Coverage demand comes from the visit book.** Staffing requirements must be derived from the patient visit calendar: each visit type is converted into staff-minutes per 30-minute interval per role and skill level. Fixed staffing templates are insufficient.
- **Staff rosters have three states, not one.** As-designed (the published roster), as-revised (the day-of coverage plan after callouts), and as-built (reconciled attendance). The variance between states is the signal that feeds gap resolution, payroll and forecasting accuracy.
- **Staff scheduling lives in external systems.** The worker master, skills and certifications, leave, the roster and the time clock are owned by HR and scheduling systems. openPractice consumes them through one vendor-neutral contract and never owns roster editing.
- **Reference implementations are our own open-source forks.** openHR (fork of frappe/hrms, GPL-3.0, verified from `license.txt`) and openBooks (fork of frappe/books, AGPL-3.0, verified from `LICENSE`) are companion products, integrated via API only, consistent with the openPractice model of integrating to Frappe apps through their REST APIs.

Decisions recorded here are research outcomes, not ratification: the change stays draft until the owner completes `owner-ratification.md`.

## What Changes

- **`staff-schedule-provider-contract`.** One versioned contract, `openpractice-staff-schedule-provider/1.0`, defines the boundary to any staff system: worker master with role and skill level, skills and certifications with expirations, leave and PTO, the roster by location and time range, open shifts, and clock events. Adapters reach systems through their public APIs only — no direct database access.
- **`coverage-requirement-engine`.** openPractice owns demand: appointment types carry duration per staff role, skill-mix requirement, equipment usage windows and room turnover buffers; the engine converts the visit calendar into coverage requirements per interval per role and skill. This is the only scheduling mathematics openPractice owns.
- **`roster-variance-tracking`.** The three-state model with gap detection: callouts are compared against coverage requirements to produce a concrete deficit (role, skill, interval, minutes), and a resolution ladder is suggested in cost order — float pool, per-diem or cross-trained staff, overtime, then appointment triage as the last resort.
- **`staff-adapter-portfolio`.** Ten built-in integrations, five open source and five commercial, in a defined build order (see `design.md`, section "Adapter portfolio").
- **`companion-forks`.** openHR and openBooks governance: AGPL/GPL notices preserved, forks kept thin with companion Frappe apps holding MedxMSO-specific doctypes, and generic improvements contributed upstream.

## Non-goals

- **Roster editing, leave approval or time-clock capture inside openPractice core.** These stay in the connected staff system (Shifts, Frappe HRMS, QuickBooks Time, and the rest of the portfolio).
- **Payroll, benefits, compensation or HR document management.** openBooks covers accounting-side needs; full payroll remains the accounting or payroll system's job.
- **In-process calls into Frappe apps.** openPractice integrates via REST API only, keeping fork licensing obligations simple.
- **Any patient data in the staff-schedule contract.** The contract carries staff identity and availability only; visit context stays in openPractice.
- **Building all ten adapters for the MVP.** The MVP builds the Frappe HRMS adapter (openHR) only; the portfolio defines the contract surface the rest must satisfy.
- **Credentialing bodies, licensing-board verification or payer enrollment.** Skill and certification records are data; verification workflows are out of scope.

## Capabilities

### New Capabilities

- `staff-schedule-provider-contract`: the versioned boundary contract, canonical schema, and conformance tests any adapter must pass.
- `coverage-requirement-engine`: visit-type resource model and demand computation per interval, role and skill level.
- `roster-variance-tracking`: three-state roster comparison, gap detection, resolution ladder, and variance analytics for forecasting accuracy.
- `staff-adapter-portfolio`: the ten-system integration plan and build order.
- `companion-forks`: openHR (GPL-3.0) and openBooks (AGPL-3.0) fork governance and update cadence.

### Modified Capabilities

None. This change references appointment scheduling and the EHR boundary from `define-mvp-practice-foundation` without modifying them.

## Impact

- **New external contract:** `openpractice-staff-schedule-provider/1.0` with canonical schema in `contracts/` when authored.
- **Data:** staff party records in openPractice are links to external systems of record, not copies; visit-type resource requirements are new owned data.
- **Licensing:** openHR and openBooks inherit upstream copyleft; fork-compliance obligations are listed in `design.md` and repeated in `tasks.md`.
- **Runtime:** no runtime code is added by this change.
