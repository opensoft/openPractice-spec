## 1. Owner Ratification Gate

- [ ] 1.1 Run `openspec validate define-mvp-practice-foundation --strict` against the exact artifacts proposed for ratification. Acceptance evidence: `owner-ratification.md` records the command, OpenSpec version, timestamp, exact artifact revision or checksums, exit code 0, and the output.
- [ ] 1.2 Obtain the owner's unconditional ratification before any Speckit feature is created. On 2026-11-14 the owner ruled for handoff shape A: the ordered multi-feature handoff in section 2, as a stated exception to the workflow protocol's one-feature handoff. The ratification record must still confirm that choice. Acceptance evidence: the owner, not an agent, completes `owner-ratification.md`. That means identity, timestamp, exact revision, strict-validation evidence, the confirmed handoff shape, and the unqualified decision `APPROVED UNCONDITIONALLY`. In the same revision, the owner changes the proposal's `Status` to `ratified`.
- [ ] 1.3 Compare this change with `opensoft/openChart-spec` change `define-openpractice-boundary` on four things: the canonical schema digests (`sha256sum -c contracts/SHA256SUMS`, matched against the digests openChart cites), the contract identifiers, the namespaces, and the issuer requirements. Acceptance evidence: both revisions compared, each digest pair, and the result, all recorded in `owner-ratification.md`. Any mismatch blocks feature creation.

## 2. Speckit Features That May Follow, In Order (Handoff Shape A)

Each feature is gated on task 1.2 and is created from the assembly root `opensoft/openPractice` on its base branch.

- [ ] 2.1 **Feature 1: patient registry (Slice 0). Created first.** Owns PR-01 to PR-15. Gate: task 1.2. Acceptance evidence:
  - every PR requirement and scenario is traced to planned work and tests;
  - the PHI safeguard plan, security review and risk-analysis update are recorded;
  - the tamper-evident audit and its redaction mechanism (T-14) are chosen and tested;
  - fixtures are synthetic only;
  - one party per person (PR-15): roles and links instead of second person records; search before create showing parties that hold other roles, including those with no date of birth, and verified matching evidence before an existing party gains the patient role; confidential-communication instructions honored for every role; review work for every other role after a death; a hard delete that keeps shared facts and says so; no automatic merge, and a merge that keeps one party; and money, coverage and sign-off actions on a staff member's own record decided by another human roster entry, or an agent the staff member does not sponsor, or under the recorded one-person alternative with its notice to the accountant, which waits while no one but the actor holds the accountant capacity; and, in place of a merge, a reversible same-person link that moves nothing, a duplicate deactivation only under its preconditions with the EHR-linked record staying, nothing retired while the EHR has its own duplicate, and one encounter never charged on both records.
- [ ] 2.2 **Feature 2: EHR patient link and demographic publication, openPractice side (Slice 0).** Owns EB-01 to EB-12, EB-19 and EB-20. Gate: task 1.2, and Feature 1 merged. Acceptance evidence:
  - contract tests validate every message against the patient-link canonical schema at its recorded digest;
  - the suspension, correction-request and single-resolver paths are covered;
  - all tests run against an EHR contract double.

  Live interoperation with openChart waits until openChart's change has been ratified and implemented.
- [ ] 2.3 **Feature 3: billing–accounting boundary, banks, accounting contract and ERPNext adapter (Slice 4).** Owns BAB-01 to BAB-18 and AI-01 to AI-23. Gate: task 1.2. Two prerequisites must be complete first, each under its own governance: the FD-7 Thread contract must be versioned, and the money ledger and reason taxonomy must be defined. Acceptance evidence:
  - contract tests for every operation, AI-13 event, key and `supersedes` case, mode lock, date rule, constrained-estimate revenue with evidence true-ups and close re-estimates, and summary-journal control-account reconciliation by payer class;
  - the ERPNext adapter passes over REST against the **remote** topology, using a contract double or a disposable synthetic instance (the remote topology alone satisfies acceptance);
  - openPractice tests green with no ERPNext present;
  - AI-20 verification is recorded;
  - **go-live gate:** the practice's accountant has approved, and the feature has recorded, the reporting basis, estimator, receivable classification, materiality approach and reopening policy (ruling 30, framework D8). No destination goes live without it;
  - bank-number protection is tested;
  - no destination enters customer-detail mode except before its first release, after a verified BAA or attestation, and with a risk entry;
  - customer names and billing contacts reach a destination only in customer-detail mode with its gate satisfied; summary-journal batches carry no customer record; every customer-detail batch records its recipient-profile version, and no profile adds an excluded field (AI-10, AI-15, AI-16, AI-23);
  - openPractice is the single poster of each deposit it explains, other-route shares post as `other_route_share`, refund disbursements are matched, and charge finalization checks the offering route, with tier-3 release at the filing threshold (BAB-14, BAB-16, AI-17);
  - summary-journal minimum group size and roll-up (AI-15), and automatic close re-estimates (AI-12);
  - sign-offs per BAB-17: agent sign-off refused when not opted in and at tier 4, and recorded with its openWallet ID when allowed;
  - money per BAB-18: every amount a decimal string with two places and a currency code, never a JSON number, USD only in 1.0, one currency per batch and per bank-lines result, BAB-18's one rounding rule, with fixed amounts split by cumulative rounding, in ascending order of identifier where no order is stated and in exact arithmetic, so that the parts equal the whole, each is within a cent of its exact share, and none is negative when every share is non-negative, and whole cents in storage.
