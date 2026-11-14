## 1. Owner Ratification Gate

- [ ] 1.1 Run `openspec validate define-mvp-practice-foundation --strict` against the exact artifacts proposed for ratification. Acceptance evidence: `owner-ratification.md` records the command, OpenSpec version, timestamp, exact artifact revision or checksums, exit code 0, and the output.
- [ ] 1.2 Obtain the owner's unconditional ratification before any Speckit feature is created. On 2026-11-14 the owner ruled for handoff shape A: the ordered multi-feature handoff in section 2, as a stated exception to the workflow protocol's one-feature handoff. The ratification record must still confirm that choice. Acceptance evidence: the owner, not an agent, completes `owner-ratification.md`. That means identity, timestamp, exact revision, strict-validation evidence, the confirmed handoff shape, and the unqualified decision `APPROVED UNCONDITIONALLY`. In the same revision, the owner changes the proposal's `Status` to `ratified`.
- [ ] 1.3 Compare this change with `opensoft/openChart-spec` change `define-openpractice-boundary` on four things: the canonical schema digests (`sha256sum -c contracts/SHA256SUMS`, matched against the digests openChart cites), the contract identifiers, the namespaces, and the issuer requirements. Acceptance evidence: both revisions compared, each digest pair, and the result, all recorded in `owner-ratification.md`. Any mismatch blocks feature creation.

## 2. Speckit Features That May Follow, In Order (Handoff Shape A)

Each feature is gated on task 1.2 and is created from the assembly root `opensoft/openPractice` on its base branch.

- [ ] 2.1 **Feature 1: patient registry (Slice 0). Created first.** Owns PR-01 to PR-14. Gate: task 1.2. Acceptance evidence:
  - every PR requirement and scenario is traced to planned work and tests;
  - the PHI safeguard plan, security review and risk-analysis update are recorded;
  - the tamper-evident audit and its redaction mechanism (T-14) are chosen and tested;
  - fixtures are synthetic only.
- [ ] 2.2 **Feature 2: EHR patient link and demographic publication, openPractice side (Slice 0).** Owns EB-01 to EB-12, EB-19 and EB-20. Gate: task 1.2, and Feature 1 merged. Acceptance evidence:
  - contract tests validate every message against the patient-link canonical schema at its recorded digest;
  - the suspension, correction-request and single-resolver paths are covered;
  - all tests run against an EHR contract double.

  Live interoperation with openChart waits until openChart's change has been ratified and implemented.
- [ ] 2.3 **Feature 3: billing–accounting boundary, banks, accounting contract and ERPNext adapter (Slice 4).** Owns BAB-01 to BAB-15 and AI-01 to AI-22. Gate: task 1.2. Two prerequisites must be complete first, each under its own governance: the FD-7 Thread contract must be versioned, and the money ledger and reason taxonomy must be defined. Acceptance evidence:
  - contract tests for every operation, AI-13 event, key and `supersedes` case, mode lock, date rule and accrual true-up;
  - the ERPNext adapter passes over REST against the **remote** topology, using a contract double or a disposable synthetic instance (the remote topology alone satisfies acceptance);
  - openPractice tests green with no ERPNext present;
  - AI-20 verification is recorded;
  - bank-number protection is tested;
  - no destination enters customer-detail mode except before its first release, after a verified BAA or attestation, and with a risk entry;
  - customer names and billing contacts reach a destination only in customer-detail mode with its gate satisfied; summary-journal batches carry no customer record; every customer-detail batch records its recipient-profile version, and no profile adds an excluded field (AI-10, AI-15, AI-16).
- [ ] 2.4 **Feature 4: clinical-fact consumption for charge capture (Slice 3).** Owns EB-13 to EB-18. It consumes the Thread, because waiting on clinical documentation and the deadline escape are thread conditions. Gate: task 1.2, Feature 2 merged, and the FD-7 Thread contract versioned. The owner may fold this feature into the Slice 3 charge-capture feature, provided ownership is recorded. Acceptance evidence:
  - contract tests run against the clinical-facts canonical schema at its recorded digest;
  - manual entry is marked manual while the capability result is false;
  - the deadline escape is tested.
- [ ] 2.5 **Post-MVP: QuickBooks Online adapter, then Business Central adapter.** Each is a separate feature against `openpractice-accounting-integration/1.x` (same MAJOR). Each starts only after the vendor's authentication, API, rate limits, idempotency, data residency, line service dates, cash-basis reporting and BAA availability have been verified and recorded. Gate: task 1.2 and Feature 3 merged. These features are excluded from this change's closure.

## 3. Planning Gates For Every Feature

- [ ] 3.1 Perform a meaningful Constitution Check against constitution v1.0.0 before each feature's plan phase. Acceptance evidence: the plan maps Principles I to VI and the quality gates to concrete obligations, and records the open questions routed to it in 3.5.
- [ ] 3.2 Complete a documented security review and update the living risk analysis for each feature. Acceptance evidence: the review record and the risk-analysis diff are referenced from `closure-evidence.md`.
- [ ] 3.3 Confine runtime changes to the openPractice code leg. Acceptance evidence: the plan's inventory shows no runtime edits in openChart, ERPNext or any vendor system.
- [ ] 3.4 Keep this checklist governance-only. Acceptance evidence: executable tasks exist only in each feature's Speckit `tasks.md`, and `speckit-analyze` reports no unresolved critical inconsistency.
- [ ] 3.5 Route every remaining open question (design.md "Open Questions") to the work it blocks. Acceptance evidence: each feature's plan records how its question was answered, or why the work it blocks is out of that feature's scope:
  - Open Question 1, appointment authority (R-7): no feature in section 2 depends on it, because the 1.0 contracts exclude appointments. It blocks any future appointment-exchange change.
  - Open Question 2, counsel on in-process ERPNext calls: blocks only a future in-process adapter change. Feature 3 records that it uses REST only.
  - Open Question 3, Frappe version policy: blocks Feature 3's same-site deployment support. It does not block remote acceptance.
  - Open Question 4, the legacy-collections account: blocks Feature 3's first release for any practice with legacy run-off.
  - Open Question 5, the mode continuity procedure: blocks any post-release mode change (AI-09). Feature 3 records the refusal path.
  - Open Question 6, R-2: blocks any future automatic batch release. Feature 3 records explicit release.
  - Open Question 7, two receivable routes in one destination (rulings 24 and 26): owned by change `define-customer-financial-data-boundaries`, on branch `define-customer-financial-data-boundaries` and not yet on main. It blocks Feature 3's go-live only in a destination that also carries membership or retail customers. Feature 3 tests AI-22 in every case.

## 4. Acceptance and Closure

- [ ] 4.1 Complete implementation and verification only through the features in section 2. Acceptance evidence: `closure-evidence.md` maps every requirement and scenario to accepted evidence.
- [ ] 4.2 Re-verify cross-repository consistency at landing. Acceptance evidence: `closure-evidence.md` records that the schema digests, contract identifiers, namespaces and issuer handling in the landed code match openChart's landed implementation, or records openChart's work as pending.
- [ ] 4.3 Land each feature's leg pull requests and the assembly root pin bump. Acceptance evidence: pull-request identifiers, exact heads, check results and review dispositions are recorded.
- [ ] 4.4 Run final strict validation on the final revision. Acceptance evidence: the command, version, timestamp, revision and output are recorded, with exit code 0.
- [ ] 4.5 Archive only after 4.1 to 4.4 are complete for Features 1 to 4, and move the canonical schemas to the spec repository's `contracts/` directory with their digests unchanged. Acceptance evidence: the archive date and path, the schema move with matching digests, and the delta-spec sync disposition are recorded.
