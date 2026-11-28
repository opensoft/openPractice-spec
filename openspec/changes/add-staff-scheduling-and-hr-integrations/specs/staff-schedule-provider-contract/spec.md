## ADDED Requirements

### Requirement: Provider confirmation and attendance govern staffing variance
The staff-schedule provider SHALL return the published roster, revisions with effective assignment intervals and source revision identity, and attendance with HR-approved worked intervals and unresolved exceptions. The openHR adapter SHALL supply these records for the MVP. openPractice's variance module SHALL track as-designed, as-revised and as-built separately under AP-11 and match each to the exact AP-10 demand intervals. It SHALL treat an open-shift posting as pending until the provider confirms the replacement in the revised roster.

openHR SHALL own confirmation of workforce assignments and reconciliation of attendance. openPractice SHALL own staff-to-visit assignments and the affected-visit list. An open-shift request SHALL be limited to the workforce requirements, location, time window and opaque correlation reference needed for staffing.

#### Scenario: openHR confirms an RN replacement and returns late attendance
- **GIVEN** two overlapping procedures need two procedure-qualified RNs from 09:10 to 09:20 and the published roster assigns two qualified RNs
- **WHEN** one RN calls out
- **THEN** openPractice shows the affected appointments and a shortage of one qualified RN for the exact ten-minute window, and excludes replacement candidates without the qualification
- **WHEN** openPractice posts the open shift
- **THEN** the request is pending and the planned gap is still visible
- **WHEN** the manager confirms an eligible float RN in openHR for 09:10 to 09:20
- **THEN** the revised roster returned by openHR supplies the confirmed assignment, the planned gap closes, and the as-designed plan shows the original assignments
- **WHEN** openHR returns approved attendance for the original RN from 09:10 to 09:20 and the replacement from 09:12 to 09:20
- **THEN** as-built shows the approved interval and the variance report shows one qualified RN short from 09:10 to 09:12, totaling two RN-minutes

#### Scenario: The provider is unavailable before a replacement is confirmed
- **GIVEN** the published roster is cached and an open-shift request awaits provider confirmation
- **WHEN** the provider becomes unreachable and staff record provisional attendance
- **THEN** openPractice shows the cached and provisional evidence with its status, keeps the request pending, and applies the confirmed revised assignment and approved attendance when openHR returns them

### Requirement: Staff-schedule adapters use the versioned provider contract

The adapter boundary SHALL use the workforce-only message schema in `contracts/staff-schedule-provider/1.0/schema.json`. The contract defines worker qualifications, approved leave, roster and reconciliation evidence, raw clock events, and open-shift requests and state. An adapter SHALL distinguish an unavailable feed from a successful read with no records. Provider-specific tests SHALL exercise the shared conformance checks with synthetic data for the feeds the adapter supplies.

#### Scenario: A provider's open-shift readback conforms to the shared schema

- **GIVEN** a provider has published a roster and accepts a synthetic open-shift request
- **WHEN** openPractice reads the roster and shift state before and after the provider confirms the replacement
- **THEN** both readbacks validate against the same versioned schema, the published snapshot stays frozen, and only the confirmed state refers to the revised roster

#### Scenario: The adapter receives only raw clock events

- **GIVEN** raw clock events exist for a worker and the provider has no approved reconciliation for the current roster
- **WHEN** the adapter reads the worker's clock and staffing evidence
- **THEN** the clock events remain separate and the staffing record has no approved attendance

### Requirement: QuickBooks Time publication and approval qualify staffing evidence

The QuickBooks Time adapter SHALL normalize the documented Time v1 resources through the shared provider contract. The adapter SHALL freeze the first captured published roster for a configured series and append new observations as revisions. It SHALL post coverage requests as unassigned drafts and count only published assignments that meet the configured workforce qualification, interval and headcount. Approved attendance SHALL use closed, timed entries within the provider's worker approval date. Raw clock evidence SHALL remain available independently of approval.

#### Scenario: QuickBooks Time publishes the replacement and later approves attendance

- **GIVEN** the adapter has captured the published roster for two overlapping procedures and one qualified RN calls out
- **WHEN** the adapter posts the coverage request as a draft or QuickBooks Time returns a draft replacement
- **THEN** coverage stays pending with one qualified RN short from 09:10 to 09:20
- **WHEN** QuickBooks Time publishes the eligible float's assignment
- **THEN** the revised roster covers the gap and the original published roster stays frozen
- **WHEN** QuickBooks Time approves the closed timecard with a 09:12 float arrival
- **THEN** the shared attendance readback shows a two-RN-minute shortfall

### Requirement: Microsoft Teams Shifts publication and manager confirmation qualify staffing evidence

The Teams Shifts adapter SHALL scope roster readback to a configured team, scheduling group and workforce mapping. It SHALL freeze the first captured shared roster for the series and record subsequent shared assignments as revisions. The adapter SHALL post open coverage as a draft, and SHALL report a request as confirmed only when an approved claim for that open shift maps unambiguously to newly shared, qualified assignments that cover the requested interval and headcount. Shifts without a mapped assignee SHALL supply no coverage. A closed timecard SHALL count as approved attendance only when Graph reports manager confirmation, with recorded breaks removed from worked intervals. Raw clock punches SHALL remain separately available.

#### Scenario: Teams Shifts publishes the float and confirms late attendance

- **GIVEN** two overlapping procedures require two qualified RNs from 09:10 to 09:20 and the frozen shared roster contains the original two assignments
- **WHEN** one RN calls out and the manager prepares the float's draft assignment
- **THEN** the request stays pending with one qualified RN short from 09:10 to 09:20
- **WHEN** the approved claim is reflected in a shared shift for the qualified float
- **THEN** the revised roster closes the planned gap and the frozen roster retains the original two assignments
- **WHEN** the manager confirms the original RN's 09:10 arrival and the float's 09:12 arrival on closed timecards ending at 09:20
- **THEN** approved attendance shows one RN short from 09:10 to 09:12, totaling two RN-minutes
