Status: draft

Ratification is pending. The owner has not ratified this change; see `owner-ratification.md`.

## Why

Every safety gate in the five merged changes fails closed, and each is sound on its own. Stacked together, they leave a small practice with no single place to see what it must sign before it can go live, who signs each item, and what stays off meanwhile. The written critique of 2026-11-15 (its item C.1, "Fail-Closed Cold-Start Paralysis") counts four authorities (accountant, counsel, medical director, controller or office manager) and nearly a dozen governance artifacts. It also notes that nothing says how those roles map when one physician-owner holds several of them.

The product owner, Brett Heap, ruled on the critique on 2026-11-15. Ruling 107 authorizes this change:

> 107. (One person in several roles.) "Allowed, with each sign-off recording the role used. A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change."

This change records ruling 107 as governed requirements. **Rulings are not ratification:** the change stays draft until the owner completes `owner-ratification.md`.

Paths are relative to the root of `opensoft/openPractice-spec`.

## What Changes

This change does three things and no more. It weakens no gate, adds no gate, and decides no policy's content.

- **`readiness-checklist` (RC-01 to RC-07).** One readiness checklist per practice. Each row is one go-live gate or policy sign-off that an existing requirement makes a precondition of a function. It shows what the item is, the requirement that enforces it at runtime, the authority that signs it and who holds that authority now, its state, and what stays off until it is done. The checklist reads and displays only. It never satisfies, replaces or relaxes a gate, and every gate keeps its own fail-closed enforcement where it is defined today. Its rows follow the requirements: when a change alters a gate, the row follows that change, never the other way round. `design.md` holds the full inventory the rows start from: 47 readiness items across the five merged changes, draft PR #7 and the corrections change stacked on it, including counsel's confirmation (ruling 102) and verified zero data retention for model providers (ruling 104).
- **`policy-starter-kit` (SK-01 to SK-06).** The product ships a starter template for each policy a gate needs, sixteen in all (for example the hold, proxy, routing-clock and messaging-continuity policies, the on-call coverage plan, the autonomy opt-ins and the revenue-recognition policy). A template is inert until the authority its requirement names signs a policy made from it. Signing a template is the same act, with the same record, as signing a policy written from scratch. Nothing is adopted by default or in bulk. Templates are versioned, and a new template version never changes a policy already in force. This change writes no template content and asserts no legal, accounting or clinical rule.
- **`signer-roles` (SR-01 to SR-04).** Ruling 107, encoded:
  - one person may hold several roles;
  - every sign-off records the role under which it was given;
  - every rule that needs two different people names its one-person alternative, and fails closed until it does;
  - an accountant's or counsel's sign-off can never be self-approved.

  `design.md` lists every existing rule that needs two different people, or that loses its meaning when one person signs both sides, with a PROPOSED one-person alternative for each. Where an alternative needs another change's text amended, `design.md` records the amendment, with the exact sentence proposed, for that change's owner. This change edits no other change.
- **Design calls.** Sixteen calls that go beyond ruling 107 are listed in `design.md` under "Design calls for owner review". Each is PROPOSED and is also an open question for the owner.

## Dependencies

- **On `main`:** `add-reimbursement-retention-control`, `define-mvp-practice-foundation`, `define-customer-financial-data-boundaries`, `define-actors-agents-and-work-inbox` and `define-patient-gateway-and-escalation`, all draft.
- **Draft PR opensoft/openPractice-spec#7** (branch `change/follow-up-ruling-93-contract-gaps`, not merged): AH-06 (consent for agent answers), PG-16 (relying-party registration and its operator agreement) and EB-15's integration operator. This change cites them and should merge after PR #7.
- **Draft PR opensoft/openPractice-spec#8, the corrections change stacked on PR #7** (branch `change/critique-2026-11-15-corrections`, read at `957c745`, still being written): it encodes rulings 102 and 104 in AH-01, AH-02 and AH-06, adds PR-15 (one party per person, and the rule for a staff member acting on their own record) and ER-20, and amends AM-01 and MA-07. This change cites those requirement IDs, never line numbers, and should merge after the corrections change.

## Non-goals

- Writing the text of any policy, template, agreement or consent form.
- Any legal, accounting or clinical rule.
- A new gate, a relaxed gate, or a change to who signs any existing gate, other than the self-approval bar and the role record that ruling 107 orders.
- Editing any other change. Amendments are proposed in `design.md` for their owners.
- A new contract. None is needed: the checklist reads gate records that other changes already define.
- Promoting anything to canonical specs (ruling 109).
- Runtime code and other repositories.

## Capabilities

### New Capabilities
- `readiness-checklist` (RC-01 to RC-07): one checklist of readiness items, derived from the enforcing requirements, read-only over the gates' own records, with states, consequences, and who holds each authority.
- `policy-starter-kit` (SK-01 to SK-06): the sixteen starter templates, inert until signed, signed exactly as a policy written from scratch, never adopted by default or in bulk, versioned, and asserting no rule.
- `signer-roles` (SR-01 to SR-04): one person in several roles, the role recorded on every sign-off, one-person alternatives for two-person rules, and the bar on self-approved accountant and counsel sign-offs.

### Modified Capabilities

None. This change depends on, and does not modify, the changes named under Dependencies.

## Impact

- **New openPractice data:** the checklist's view over existing gate records, the starter-kit templates and their versions, the template provenance on a policy version, and the role on each sign-off record.
- **External contracts:** none.
- **PHI exposure:** none. The checklist holds no PHI (RC-07).
- **Amendments proposed for other owners:** four required amendments (RA-1 to RA-4), one fallback (RA-5, needed only if the owner rejects the corrections change's design call F-7), and nine conforming amendments (CA-1 to CA-9), listed in `design.md`.
- **Runtime:** no runtime code, dependencies or vendor claims are added.
