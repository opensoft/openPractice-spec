Status: draft

Ratification is pending. The owner has not ratified this change; see `owner-ratification.md`.

## Why

Every safety gate in the five merged changes fails closed, and each is sound on its own. Stacked together, they leave a small practice with no single place to see what it must sign before it can go live, who signs each item, and what stays off meanwhile. The written critique of 2026-11-15 (its item C.1, "Fail-Closed Cold-Start Paralysis") counts four authorities (accountant, counsel, medical director, controller or office manager) and nearly a dozen governance artifacts. It also notes that nothing says how those roles map when one physician-owner holds several of them.

The product owner, Brett Heap, ruled on the critique on 2026-11-15. Ruling 107 authorizes this change:

> 107. (One person in several roles.) "Allowed, with each sign-off recording the role used. A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change."

This change records ruling 107 as governed requirements. **Rulings are not ratification:** the change stays draft until the owner completes `owner-ratification.md`.

Paths are relative to the root of `opensoft/openPractice-spec`.

## What Changes

This change does three things. It weakens no gate, decides no policy's content, and changes no contract. It adds no gate, with one disclosed exception: under the PROPOSED design call RD-11, the accountant's sign-offs wait on a recorded outside engagement, with the accountant's own attestation and a check of their licence against the board's public record. That is a precondition and a record field, set out as T-9 in `design.md`. Everything beyond ruling 107's own words is a design call marked PROPOSED. Some amendments proposed for other owners go further than ruling 107: RA-1, RA-3, RA-5 and RA-6 would add a condition, a notice or a record that those rules lack today. Each changes nothing unless its owner adopts it (`design.md`, T-10).

- **`readiness-checklist` (RC-01 to RC-07).** One readiness checklist per practice. Each row is a go-live gate, or a policy version a named authority approves while a fail-closed default runs. It shows:
  - what the item is;
  - the requirement that enforces it at runtime;
  - the authority that signs it, and who holds that authority now;
  - its state, including `unavailable` when the gate's record cannot be read;
  - what stays off until it is done, in the requirement's own words.

  The checklist reads and displays only. It never satisfies, replaces or relaxes a gate, and every gate keeps its own fail-closed enforcement where it is defined today. Its rows follow the requirements. A use of a one-person alternative whose subject is a patient-side record appears only as a count, with an opaque reference to the audit entries, and both are shown only to readers authorized to see that PHI. `design.md` holds the inventory the rows start from: 49 items across the five merged changes, draft PR #7 and draft PR #8. It also records the four items retired because no requirement states what waits on them.
- **`policy-starter-kit` (SK-01 to SK-06).** The product ships a starter template for each policy a gate needs, fifteen in all, for example the hold, proxy, routing-clock and messaging-continuity policies, the on-call coverage plan, the autonomy opt-in set and the revenue-recognition policy.
  - A template is inert until the authority its requirement names signs a policy made from it.
  - Signing a template is the same act, with the same record, as signing a policy written from scratch.
  - Nothing is adopted by default or in bulk.
  - Templates are versioned, and a new template version never changes a policy already in force.

  This change writes no template content and asserts no legal, accounting or clinical rule.
- **`signer-roles` (SR-01 to SR-04).** Ruling 107, encoded:
  - one person may hold several roles;
  - every sign-off records the role under which it was given;
  - every rule that needs two different people names its one-person alternative, and fails closed until it does;
  - an accountant's sign-off, and a counsel sign-off that a requirement records, can never be self-approved. Self-approval is tested on the engagement (PROPOSED): the person is engaged from outside the practice, is neither an owner nor an employee, gives no other sign-off on the same item, and is not an agent. For the accountant, this is shown by their own attestation, a licence check against the board's public record, and a name match against the other roster entries and recorded owners. Apart from that engagement record and its checks (T-9), SR-04 adds no record and no precondition.

  `design.md` lists every existing rule that needs two different people, or that loses its meaning when one person signs both sides, with a PROPOSED one-person alternative for each. Where an alternative needs another change's text amended, `design.md` records the amendment, with the exact sentence proposed, for that change's owner. This change edits no other change.
- **Design calls.** Nineteen calls that go beyond ruling 107 are listed in `design.md` under "Design calls for owner review". Each is PROPOSED and is also an open question for the owner.

## Dependencies

- **On `main`:** `add-reimbursement-retention-control`, `define-mvp-practice-foundation`, `define-customer-financial-data-boundaries`, `define-actors-agents-and-work-inbox` and `define-patient-gateway-and-escalation`, all draft.
- **Draft PR opensoft/openPractice-spec#7** (branch `change/follow-up-ruling-93-contract-gaps`, not merged): AH-06's consent, PG-16 (relying-party registration and its operator agreement) and EB-15's integration operator. This change cites them and should merge after PR #7.
- **Draft PR opensoft/openPractice-spec#8, the corrections change stacked on PR #7** (branch `change/critique-2026-11-15-corrections`, still being written). It carries:
  - rulings 102, 104 and 133 in AH-01, AH-02 and AH-06, including counsel's recorded confirmation of a provider's cache, and the declared model path in AM-04;
  - PR-15, with one party per person and the rule for a staff member acting on their own record;
  - ER-20, and the amended ER-12, MA-04, MA-07 and AM-01.

  This change cites those requirement IDs and describes them, never quoting or citing line numbers, and should merge after PR #8. Where the owner has a pending decision on an open question here (decisions 139 to 148), `design.md` points at it and encodes none.

## Non-goals

- Writing the text of any policy, template, agreement or consent form.
- Any legal, accounting or clinical rule.
- A new gate, a relaxed gate, or a change to who signs any existing gate, other than the self-approval bar and the role record that ruling 107 orders.
- A counsel sign-off where a requirement only describes counsel's part.
- Editing any other change. Amendments are proposed in `design.md` for their owners.
- A new contract, or a change to an existing one. None is needed: the checklist reads gate records that other changes already define.
- Promoting anything to canonical specs (ruling 109).
- Runtime code and other repositories.

## Capabilities

### New Capabilities
- `readiness-checklist` (RC-01 to RC-07): one checklist of readiness items, derived from the enforcing requirements, read-only over the gates' own records, with states, consequences, who holds each authority, and no PHI.
- `policy-starter-kit` (SK-01 to SK-06): the fifteen starter templates. They are inert until signed, signed exactly as a policy written from scratch, never adopted by default or in bulk, versioned, and assert no rule.
- `signer-roles` (SR-01 to SR-04): one person in several roles, the role recorded on every sign-off, one-person alternatives for two-person rules, and the bar on self-approved accountant and counsel sign-offs.

### Modified Capabilities

None. This change depends on, and does not modify, the changes named under Dependencies.

## Impact

- **New openPractice data:**
  - the checklist's view over existing gate records;
  - the starter-kit templates and their versions;
  - the template provenance on a policy version;
  - the role on each sign-off record.
- **External contracts:** none.
- **PHI exposure:** none. The checklist holds no PHI (RC-07). Uses whose subject is a patient-side record are only counted, and only readers authorized to see that PHI see the count (RC-06).
- **Amendments proposed for other owners**, all listed in `design.md`:
  - **required (five):** RA-1 to RA-4 and RA-6. RA-2 is now a record only, and RA-6 asks that ownership be recorded.
  - **fallback (one):** RA-5, needed only if the owner rejects PR #8's design call F-7.
  - **conforming (ten):** CA-1 to CA-10, of which CA-5 to CA-8 apply only if their owners come to record counsel's part.
- **Runtime:** no runtime code, dependencies or vendor claims are added.
