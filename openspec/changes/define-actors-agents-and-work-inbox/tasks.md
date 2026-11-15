## 1. Governance Gates

- [ ] 1.1 Run `openspec validate define-actors-agents-and-work-inbox --strict --no-interactive` on the exact revision proposed for ratification, with `define-mvp-practice-foundation` at the same revision. Acceptance evidence: exit code 0, with the version, command, timestamp, revision and output recorded in `owner-ratification.md`.
- [ ] 1.2 Obtain the owner's ratification in `owner-ratification.md`. Acceptance evidence: every field complete and the decision exactly `APPROVED UNCONDITIONALLY`. No Speckit feature is created before this.
- [ ] 1.3 Confirm that `define-mvp-practice-foundation` is ratified at a revision whose requirement IDs match the ones this change cites. Acceptance evidence: a cross-reference check listing every cited ID.
- [ ] 1.4 Compare the canonical schema digest in `contracts/SHA256SUMS` with design.md Decision 4. Acceptance evidence: `sha256sum -c` output.

## 2. Speckit Handoff

- [ ] 2.1 Fold the actor model, recipes, offboarding and the work inbox into the registry feature (Feature 1 of `define-mvp-practice-foundation`), or into one feature immediately after it, as the owner decides at ratification. Acceptance evidence: the feature's traceability matrix covers AM-01 to AM-06 and WI-01 to WI-07.
- [ ] 2.2 AI data handling (AH-01 to AH-05). It comes before the agent operations contract in 2.3. Gate: before any agent grant at any rung, `observe` included. Acceptance evidence:
  - verified BAAs with the agent operator and with each model provider, each model provider's verified zero-data-retention arrangement for its exact endpoints and models, with any in-memory cache's limit recorded and counsel's confirmation of ruling 133 where one is allowed, each agent's declared model path (D-32), counsel's recorded confirmation of ruling 97's answer (ruling 102), and a recipient profile for each;
  - the suspension of all agent PHI access when a control lapses, and its lifting only by a named human;
  - the server-side untrusted determination, including a false-flag fixture;
  - prompt-injection test fixtures (synthetic) showing the `propose` cap, and every condition of AH-05 exception 1;
  - the AH-06 consent check before scheduling and before every relay, refusal with `consent_not_recorded`, a withdrawal that wins over a concurrent relay, the hold after `ai_messaging_withdrawal_requested`, a representative's withdrawal, the cancellation of every pending answer on withdrawal, and that provider processing for practice operations does not wait on per-patient consent (ruling 97);
  - a log scan showing no PHI.
- [ ] 2.3 Agent operations contract (AO-01 to AO-13) for registry operations, the read-only answer types and the work-item operations. Gate: tasks 2.1 and 2.2, and the registry API (PR-09). Acceptance evidence:
  - contract tests for every operation, rung, cap, limit, the pause, the digest and idempotency;
  - refusal of any grant, rung, limit or pause change not made by a named human, and of an agent deciding its own proposal;
  - the per-patient read scope, including the `patient_scope` refusal;
  - read-only answers: no payload, server assembly, eligibility, `act_after_delay` only, attribution, the automatic return to `propose` on a defect;
  - agents start at `propose`;
  - the work-item operations of AO-13: each outcome, the `propose` cap, `not_item_owner`, `text_not_held` for a clinical item, removed text and a document, `answer_not_pending`, and the `rate_limited` refusal that releases the item to a human, showing the refused proposal and alerting at urgent priority when it was `urgent`.
- [ ] 2.4 Remote VA safeguards (VA-01 to VA-05). Gate: the remote VA recipe's grant set is approved (design.md D-1). Acceptance evidence: tests for own-queue scoping, export refusal, masking, device and session refusal, and expiry.

## 3. Governance Constraints

- [ ] 3.1 Each feature's plan performs a Constitution Check against v1.0.0 and records a security review. Agent grants, opt-ins above `propose`, read-only answer types and VA companies each get a risk-analysis entry.
- [ ] 3.2 Route the open questions in design.md to the work they block. The recipe grant sets block 2.4 and the new recipes. The limit values and openWallet mechanics block opt-ins above `propose`. Each practice's answer types, and the consent record of Open Question 9, block switching them on. Rulings 99 and 100 closed Open Questions 7 and 6.
- [ ] 3.3 Keep this checklist governance-only. Executable tasks live in each feature's Speckit `tasks.md`.

## 4. Acceptance and Closure

- [ ] 4.1 `closure-evidence.md` maps every requirement and scenario to accepted evidence.
- [ ] 4.2 Archive only after the features land, the assembly pin bump lands, and `closure-evidence.md` is complete.
