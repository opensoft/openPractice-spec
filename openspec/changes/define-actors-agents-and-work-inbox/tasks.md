## 1. Governance Gates

- [ ] 1.1 Run `openspec validate define-actors-agents-and-work-inbox --strict --no-interactive` on the exact revision proposed for ratification, with `define-mvp-practice-foundation` at the revision it is stacked on. Acceptance evidence: exit code 0, with the version, command, timestamp, revision and output recorded in `owner-ratification.md`.
- [ ] 1.2 Obtain the owner's ratification in `owner-ratification.md`. Acceptance evidence: every field complete and the decision exactly `APPROVED UNCONDITIONALLY`. No Speckit feature is created before this.
- [ ] 1.3 Confirm that `define-mvp-practice-foundation` is ratified at a revision whose requirement IDs match the ones this change cites. Acceptance evidence: a cross-reference check listing every cited ID.
- [ ] 1.4 Compare the canonical schema digest in `contracts/SHA256SUMS` with design.md Decision 4. Acceptance evidence: `sha256sum -c` output.

## 2. Speckit Handoff

- [ ] 2.1 Fold the actor model, recipes, offboarding and the work inbox into the registry feature (Feature 1 of `define-mvp-practice-foundation`), or into one feature immediately after it, as the owner decides at ratification. Acceptance evidence: the feature's traceability matrix covers AM-01 to AM-06 and WI-01 to WI-07.
- [ ] 2.2 Agent operations contract (AO-01 to AO-12) for registry operations. Gate: task 2.1 and the registry API (PR-09). Acceptance evidence:
  - contract tests for every operation, rung, cap, limit, the pause, the digest and idempotency;
  - agents start at `propose`.
- [ ] 2.3 Remote VA safeguards (VA-01 to VA-05). Gate: the remote VA recipe's grant set is approved (design.md D-1). Acceptance evidence: tests for own-queue scoping, export refusal, masking, device and session refusal, and expiry.
- [ ] 2.4 AI data handling (AH-01 to AH-05). Gate: before any opt-in above `propose`. Acceptance evidence:
  - a verified provider agreement and a recipient profile per provider;
  - prompt-injection test fixtures (synthetic) showing the `propose` cap;
  - a log scan showing no PHI.

## 3. Governance Constraints

- [ ] 3.1 Each feature's plan performs a Constitution Check against v1.0.0 and records a security review. Opt-ins above `propose` and VA companies each get a risk-analysis entry.
- [ ] 3.2 Route the open questions in design.md to the work they block. The recipe grant sets block 2.3 and the new recipes. The limit values and openWallet mechanics block opt-ins above `propose`.
- [ ] 3.3 Keep this checklist governance-only. Executable tasks live in each feature's Speckit `tasks.md`.

## 4. Acceptance and Closure

- [ ] 4.1 `closure-evidence.md` maps every requirement and scenario to accepted evidence.
- [ ] 4.2 Archive only after the features land, the assembly pin bump lands, and `closure-evidence.md` is complete.
