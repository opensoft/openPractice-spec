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
