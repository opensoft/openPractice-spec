## 1. Owner Ratification Gate

- [ ] 1.1 Run `openspec validate define-practice-readiness --strict --no-interactive` against the exact artifacts proposed for ratification. Acceptance evidence: `owner-ratification.md` records the command, OpenSpec version, timestamp, exact artifact revision or checksums, exit code 0, and the output.
- [ ] 1.2 Confirm the dependencies first. Acceptance evidence: `owner-ratification.md` records the merged revisions of draft PR opensoft/openPractice-spec#7 and of the corrections change stacked on it (draft PR #8), and a cross-reference check listing every requirement ID this change cites, each found at those revisions. If either changed a cited requirement before merging, the inventory rows, the starter kit and the amendments that cite it are updated and revalidated first. A missing dependency blocks ratification.
- [ ] 1.3 Obtain the owner's unconditional ratification before any Speckit feature is created. Acceptance evidence: the owner, not an agent, completes `owner-ratification.md`. That means identity, timestamp, exact revision, strict-validation evidence, the confirmed handoff shape (design call RD-15), his disposition of design calls RD-1 to RD-20, and the unqualified decision `APPROVED UNCONDITIONALLY`. In the same revision, the owner changes the proposal's `Status` to `ratified`.
- [ ] 1.4 Record the disposition of each amendment this change asks of another change. Acceptance evidence: `owner-ratification.md` confirms that RA-1 to RA-4 and RA-6 are in the merged revision of PR #8 as adopted, records the owner's disposition of PR #8's design call F-7 as it carries RA-5a to RA-5c, and confirms that RA-5d is covered by F-7. It confirms that CA-1 to CA-4, CA-9 and CA-10, applied in PR #8, checked at `a1a5e02`, are in its merged revision; for each of CA-5 to CA-8, only if their owners come to record counsel's part, it records accepted, changed or rejected, and, where accepted, the change and revision that carries it. A conforming amendment that is not accepted changes nothing, because SR-02 and SR-04 already bind.

## 2. Single Speckit Handoff

