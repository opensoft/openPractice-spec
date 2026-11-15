# Closure Evidence Record

Status: **PENDING — ARCHIVAL IS NOT AUTHORIZED**

This record collects acceptance evidence for OpenSpec change `define-actors-agents-and-work-inbox`. It does not replace the Speckit features' implementation tasks or validation artifacts.

## Governance Evidence

- Owner ratification reference: `owner-ratification.md` — _pending completion_
- Approved grant sets for the remote VA, nurse, medical director, controller or office manager, and agent recipes: _pending_
- Per-feature Constitution Check and security review records: _pending_
- Verified VA-company BAAs, and verified BAAs with each agent operator and model provider, with risk-analysis entries: _pending_
- Each model provider's verified zero-data-retention arrangement for its exact endpoints and models, matched to each agent's declared model path (ruling 104, D-32), and counsel's recorded confirmation of ruling 97's answer (ruling 102), before any PHI reached a model provider: _pending_
- Approved read-only answer types and template versions, with medical-director sign-off where health-adjacent: _pending_

## Product Acceptance Evidence

- Requirement and scenario traceability for `actor-model`, `agent-operations`, `work-inbox`, `remote-va-safeguards` and `ai-data-handling`: _pending_
- Actor, actor type and principal recorded on every action, with no PHI in logs: _pending_
- Same-day offboarding of a person, a VA, a VA company and an agent, including cancellation of delayed actions: _pending_
- Contract tests for `openpractice-agent-operations/1.0` at its recorded digest. They cover every operation, rung and cap, dry run, idempotency, limits, the pause and the digest: _pending_
- Agent caps proven: no tier-3 action at `act_with_notice`, and no tier-4, D8 or untrusted-content action above `propose`: _pending_
- Work inbox: nothing unowned; approve, change and reject with signer identity; absence cover; existing gates carried with their own escapes: _pending_
- Remote VA safeguards: own queue only, no export, masking, device and session refusal, expiry, and per-VA reports: _pending_
- Prompt-injection fixtures (synthetic) showing the `propose` cap and the server-side untrusted determination, including a false flag: _pending_
- AH-01 gate before any grant at any rung, and suspension of all agent PHI access on a lapse: _pending_
- Named-human-only grants, rungs, limits and pause lifting; no agent deciding its own proposal; an agent holding only the agent recipe: _pending_
- Per-patient read scope and the `patient_scope` refusal: _pending_
- Read-only answers: every condition of AH-05 exception 1, including server assembly, `act_after_delay` only, attribution, the sampled audit and the return to `propose`: _pending_
- Held delayed actions restart their windows after the pause or a suspension lifts: _pending_
- Work-item operations (AO-13): reading only held administrative text, `text_not_held` for every item kind without text, re-route and urgency proposals applied by rule and never lowering urgency, release to a human, cancellation of the agent's own pending answer, the `propose` cap, `not_item_owner`, and the `rate_limited` release at a limit, with the refused proposal shown and an urgent-priority alert for a refused `urgent` raise: _pending_
- Consent check (AH-06): refusal without a recorded grant, the check before every relay, withdrawal winning a concurrent relay, the hold after an agent hears a withdrawal, a representative's withdrawal, cancellation of every pending answer on withdrawal, grants recorded only by a named human: _pending_

## Final Validation and Archive Evidence

- Final strict OpenSpec validation command, version, timestamp, revision, exit code and output: _pending_
- Merged leg PR identifiers, exact heads, and the assembly root pin-bump PR: _pending_
- Archive date and path: _pending_

Until every applicable field is complete and final strict validation exits 0, this change must remain active.
