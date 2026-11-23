# Owner review and validation evidence

Status: draft

This packet prepares the full foundation for exact-revision owner review. The owner selected the full foundation scope and directed that Feature 1 include both reversible same-person linking and duplicate deactivation with their safeguards. The exact-revision decision and handoff choice are recorded by the owner in [owner-ratification.md](owner-ratification.md).

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
| F-11 | Suppression of customer-detail billing contact and address when a confidentiality restriction applies. |
| F-12 and F-13 | Dated owner links, provenance and notices, handling first-ever bootstrap and improper links, and practice manager authority. Ownership grants no permissions. |
| F-14 to F-19 | Accounting payload service categories, paired payer classes, neutral labels, payer-class coverage, bank-debit ownership, and customer-detail posting dispositions. |
| F-20 and F-21 | Claim acceptance as the billing event, related ledger transitions and the self-pay or core handoff of shared controls. |
| F-22 and F-24 | Pending corrections when a link ends and republishing the current demographic snapshot after review. |
| F-23 and F-25 | Unidentified receipts and unbilled receivable write-offs at the accounting boundary. |
| F-26 | End one edge and recompute membership. Keep restrictions, holds and registry conflict checks for unresolved splits. A separate, disinterested tier-4 reviewer qualifies each release. This can keep a small practice's action waiting until it has a qualified reviewer. |
| F-27 | Scope hard deletion to the patient role, give a clear confirmation of what remains under other roles, and keep deletion blocked when history or dependencies cannot be cleared. |
| F-28 | Include registry-relevant authority and owned work in Feature 1. Verify dependency guards with synthetic inputs and gate live activation in the owning feature. |

See the [design and owner rulings](design.md) for the complete text. The proposed ordered handoff is shape A. The choice and unqualified decision remain the owner's fields.

## Adoption and handoff

The practice draft is based on PR #8 at `f8c0532532ce19c2a4fc225656e0bb7e1b934943`, which includes PR #7 at `e746f5838b946d25efe7fc1359816fabd8037137`. The companion candidate is prepared on the exact openChart PR #2 base. Its adoption task still calls for the landed Practice #7 artifacts. Carry this paired comparison to the owning branches and rerun it if either side changes. The partner's decisions and ratification are its own.

The umbrella's Feature 1 handoff requires its own exact-revision approval and compatible artifact evidence. Live EHR use follows the implemented companion and live-activation controls. The reimbursement change still needs to reconcile the narrow erroneous-record exclusion before its separate retention handoff. The synthetic registry hard-delete test has to establish eligible state explicitly.

## Exact paired review evidence
Validated at `2026-11-23T18:41:06+00:00` UTC. The exact practice artifacts are identified by their SHA-256s below. The owner records these or the matching prepared commit revision when making the decision.
- Companion review commit `bb20497d50ad682e3fa591e0221edbf7dfab919c` (openChart PR #2 base `afcca3be7f466b5e90ceaa491df991dc72752694`).
- Contract comparison checked both IDs, the reciprocal `openpractice` and `openchart` namespaces, and the stable issuer unique to a deployment with references compared by the complete triple (EB-01 and EB-02, PL-02 and PL-03).
- Companion synthetic verification checked ten schema cases covering the updated retraction codes, pointer shape and version behavior. These results are specification evidence for owner review.

| Contract | Candidate schema SHA-256 | Companion result |
|---|---|---|
| `openpractice-ehr-patient-link/1.0` | `76f465e144893000de4bd2240adb9f547899e2e8100315f698c3cc6cb885b802` | Matches PL-04 |
| `openpractice-ehr-clinical-facts/1.0` | `3aac773e9e8a8a370e1a78c845e5837d059b60146e411d973e0c54464aad12db` | Matches PL-04 |

### Validation output

```text
openPractice OpenSpec 1.2.0
$ openspec validate define-mvp-practice-foundation --strict
Change 'define-mvp-practice-foundation' is valid
$ (cd contracts && sha256sum -c SHA256SUMS)
openpractice-ehr-patient-link-1.0.schema.json: OK
openpractice-ehr-clinical-facts-1.0.schema.json: OK
openpractice-accounting-integration-1.0.schema.json: OK

openChart OpenSpec 1.2.0
$ openspec validate define-openpractice-boundary --strict
Change 'define-openpractice-boundary' is valid
```

### Prepared practice artifact SHA-256s

The paths are relative to this OpenSpec change. This evidence file is outside its own digest list. The list describes the pre-ratification artifacts for the owner's review. The decision fields and proposal status can be completed together in the owner decision revision. Substantive text changes require fresh checks.

```text
b90a3a5e75577b9a75aacf25ee861982457fad5dd4bd8ecb5429feccae7dc5e2  .openspec.yaml
98ad76a6aefd8fb76677784ff45b3f257fd44fb49c2b491ca0a0f84ae195d091  closure-evidence.md
4852129ed47d98fce0355de0c686cecd1d3b12c918be7a866755ec814a97893b  contracts/SHA256SUMS
5ef5176056228bdd80aeeb285da4e4a4478ad3c2f476dd70b346144ce3ceb5f2  contracts/openpractice-accounting-integration-1.0.schema.json
3aac773e9e8a8a370e1a78c845e5837d059b60146e411d973e0c54464aad12db  contracts/openpractice-ehr-clinical-facts-1.0.schema.json
76f465e144893000de4bd2240adb9f547899e2e8100315f698c3cc6cb885b802  contracts/openpractice-ehr-patient-link-1.0.schema.json
d079c8524b21886162788eb8cbf95698d1320dfa2a314a7d71f8393b13f7ea5e  design.md
fae7165f1f043e516b3161f961c74c924e859cd098a3a5d93d1130363ad148f7  owner-ratification.md
158d43a766bbf42e1c021eeb9bcf39c21d8a21158a751d912c01cbc05fda1027  proposal.md
574db88ba6ce7dfa0b7a04b2a4b88cad5672613b26acecd0f22951c4490f5398  specs/accounting-integration/spec.md
28e037df8b993ea5aa2ec31f20b69a16f57904d4a0a15befeb234774b8948060  specs/billing-accounting-boundary/spec.md
935933c991c2295b8b10ed961d52f09e475e66cd165411337275f504d3a0b05e  specs/ehr-boundary/spec.md
e7c5d54096dea31c53722300e8b8f61946bac356ec80888ed86c71a8f3054644  specs/patient-registry/spec.md
c6c969221daa8eb57f21f8d8f98dd4acdae8e69028440f522ddbf58370937f5f  tasks.md
```