- [ ] 2.1 Create exactly one Speckit feature from the assembly root `opensoft/openPractice` on its base branch, covering RC-01 to RC-07, SK-01 to SK-06 and SR-01 to SR-04. Gate: tasks 1.2 and 1.3, and change A's work-inbox feature merged, because it builds the first shared sign-off record (WI-03). Acceptance evidence:
  - SR-02 and SR-04 are built into the shared sign-off record before any sign-off is recorded in production: the role on every sign-off, refusal of a role the signer does not hold or the requirement does not allow, the practice manager as "an authorized person" where a requirement names no other role (ruling 142), two sign-offs for two roles, and every SR-04 refusal (no outside engagement, an employed accountant, an accountant's entry not created with its arrangement or holding another recipe, no own attestation, or one that does not confirm the owners recorded or that the ending or changing of an owner link has reopened, no CPA licence or one that does not check active against the state board's public record, a counsel admission that does not check active against the state bar's public record, a name that matches a party who counts as an owner or an employee (now, or within the last 24 months), no owner link recorded, the other side of the same item, an agent), with no SR-04 check on any record outside the sign-offs SR-04 names;
  - SR-03's fail-closed default for a two-person rule with no one-person alternative, or whose named alternative is unavailable, and the record of each use and of each sign-off in which one person signed both sides;
  - the checklist reads every row from the gate's own record; tests prove that no action on the checklist records, approves or verifies a gate's item, and that completing every row enables nothing (RC-03);
  - every row is traced to an enforcing requirement, and no row exists without one (RC-02); a row whose requirement names no authority says so;
  - the six row states, `unavailable` whenever the gate's record cannot be read, `not_required` only from the requirement's own condition, and refusal of a manual not-required mark (RC-04);
  - the authority holders, the one-person marks and the collapse warnings of RC-06, with every use whose subject is a patient-side record shown only as a count with an opaque reference, both visible only to readers authorized to see that PHI;
  - templates never read by a gate, inert after installation and upgrade, approved only through the gate's own action with the same record as a version written from scratch, with template provenance that changes no check (SK-02, SK-03);
  - refusal of any adoption or approval of more than one item at once, and one opt-in per agent and action type (SK-04);
  - immutable template versions, a newer template that changes no policy in force, and a withdrawn template that withdraws no policy (SK-05);
  - no PHI in the checklist, its logs or its diagnostics (RC-07);
  - the PHI safeguard plan, security review and risk-analysis update are recorded.

  Each checklist row is added with the feature that builds its gate; until then the row shows that its gate is not yet built. Template content ships only after the reviews that ruling 147 requires are recorded (SK-06, RD-20).

## 3. Planning Gates

- [ ] 3.1 Perform a meaningful Constitution Check against constitution v1.0.0 before the feature's plan phase. Acceptance evidence: the plan maps Principles I to VI and the quality gates to concrete obligations, records how T-2 and T-5 were dispositioned, and records the open questions routed to it in 3.4.
- [ ] 3.2 Complete a documented security review and update the living risk analysis. Acceptance evidence: the review record and the risk-analysis diff are referenced from `closure-evidence.md`.
- [ ] 3.3 Confine runtime changes to the openPractice code leg, and keep this checklist governance-only. Acceptance evidence: the plan's inventory shows no runtime edits in any other repository; executable tasks exist only in the feature's Speckit `tasks.md`; `speckit-analyze` reports no unresolved critical inconsistency.
- [ ] 3.4 Route every open question (design.md "Open Questions") to the work it blocks. Acceptance evidence: the feature's plan records how each was answered, or why the work it blocks is out of the feature's scope:
  - Open Question 1, the inclusion rule: blocks the checklist's row set.
  - Open Question 2, SR-04's details beyond the rulings: blocks those refusal rules; the ruled ones can be built first.
  - Open Question 3: closed by ruling 141.
  - Open Question 4: closed by ruling 147; shipping any template waits on its recorded reviews.
  - Open Question 5: closed by ruling 146.
  - Open Question 6, the conforming amendments: blocks nothing here.
  - Open Question 7: closed by ruling 143.
  - Open Question 8, gates with no named authority: blocks nothing; those rows say so.
  - Open Question 9: closed by ruling 193.
  - Open Question 10, rulings 102, 104, 133 and 138: blocks nothing here; RI-46, RI-47, RI-49 and RI-53 follow the corrections change.
  - Open Question 11, the handoff shape: blocks task 2.1.
  - Open Question 12, who may read the checklist: blocks the checklist's authorization.
  - Open Question 13, security reviews and risk entries that nothing waits on: blocks nothing; no row until a requirement makes something wait on them.
  - Open Question 14: closed by ruling 139.
  - Open Question 15: closed by ruling 145.
  - Open Question 16: closed by ruling 144.
  - Open Question 17, the remaining design calls: blocks the parts of the feature each governs.
  - Open Question 18: closed by ruling 142.
  - Open Question 19: closed by ruling 149.
  - Open Question 20: closed by ruling 197.

## 4. Acceptance and Closure

- [ ] 4.1 Complete implementation and verification only through the feature in section 2. Acceptance evidence: `closure-evidence.md` maps every requirement and scenario to accepted evidence.
- [ ] 4.2 Re-verify the rows against the requirements at landing. Acceptance evidence: `closure-evidence.md` records, for the landed revision, every checklist row with its enforcing requirement, and confirms that no row lacks one and no gate in a landed feature lacks its row.
- [ ] 4.3 Land the feature's leg pull requests and the assembly root pin bump. Acceptance evidence: pull-request identifiers, exact heads, check results and review dispositions are recorded.
- [ ] 4.4 Run final strict validation on the final revision. Acceptance evidence: the command, version, timestamp, revision and output are recorded, with exit code 0.
- [ ] 4.5 Archive only after 4.1 to 4.4 are complete, with no promotion to canonical specs before then (ruling 109). Acceptance evidence: the archive date and path, and the delta-spec sync disposition, are recorded.
