Status: draft

Ratification is pending. The owner has not ratified this change; see `owner-ratification.md`.

## Why

Every safety gate in the five merged changes fails closed, and each is sound on its own. Stacked together, they leave a small practice with no single place to see what it must sign before it can go live, who signs each item, and what stays off meanwhile. The written critique of 2026-11-15 (its item C.1, "Fail-Closed Cold-Start Paralysis") counts four authorities (accountant, counsel, medical director, controller or office manager) and nearly a dozen governance artifacts. It also notes that nothing says how those roles map when one physician-owner holds several of them.

The product owner, Brett Heap, ruled on the critique on 2026-11-15. Ruling 107 authorizes this change:

> 107. (One person in several roles.) "Allowed, with each sign-off recording the role used. A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change."

This change records ruling 107 as governed requirements. **Rulings are not ratification:** the change stays draft until the owner completes `owner-ratification.md`.

Paths are relative to the root of `opensoft/openPractice-spec`.

## What Changes

This change does three things. It weakens no gate, decides no policy's content, and changes no contract. It adds no gate of its own, except what the owner's rulings order and one PROPOSED precondition, all set out as T-9 in `design.md`:
- under rulings 139 and 140, the accountant's sign-offs wait on a recorded outside engagement, the accountant's own attestation, a check of their CPA licence against the state board's public record and a match against the practice's staff and owners (ruling 139, read with ruling 149's question);
- under ruling 149, counsel sign-offs wait on a check of the named counsel against the state bar's public record;
- design call RD-11 proposes that neither is accepted while no owner link is in force, and that the accountant's attestation confirms the owners recorded. The practice manager records owner links; under ruling 193 a recorded owner grants nothing, and the first link the practice ever records is never held (PR #8's PR-15), so this precondition needs no outside accountant, and where recording the needed link would be held, counsel proceeds on their own statement of independence. Ending or changing an owner link reopens the attestation, and the matches count an owner link or staff roster entry that ended within the last 24 months. These details are put to the owner.

Ruling 107 authorizes the change, and the owner made rulings 139, 140, 147 and 149 for it on the same day. Everything beyond the rulings' words is a design call marked PROPOSED. Rulings 136 to 150 also settled most of what this draft had left open, and rulings 161, 164, 165, 184, 185 and 191 to 193 settled parts of what it follows in PR #8. The required amendments it proposed for other owners are adopted in PR #8: RA-1, RA-3 and RA-4 under ruling 148, RA-2 under ruling 143 and RA-6 under ruling 145. RA-5a to RA-5c are in PR #8 in another form, in PR-15 and its design call F-7: ruling 185 rules three of the acts, and the rest is PROPOSED; RA-5d is covered by F-7. PR #8 has also applied six of the conforming amendments (CA-1 to CA-4, CA-9 and CA-10).

- **`readiness-checklist` (RC-01 to RC-07).** One readiness checklist per practice. Each row is a go-live gate, or a policy version a named authority approves while a fail-closed default runs. It shows:
  - what the item is;
  - the requirement that enforces it at runtime;
  - the authority that signs it, and who holds that authority now;
  - its state, including `unavailable` when the gate's record cannot be read;
  - what stays off until it is done, in the requirement's own words.

  The checklist reads and displays only. It never satisfies, replaces or relaxes a gate, and every gate keeps its own fail-closed enforcement where it is defined today. Its rows follow the requirements. A use of a one-person alternative whose subject is a patient-side record appears only as a count, with an opaque reference to the audit entries, and both are shown only to readers authorized to see that PHI. `design.md` holds the inventory the rows start from: 56 items across the five merged changes, draft PR #7 and draft PR #8. It also records the five items retired, four because no requirement states what waits on them and one because PR #8 now meets it.
- **`policy-starter-kit` (SK-01 to SK-06).** The product ships a starter template for each policy a gate needs, fifteen in all, for example the hold, proxy, routing-clock and messaging-continuity policies, the on-call coverage plan, the autonomy opt-in set and the revenue-recognition policy.
  - A template is inert until the authority its requirement names signs a policy made from it.
  - Signing a template is the same act, with the same record, as signing a policy written from scratch.
  - Nothing is adopted by default or in bulk.
  - Templates are versioned, and a new template version never changes a policy already in force.

  openPractice's product team drafts the templates, counsel, an accountant and a physician review them before release, and each practice's own authority still adopts each one (ruling 147). This change writes no template content and asserts no legal, accounting or clinical rule.