- [ ] 2.4 **Feature 4: clinical-fact consumption for charge capture (Slice 3).** Owns EB-13 to EB-18. It consumes the Thread, because waiting on clinical documentation and the deadline escape are thread conditions. Gate: task 1.2, Feature 2 merged, and the FD-7 Thread contract versioned. The owner may fold this feature into the Slice 3 charge-capture feature, provided ownership is recorded. Acceptance evidence:
  - contract tests run against the clinical-facts canonical schema at its recorded digest;
  - manual entry is marked manual while the capability result is false;
  - the deadline escape is tested, including manual entry only after the EB-17 staff attestation and refusal without it;
  - retractions (EB-15): both reason codes with the same billing effect, an absent code read as `withdrawn`, an undefined code applied and recorded as `withdrawn`, its value in no record, log, diagnostic or stored copy, and reported only as a per-period count to the integration operator, no cause inferred or shown, references to another link's or subject's facts changing nothing, and retractions accepted after a link ends `revoked` or `superseded` (ruling 88).
- [ ] 2.5 **Post-MVP: QuickBooks Online adapter, then Business Central adapter.** Each is a separate feature against `openpractice-accounting-integration/1.x` (same MAJOR). Each starts only after the vendor's authentication, API, rate limits, idempotency, data residency, line service dates, cash-basis reporting and BAA availability have been verified and recorded. Gate: task 1.2 and Feature 3 merged. These features are excluded from this change's closure.

## 3. Planning Gates For Every Feature

- [ ] 3.1 Perform a meaningful Constitution Check against constitution v1.0.0 before each feature's plan phase. Acceptance evidence: the plan maps Principles I to VI and the quality gates to concrete obligations, and records the open questions routed to it in 3.5.
- [ ] 3.2 Complete a documented security review and update the living risk analysis for each feature. Acceptance evidence: the review record and the risk-analysis diff are referenced from `closure-evidence.md`.
- [ ] 3.3 Confine runtime changes to the openPractice code leg. Acceptance evidence: the plan's inventory shows no runtime edits in openChart, ERPNext or any vendor system.
- [ ] 3.4 Keep this checklist governance-only. Acceptance evidence: executable tasks exist only in each feature's Speckit `tasks.md`, and `speckit-analyze` reports no unresolved critical inconsistency.
- [ ] 3.5 Route every remaining open question (design.md "Open Questions") to the work it blocks. Acceptance evidence: each feature's plan records how its question was answered, or why the work it blocks is out of that feature's scope:
  - Open Question 1, appointment authority (R-7): closed by ruling 31. openPractice owns the administrative appointment. Appointment events wait for a later MINOR version of the contracts.
  - Open Question 2, counsel on in-process ERPNext calls: blocks only a future in-process adapter change. Feature 3 records that it uses REST only.
  - Open Question 3, Frappe version policy: blocks Feature 3's same-site deployment support. It does not block remote acceptance.
  - Open Question 4, the legacy-collections account: blocks Feature 3's first release for any practice with legacy run-off.
  - Open Question 5, the mode continuity procedure: blocks any post-release mode change (AI-09). Feature 3 records the refusal path.
  - Open Question 6, R-2: blocks any future automatic batch release. Feature 3 records explicit release.
  - Open Question 7, two receivable routes in one destination (rulings 24 and 26): owned by change `define-customer-financial-data-boundaries`, merged on `main` as a draft. It blocks Feature 3's go-live in a destination that also carries membership or retail customers, or whose explained deposits fund their balances. Ratifying that change does not by itself lift the block: it lifts only when that change is ratified and Feature 3 has accepted evidence for the single deposit poster with `other_route_share` (BAB-14) and the offering-route check before charge finalization (BAB-16). Feature 3 tests AI-22 in every case.
  - Open Question 8, telling withdrawals apart for claims already sent: blocks nothing; Feature 4 lets staff choose the claim action (F-4).
  - Open Question 9, the PROPOSED follow-up calls F-1 to F-4: block Feature 4's retraction handling until the owner dispositions them at ratification.
  - Open Question 10, a full merge of two parties: blocks nothing; the same-person link of PR-15 moves nothing, and linked parties stay two records.

## 4. Acceptance and Closure

- [ ] 4.1 Complete implementation and verification only through the features in section 2. Acceptance evidence: `closure-evidence.md` maps every requirement and scenario to accepted evidence.
- [ ] 4.2 Re-verify cross-repository consistency at landing. Acceptance evidence: `closure-evidence.md` records that the schema digests, contract identifiers, namespaces and issuer handling in the landed code match openChart's landed implementation, or records openChart's work as pending.
- [ ] 4.3 Land each feature's leg pull requests and the assembly root pin bump. Acceptance evidence: pull-request identifiers, exact heads, check results and review dispositions are recorded.
- [ ] 4.4 Run final strict validation on the final revision. Acceptance evidence: the command, version, timestamp, revision and output are recorded, with exit code 0.
- [ ] 4.5 Archive only after 4.1 to 4.4 are complete for Features 1 to 4, and move the canonical schemas to the spec repository's `contracts/` directory with their digests unchanged. Acceptance evidence: the archive date and path, the schema move with matching digests, and the delta-spec sync disposition are recorded.
