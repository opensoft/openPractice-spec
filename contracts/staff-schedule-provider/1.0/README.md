# Staff schedule provider 1.0

Adapters normalize workforce evidence from their provider's supported API.
Each message carries `contract: openpractice-staff-schedule-provider/1.0`,
`kind`, and `data`. The [JSON Schema](schema.json) uses Draft 2020-12.
Validate its date and date-time formats as well as its structure.

| Message | Provider operation | Evidence |
| --- | --- | --- |
| `workers` | Read scoped workers | Workforce IDs, roles, skills and levels, certifications and expiry dates. A null expiry means the provider explicitly identifies the certification as nonexpiring. |
| `leave` | Read approved leave | Provider leave references and approved workforce intervals that overlap the scope. |
| `staffing` | Read a roster series and its current attendance reconciliation | The published roster, ordered confirmed revisions, the current revision's approved attendance and exceptions, and confirmed coverage links. |
| `clock_events` | Read scoped clock events | Raw in/out evidence and source references. HR approval is represented by `staffing.attendance`. |
| `open_shift_request` | Post an open shift | Site, role, qualification, headcount, interval and an opaque random UUIDv4 in lowercase hex. |
| `open_shift_state` | Read the status of an open-shift request | The request and its pending or confirmed state. Confirmation identifies the current revised roster reference. |

## Rules across messages

- Use offset-bearing RFC 3339 timestamps. All windows are half-open and end
  after they start. Clock events fall inside the query window. Leave retains
  its provider-approved bounds.
- Keep workforce identifiers stable for a provider connection. Use source
  references for roster snapshots and reconciliation exceptions. Send only
  workforce requirements and evidence. Keep appointments and patients in
  openPractice.
- Publish one frozen baseline per roster series. Return confirmed revisions as
  full snapshots in source order, with distinct references. The last revision
  is current. Until the provider confirms a revision, the published snapshot
  remains current. A replacement, withdrawal, or correction to a confirmed
  roster creates a later snapshot.
- Leave attendance null until there is HR-approved reconciliation for the
  current roster. A reconciliation can contain both approved worked intervals
  and unresolved exceptions. An empty approved interval list is valid when
  there are no approved worked intervals. Approval for an old roster and raw
  clock events do not establish worked time for the current roster.
- Reposting the same open-shift request is idempotent. Reject reuse of the
  correlation ID with different requirements. A successful post and a draft
  assignment both leave a request pending. Confirm it only when distinct
  assignments linked in the current revised roster cover the site, role,
  qualification, complete interval, and headcount. A pending state has a null
  roster reference.
- A successful empty response means the supported feed returned no records
  for the requested scope. Report a feed as unavailable when the adapter
  cannot read it.

## Conformance

The code leg's shared harness checks message shape, time windows, roster
history, reconciliation scope, and open-shift state. A provider's synthetic
fixture drives its native writes and feeds the readback into that harness.
T-26 covers the openHR scheduling slice with the published roster, approved
revisions, open shifts, and reconciled attendance. Worker, leave, and raw
clock feeds have canonical fixtures and require their own provider acceptance
when an adapter implements those feeds.