- **`signer-roles` (SR-01 to SR-04).** Ruling 107, encoded:
  - one person may hold several roles;
  - every sign-off records the role under which it was given, and "an authorized person" is the practice manager unless the rule names another role (ruling 142);
  - every rule that needs two different people names its one-person alternative, and fails closed until it does;
  - an accountant's sign-off, and a counsel sign-off that a requirement records, can never be self-approved. The accountant is a licensed CPA engaged from outside the practice who is neither an owner nor an employee, attests in their own name, has the licence checked against the state board's public record, and is matched against the practice's staff and owners (rulings 139 to 141, read with ruling 149's question). A named counsel is checked against the state bar's public record and matched against the practice's staff and owners (ruling 149). The details are PROPOSED (RD-11). Apart from those checks (T-9), SR-04 adds no record and no precondition.

  `design.md` lists every existing rule that needs two different people, or that loses its meaning when one person signs both sides, with a PROPOSED one-person alternative for each. Where an alternative needed another change's text amended, `design.md` recorded the amendment for that change's owner; each required one is now in PR #8, five adopted under rulings and RA-5a to RA-5c in a PROPOSED form there. Six conforming amendments are applied in PR #8, and four remain conditional. This change edits no other change.
- **Design calls.** Twenty calls that go beyond the owner's rulings are listed in `design.md` under "Design calls for owner review". Each is PROPOSED and is also an open question for the owner.

## Dependencies

- **On `main`:** `add-reimbursement-retention-control`, `define-mvp-practice-foundation`, `define-customer-financial-data-boundaries`, `define-actors-agents-and-work-inbox` and `define-patient-gateway-and-escalation`, all draft.
- **Draft PR opensoft/openPractice-spec#7** (branch `change/follow-up-ruling-93-contract-gaps`, not merged): AH-06's consent, PG-16 (relying-party registration and its operator agreement) and EB-15's integration operator. This change cites them and should merge after PR #7.
- **Draft PR opensoft/openPractice-spec#8, the corrections change stacked on PR #7** (branch `change/critique-2026-11-15-corrections`, still being written). It carries:
  - rulings 102, 104, 133 and 138 in AH-01, AH-02 and AH-06, including counsel's recorded confirmation of a provider's cache, and the declared model path in AM-04;
  - rulings 136 (AM-07), 137 (MA-04), 141 and 146 (AM-01, AM-02), 142 (each agreement record), 143 (ER-08, ER-09), 144, 145 and 150 (PR-15, WI-03) and 148 (AO-10, AO-03, AH-05);
  - PR-15, with one party per person, same-person links, the practice's owner links, which the practice manager records, and the rule for a staff member acting on their own record, which now also reaches PG-05, PG-06 and PH-04 (design call F-7, PROPOSED);
  - this change's conforming amendments CA-1 to CA-4, CA-9 and CA-10;
  - ER-20, and the amended ER-12 and MA-07;
  - AM-07's last-resort recovery, ruled in its main lines by ruling 191, with design call D-36's details PROPOSED; ruling 184's sign-in values; and ruling 161's set-up window for the practice's first phone (design call DC-69, PROPOSED);
  - rulings 185 and 192 in PG-05, PG-06, PH-04, WI-03 and PR-15;
  - ruling 193: a recorded owner grants nothing, and the practice's owner in any requirement means the practice manager recipe holder (design call F-13's details PROPOSED);
  - AI-20's per-destination record of who books explained bank debits, which the practice manager records and the practice's accountant confirms, taking effect only once confirmed (design call F-18, PROPOSED under rulings 125 and 164), ruling 164's go-live bar, and ruling 165's claim-acceptance treatment.

  This change cites those requirement IDs and describes them, never quoting or citing line numbers, and should merge after PR #8. It reconciles against PR #8 at `ad6f4be`.
- **Draft PR opensoft/openPractice-spec#11** (`define-revenue-cycle-core`, read at `44b7d6b`), not a dependency: its PS-11 and RE-09 define the amount at which a write-off becomes material (ruling 129), which ruling 141 calls the write-off threshold. When it lands, that confirmation comes under SR-04.

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
- `policy-starter-kit` (SK-01 to SK-06): the fifteen starter templates. They are drafted and reviewed before release under ruling 147, inert until signed, signed exactly as a policy written from scratch, never adopted by default or in bulk, versioned, and assert no rule.
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
- **Amendments for other owners**, all listed in `design.md`:
  - **required:** RA-1 to RA-4 and RA-6 are adopted in PR #8; RA-5a to RA-5c are there as an extension of design call F-7, PROPOSED; RA-5d is covered by F-7;
  - **conforming (ten):** CA-1 to CA-4, CA-9 and CA-10 are applied in PR #8, checked at `ad6f4be`, including the fields ruling 149's counsel check needs; CA-5 to CA-8 apply only if their owners come to record counsel's part.
- **Runtime:** no runtime code, dependencies or vendor claims are added.
