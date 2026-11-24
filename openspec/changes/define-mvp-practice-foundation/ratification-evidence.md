# Owner review and validation evidence

Status: draft

This packet prepares the full foundation for exact-revision owner review. The owner selected the full foundation scope and directed that Feature 1 include both reversible same-person linking and duplicate deactivation with their safeguards. On November 24 the owner confirmed Shape A and the recommended preparation dispositions. The exact-revision ratification is recorded by the owner in [owner-ratification.md](owner-ratification.md).

## Feature 1 scope

| Registry behavior | Evidence to require |
|---|---|
| Shared Party and verified reuse | A patient role refers to the same party used by the human staff, guarantor, subscriber or contact roles where identity has been verified. Scoped matching includes no-DOB candidates and no automatic merge. Concurrent role creation preserves the invariant. |
| Authority, confidentiality and lifecycle | Role-specific visibility and permissions, cross-role audit, effective restrictions, named human grants, dated owner links with no grants from ownership, appropriate own-record checks, and attributable review of the other roles of a deceased patient. |
| Link, unlink and duplicate deactivation | Qualified named human tier-4 decisions, verified evidence, scoped view, commit-time rechecks, audited refused bypasses, and owned review. Test an alternate path, a separated pair with unsettled safeguards, independently authorized scoped release, EHR conflict, and hard-delete refusal for related identity history. |
| Role-scoped hard deletion | Remove eligible patient-only data and audit values, preserve separately authorized shared facts, leave a verifiable tombstone and explain the removal scope. Indeterminate or linked activity blocks deletion. |
| Cross-feature safeguards | Synthetic inputs exercise the linked, conflicting, unavailable and indeterminate cases in Feature 1. The actual EHR, gateway, accounting and revenue-cycle owners prove their application of the safeguards before enabling their capabilities. |

The first QA site uses synthetic fixtures. It can exercise the registry commands with named staff identities, assigned grants and review work. It can prove EHR or downstream refusal paths with deterministic synthetic inputs.

## Dispositions for the owner

Use the exact draft text for the owner decision. Document accepted, changed or rejected calls in the owner record. The existing PR #8 calls remain for owner review alongside the Feature 1 refinements.

| Calls | Decision under review |
|---|---|
| F-1 to F-4, with F-5 noted | Clinical facts retraction reason codes, undefined or absent reason handling, and billing effects. F-5 records the same-link and subject safety requirement. |
| F-6 and F-7 | Shared identity, reversible grouping, full duplicate controls and own-record authority. Read F-6 together with the proposed refinements F-26 to F-28. |
| F-8 to F-10 | Money format, USD-only 1.0, rounding and currency scope per batch. |
| F-11 | Suppression of restricted customer-detail fields and a fresh check before send or retry. Hold frozen unsafe messages, resolve unknown delivery, and distinguish redacted supersession from delivered-field removal. |
| F-12 and F-13 | Dated owner links, provenance and notices, handling first-ever bootstrap and improper links, and practice manager authority. Ownership grants no permissions. |
| F-14 to F-19 | Accounting payload service categories, paired payer classes, neutral labels, payer-class coverage, bank-debit ownership, and customer-detail posting dispositions. |
| F-20 and F-21 | Claim acceptance as the billing event and related ledger transitions. Shape A uses the core handoff for shared controls. A stalled route requires a separately governed, ratified replacement. |
| F-22 and F-24 | Pending corrections when a link ends and one-time republication after a valid current review. Only the compared snapshot applies at confirmation. Replay, terminal state and rejected-DOB suspension protect against publication. |
| F-23 and F-25 | Unidentified receipts and unbilled receivable write-offs at the accounting boundary. |
| F-26 | End one edge and recompute membership. Keep restrictions, holds and registry conflict checks for unresolved splits. A separate, disinterested tier-4 reviewer qualifies each release. This can keep a small practice's action waiting until it has a qualified reviewer. |
| F-27 | Scope hard deletion to the patient role, give a clear confirmation of what remains under other roles, and keep deletion blocked when history or dependencies cannot be cleared. |
| F-28 | Include registry-relevant authority and owned work in Feature 1. Verify dependency guards with synthetic inputs and gate live activation in the owning feature. |

See the [design and owner rulings](design.md) for the complete text. Preparation covers Shape A, the accepted calls F-1 to F-4, F-6 to F-10, F-12 to F-20, F-22, F-23, F-25 to F-28, F-5 noted, and the agreed F-11, F-21, F-24 and EHR versioning revisions. The exact-revision decision remains the owner's field.

## Adoption and handoff

The practice draft is based on PR #8 at `f8c0532532ce19c2a4fc225656e0bb7e1b934943`, which includes PR #7 at `e746f5838b946d25efe7fc1359816fabd8037137`. The companion candidate is prepared on the exact openChart PR #2 base. Its adoption task still requires the ruling-93 work from Practice #7 to land and additionally requires the revised foundation schema bytes to land. PR #7's intermediate schema digests alone cannot qualify the revised pair. Carry this paired comparison to the owning branches and rerun it if either side changes. The partner's decisions and ratification are its own.

The umbrella's Feature 1 handoff requires its own exact-revision approval and compatible artifact evidence. Live EHR use follows the implemented companion and live-activation controls. The reimbursement change still needs to reconcile the narrow erroneous-record exclusion before its separate retention handoff. The synthetic registry hard-delete test has to establish eligible state explicitly.

## Exact paired review evidence
Validated at `2026-11-24T11:31:56+00:00` UTC. The practice artifact digests below identify the draft for owner review.
- Practice review parent `2451d0890cf89f1876f04e4578d71b1261fa9911`.
- OpenChart candidate `32ef1ede6854784bb4b29d414236a6fedf0f5b99` on the local companion branch.
- Both EHR contract IDs and canonical digests matched. Namespace and issuer checks matched the reciprocal openpractice and openchart namespaces and the stable per-deployment issuer with identifiers compared by the full triple.
- Exact wire 1.0 selection rejects unadmitted minors and undeclared fields. The unknown-code fallback remains exercised within an admitted 1.0 message.
- The nested unlinked-identity snapshot and all its referenced definitions match the previous patient-link schema. The separately governed gateway draft still needs to reconcile its prior digest references before its own activation.

| Contract | Candidate schema SHA-256 | Companion result |
|---|---|---|
| openpractice-ehr-patient-link/1.0 | 82d87592de7101a5aa0ac4a4c926a4c5ebced906f48508ca4961685905f600a3 | Matches companion PL-04 |
| openpractice-ehr-clinical-facts/1.0 | 9ae9648bd803f3bae6ee04fc0123409b741b8466a9a013f19d4dd91def07900d | Matches companion PL-04 |

### Validation output

```text
OpenSpec version 1.2.0
$ openspec validate define-mvp-practice-foundation --strict
Change 'define-mvp-practice-foundation' is valid
$ openspec validate define-openpractice-boundary --strict
Change 'define-openpractice-boundary' is valid
openpractice-ehr-patient-link-1.0.schema.json: OK
openpractice-ehr-clinical-facts-1.0.schema.json: OK
openpractice-accounting-integration-1.0.schema.json: OK
```

### Synthetic Draft 2020-12 checks

These structural checks do not exercise runtime handlers or delivery.

```text
PASS patient-link 1.0 confirmation
PASS patient-link 1.0 confirmation without applied version
PASS patient-link rejects unadmitted 1.1 same shape
PASS patient-link rejects undeclared envelope property
PASS patient-link confirmation needs review ID
PASS clinical facts 1.0 withdrawal
PASS clinical facts unknown code within admitted envelope
PASS clinical facts absent code in admitted envelope
PASS clinical facts rejects unadmitted 1.1 same shape
PASS clinical facts rejects unsupported 2.0 wire schema
PASS clinical facts rejects malformed version
PASS clinical facts rejects extra envelope property
PASS clinical facts rejects extra payload property
PASS clinical facts admits entered-in-error for a Condition
PASS clinical facts rejects entered-in-error at Encounter level
PASS clinical facts 1.0 capability result
PASS clinical facts 1.0 no billable facts result
```

### Prepared practice artifact SHA-256s

Paths are relative to this change. The evidence file is outside its own digest list. These checksums identify the artifacts before the owner completes the decision fields and proposal status. Substantive text changes require a new comparison.

```text
b90a3a5e75577b9a75aacf25ee861982457fad5dd4bd8ecb5429feccae7dc5e2  .openspec.yaml
107acd52cef4d67fdf7bb5ac0e2de0dc2ae3747a221a349147a279aefc183e72  closure-evidence.md
5ef5176056228bdd80aeeb285da4e4a4478ad3c2f476dd70b346144ce3ceb5f2  contracts/openpractice-accounting-integration-1.0.schema.json
9ae9648bd803f3bae6ee04fc0123409b741b8466a9a013f19d4dd91def07900d  contracts/openpractice-ehr-clinical-facts-1.0.schema.json
82d87592de7101a5aa0ac4a4c926a4c5ebced906f48508ca4961685905f600a3  contracts/openpractice-ehr-patient-link-1.0.schema.json
0ccb4e294d8d00ed9e9359c565bc0617e2e6c9b392710e652872ee98d0a4d634  contracts/SHA256SUMS
dc267a758ad8466a8759b603794d4b15086a5b6bd76baf72415610c739116c6b  design.md
b788a1203d6b339207cce9594ed24fe23faecfd811589834d434aa1d6d02097b  owner-ratification.md
beb99f2a113dd962f6e7ea58f19f581037b8c8bf28fc86b4967ad0bf5cf9ee92  proposal.md
1cd5ef7558af27aa6a157494d167cb9c99f7c3062c98ff52c847f5492ec80c40  specs/accounting-integration/spec.md
28e037df8b993ea5aa2ec31f20b69a16f57904d4a0a15befeb234774b8948060  specs/billing-accounting-boundary/spec.md
445219f8d3e423cd4719137d16d820985875c52d988827c0430e3a83aeae4eac  specs/ehr-boundary/spec.md
e7c5d54096dea31c53722300e8b8f61946bac356ec80888ed86c71a8f3054644  specs/patient-registry/spec.md
c90729a2542e55d912b834f4d879ff713c32d580bf8429c5e29575324fde1b66  tasks.md
```
