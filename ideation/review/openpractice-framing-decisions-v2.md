# Framing Decisions v2: The Eleven Calls and the Owner's Rulings

Status: draft for the owner's review — not ratified
Kind: decision-brief
Summary: a second version of the eleven framing decisions that folds in the owner's rulings 1 to 194 wherever they bear on a framing decision. FD-1 to FD-5 and FD-10 are amended in place; the other five carry version 1's text word for word; four new decisions, FD-12 to FD-15, record the agent operation, safety routing, clinical boundary and accounting boundary the rulings established. Every changed sentence cites its rulings, and every remaining conflict is listed for the owner. Drafted under ruling 108; version 1 stays as history.
Topics: openpractice-feat-review, framing-decisions, owner-rulings
Captured: 2026-11-15

**This is a draft for Brett Heap's review. Nothing in it is ratified.** It was drafted under ruling 108: "I draft a v2 for your review that folds in rulings 1–98 and adds an index of every ruling. Version 1 stays as history." Version 1, [Framing Decisions](openpractice-framing-decisions.md), with its ratification record of 2026-08-27 and its Amendments (2026-11-14), is unchanged. Until the owner completes the pending block at the end of this document, version 1's ratified text stands, and every difference here is a proposal.

Ruling 108 says "1–98" because that was the count when it was put to the owner. Rulings 99 to 111 were accepted in the same answer of 2026-11-15, and rulings 112 to 194 later that day, so this draft folds in those of them that bear on a framing decision too. Decision 80, open when this draft was first written, was ruled the same day (ruling 80), and ruling 151 adds to its clause 12. Both govern additions to a patient's chart, in openChart and the companion systems, and rulings 152 to 160, 183, 186 to 190 and 194 settle how they apply; this draft cites them under FD-14, and the index reproduces ruling 80's approved wording in its public form.

**Built on 2026-11-15** from the tables at the revisions the index lists under "Revisions read". Whatever this draft says about a change or pull request being merged, open or in draft describes that date. Version 1's Amendments (2026-11-14) say the two changes carrying rulings 36 and 38 are "on their own branches, not yet on main"; by 2026-11-15 both were merged on `main` as unratified drafts (`opensoft/openPractice-spec#5` and `#6`). Version 1 stays untouched, so that line is left as written.

Each ruling has one row in the [Owner Rulings Index](openpractice-owner-rulings-index.md). The "Owner rulings" table in the `design.md` of each change remains the source of truth for every ruling; this draft and the index are built from those tables.

## How to read this draft

- **Carried from v1** blocks are version 1's text, word for word. They appear for every decision no ruling changed, and for the parts of an amended decision that no ruling changed.
- **Decision as it now stands** states an amended or new decision. Each sentence ends with the rulings it rests on, or "(v1)" where it is version 1's own position.
- **Changed from v1** says what differs from version 1 and which ruling made the difference, so each change can be checked against its ruling.
- **Rulings that bear on it** lists rulings that touch a decision without changing its text.
- **Open tensions** points to the list near the end. Where two rulings, or a ruling and version 1, still pull against each other, this draft does not resolve them.
- Where a ruling says "in version 1" or "not in version 1", it means the first version of what that ruling governs, not version 1 of these framing decisions; this draft quotes those words.

**Form chosen.** A ruling that changes a decision version 1 already makes is folded into that decision, beside the text it changes: FD-1, FD-2, FD-3, FD-4, FD-5 and FD-10 are amended in place. A position the rulings established that version 1 has no decision for becomes a new numbered decision after FD-11: FD-12 (agent operation), FD-13 (safety routing), FD-14 (the clinical boundary) and FD-15 (the billing and accounting boundary). This keeps version 1's numbering stable and ties every ruling to the exact sentences it supports. The three positions named in item D.2 of the critique of 2026-11-15 land here:

- **The agent-operated frame:** FD-1, amended by ruling 38, and FD-12.
- **Safety routing:** FD-13. No ruling says the routing rules are fixed; see OT-1.
- **Tier-2 human confirmation for identity and untrusted patient input:** identity in FD-5 (ruling 12), untrusted patient input in FD-12 (ruling 45). No ruling says what tier 2 requires for these actions; see OT-4.

**What is not folded in.** Rulings that govern only another system (openChart, openWallet, openSig, medXfactory, MedxEHR or HealthLinc), only packaging and process, or only a contract detail are in the index and are not restated here. They are listed under "Rulings not folded into a decision". The tension tables of the changes also record conflicts with the constitution, with openPractice's `AGENTS.md` and with design calls; this draft lists only tensions between rulings, or between a ruling and version 1.

**Sources.** The rulings were read from the tables listed in the index, at the revisions it names. Private repositories are cited by repository and change id only.

---

## FD-1. Staff-count frame

**v2 status:** amended by ruling 38; ruling 107 added.

**Decision as it now stands.**

- The agent-operated practice is the primary frame, so screens are built for exceptions and approvals (ruling 38).
- The 2–4 in-house staff mode, with the external biller as a first-class actor, remains for when agents are off (ruling 38, as worded in v1's Amendments (2026-11-14)). The outsourced biller is modelled as a first-class external actor on the Roster (bounded access, own work views, own accountability), since many small practices outsource RCM rather than employ a biller (v1).
- One person may hold several roles, with each sign-off recording the role used (ruling 107). FD-5 states the rest of ruling 107.
- The frame governs defaults and surfaces, not data structures (v1; v1's Amendments: "The watch-out still stands").

**Carried from v1, word for word.**

> **Decision:** Who is the product designed for, and is that a persona or a range?
>
> **Why:** Every "questionable" verdict in the review traced to one source —
> configuration surfaces asking the practice to author policy it has never
> articulated. A fixed frame deletes that entire class: no routing engines, no
> approval ladders, no RBAC matrix, no config-promotion pipeline.
>
> **Watch-out:** Don't design a ceiling into the schema. The frame governs
> **defaults and surfaces**, not data structures. Growth beyond the frame adds
> machinery later; it never requires redesigning the core objects.
>
> **Changes:** feature-normalization gains a "frame rule"; every atomic drops its
> enterprise-surface variants; the Roster atomic gains the external-biller actor.

**Changed from v1.**

- v1 fixed the design frame at 2–4 in-house staff. The primary frame is now the agent-operated practice, and the 2–4 staff mode is the mode for when agents are off (ruling 38). This repeats the amendment v1 already records under "Amendments (2026-11-14)".
- New: one person may hold several roles, and each sign-off records the role used (ruling 107).
- Unchanged: the external biller, the watch-out and the "Why". Ruling 38 changes the frame and says nothing about the "Why"'s "no routing engines, no approval ladders, no RBAC matrix", which pulls against rulings 34, 37, 44 and 51 (OT-1, OT-2). The actors change reads ruling 38 the same way (its T-1 and T-6).

**Rulings that bear on it:** 35 (the openPractice app covers administrative work and approvals; FD-14), 37 (the agent ladder; FD-12), 141 (the Roster gains an outside-accountant entry; FD-5), 145 (the practice's owners are recorded; FD-5) and 193 (a recorded owner grants nothing by itself; FD-5).

**Open tensions:** OT-1, OT-2.

---

## FD-2. Expectation source (learned vs. contract)

**v2 status:** amended by rulings 123 and 126.

**Decision as it now stands.**

- Version 1's decision, carried below word for word, stands, except for the part of its "Changes" line that says "the expectation engine leaves the beta's critical path" (v1).
- Payer expectations come from the estimator, not loaded fee schedules (ruling 126).
- The revenue-estimate record is built in full now, with a simple first method that the practice's accountant approves before accounting goes live (ruling 123). An estimator, in that simple first form, is therefore on the beta's critical path, for the reasons OT-8 gives (rulings 30, 123).

**Carried from v1, word for word.**

> **Decision:** Where does "what should this payer pay us" come from?
>
> **Recommendation — accept (invert the current default).** The **learned
> baseline is primary**: expected allowables are a statistic over the practice's
> own posted remittances (median by payer × code × modifier × POS, with n and
> spread). The **contract is the arbiter**, loaded only when the practice wants to
> dispute — and *disagreement between promise-memory and contract is itself the
> highest-value alert*.
>
> **Why:** Zero configuration, self-improving, absorbs sequestration and mid-year
> fee changes for free. Nobody at a 2–4 person practice will load and maintain
> fee schedules; a contract-first engine is a contract-management product nobody
> will populate.
>
> **Watch-out — cold start:** at go-live there is no history. Mitigate in order:
> seed from migrated EOB/ERA history where available; use the public Medicare fee
> schedule as a reference band; and let variance detection activate progressively
> as n grows rather than pretending confidence at n=1.
>
> **Changes:** payer-contract-and-reimbursement-integrity is rewritten around
> promise memory; the "governed reimbursement terms" feat demotes to an optional
> arbiter import; the expectation engine leaves the beta's critical path.

**Rulings that bear on it:** 22, superseded, would have valued accruals from "the payer's observed history (FD-2)". Ruling 30, which supersedes it, sets the revenue-recognition basis for accounting postings (FD-15): net revenue is estimated at the date of service, and the practice's accountant approves the estimator before go-live. Ruling 30 pulled against FD-2's "the expectation engine leaves the beta's critical path" (OT-8), which rulings 123 and 126 now answer.

**Changed from v1.**

- v1's "Changes" line says "the expectation engine leaves the beta's critical path". Ruling 123 puts the estimate record, with a simple first method the accountant approves, before accounting goes live, and ruling 126 makes the estimator the source of payer expectations. This answers OT-8.
- Unchanged: the learned baseline as primary, the contract as arbiter, the "Why" and the cold-start watch-out. No ruling says whether the estimator's later methods are FD-2's learned baseline. `define-revenue-cycle-core` proposes accountant-set ratios as the first method and a learned method as a later version (its T-5); that is the change's design, not a ruling.

**Open tensions:** OT-8, answered by rulings 123 and 126.

---

## FD-3. Period close

**v2 status:** amended by rulings 4, 8, 26 and 30.

**Decision as it now stands.**

- **Immutability at post time** (corrections are dated reversals, never edits) + **as-of queries** + the **daily balanced day** from End-of-Day Close. Month-end is printing, not a procedure (v1).
- Revenue re-estimates and true-ups are new dated entries, never edits. A close re-estimate is dated in the period being closed if it is open, otherwise on the first open date (ruling 30).
- The GL date is the date of service if that period is open, otherwise the first open date, with a late-charge flag. The sync timestamp is kept separately and is never an accounting date (ruling 4). Ruling 4's table row adds a scope note "stated in fix round 3", which is the change's statement and not the owner's words: the GL-date rule governs charge postings, and payments, refunds and deposits use their own event or bank date under the same closed-period rule.
- The accountant still needs a stable artifact. **Export-batch freeze** provides it: once a journal batch is exported to the accounting destination, that batch is versioned and immutable; later corrections land in the next batch with back-references. The accountant gets stability; the practice never sees a lock (v1, with "QuickBooks" read as the accounting destination; rulings 7, 8, 26). Batches and their freeze, release and retries belong to the accounting contract's posting mechanics (ruling 26). In the MVP that destination is ERPNext, which the MVP calls only through its REST API (ruling 8).

**Carried from v1, word for word.**

> **Decision:** Is there a close ceremony, or are the books always closed?
>
> **Why:** No small practice runs a close ceremony, so a ceremony either blocks
> the product or gets skipped — both fatal. Reversal-based correction preserves
> full auditability without a lock.
>
> **Changes:** financial-control-and-accounting-bridge drops the "controlled close
> and correction" ceremony; End-of-Day Close (new atomic) becomes the daily
> integrity unit; accounting export gains batch versioning.

**Changed from v1.**

- v1's watch-out named QuickBooks as the place a batch is exported to. The rulings speak of destinations, every one of which starts in `summary-journal` (ruling 7), assign batches and their freeze to the posting mechanics (ruling 26), and have the MVP call ERPNext (ruling 8). The watch-out now reads "the accounting destination".
- New: the GL-date rule (ruling 4). The rule for payments, refunds and deposits is the scope note in ruling 4's table row, stated in fix round 3, not the owner's words.
- New: revenue re-estimates and true-ups are dated entries, never edits, with the dating of a close re-estimate (ruling 30).

**Rulings that bear on it:** 130 puts the deposit slip in the self-pay slice and the drawer count and close sheet in a later change; 124 makes every ledger entry move money between two fixed buckets, so it always balances (FD-15). Neither changes FD-3's text.

**Open tensions:** OT-6.

---

## FD-4. Estimates and GFE

**v2 status:** amended by rulings 113, 114, 121, 122, 126, 130, 172 and 179; rulings 166, 175, 177 and 193 added.

**Decision as it now stands.**

- Version 1's decision, carried below word for word, stands: one self-pay / time-of-service money path, with the GFE register riding the same path (v1).
- Self-pay comes first, then Medicare Part B as primary. Commercial and secondary claims come later; the data shape allows both from the start (ruling 113); meanwhile they are recorded through the manual path (ruling 166). The self-pay visit is the first slice built: it fixes every data shape and needs no payer, clearinghouse or processor (ruling 114). It also builds two checks that belong to the later accounting feature, that a charge follows an allowed billing route and that sign-offs are enforced, and only those two; the accounting feature then adopts them instead of building them again (ruling 177).
- Good Faith Estimates are single-provider estimates for scheduled self-pay care: issue, replace, deliver with proof, retain, watch the variance, handle a dispute (ruling 121). Until counsel has verified the deadlines, contents and other parameters, openPractice issues none, and the practice issues estimates outside the system (ruling 172).
- Desk payments, receipts, allocations and credits are recorded. Refunds stay human-only. Statements are printable. The payment processor and the pay link come later (ruling 122). The deposit slip is in the self-pay slice; the drawer count and close sheet come in a later change (ruling 130). The record that a refund was issued, which moves no money by itself, is built in feature 2 so the self-pay slice can finish end to end; matching it to the bank statement stays in feature 4 (ruling 175). A prepaid visit that is cancelled, missed or abandoned keeps its Thread open until the patient's money is applied, refunded or moved to a credit case, and a Thread never closes while the practice owes the patient (ruling 179).
- One practice price list and one self-pay discount (ruling 126). Setting the discount, a power some rules gave "the owner", is held by the practice manager role (ruling 193).

**Carried from v1, word for word.**

> **Decision:** Do estimates, self-pay, and the Good Faith Estimate enter the beta?
>
> **Recommendation — accept (promote to beta-scoped money path).** One
> **self-pay / time-of-service money path**: estimate → GFE document where
> required → collect at visit → receipt → thread closed same day. The GFE
> register (issued GFEs, amounts, dispute window) rides the same path.
>
> **Why:** This was the weakest treatment in the packet — as sequenced, the beta
> collects no money before or at the visit, and cash patients sit entirely outside
> the integrity model. The GFE is a legal obligation for uninsured/self-pay
> patients, not a feature choice. It is also the *simplest* money path — no payer,
> no clearinghouse — so it's the natural first proof of the money model.
>
> **Watch-out:** Keep estimate sophistication out of scope: beta estimates are
> schedule-of-charges arithmetic plus promise-memory copay/deductible hints, not
> benefit-engine modeling.
>
> **Changes:** scope-and-sequencing gains the self-pay path in an early slice;
> patient-billing atomic gains time-of-service collection; a new GFE register
> feat lands in the documents/attachments or billing atomic.

**Rulings that bear on it:** 24 and 25 treat self-pay and "direct healthcare charges under FD-4" as medical-billing guarantor accounts, whose customer records reach the books only in `customer-detail` mode (FD-15). Rulings 41 and 52 define the payment hold, which never blocks billing contact (FD-13).

**Changed from v1.**

- v1 put the self-pay path "in an early slice". The self-pay visit is now the first slice built (ruling 114), and self-pay comes before Medicare Part B (ruling 113).
- New: the scope of the Good Faith Estimate (ruling 121), of payments and statements (ruling 122), of prices (ruling 126) and of the deposit slip (ruling 130); the manual path for commercial and secondary claims (ruling 166), the refund record (ruling 175), the two accounting checks (ruling 177) and who sets the self-pay discount (ruling 193).
- v1's path includes a "GFE document where required". Until counsel has verified the parameters, openPractice issues none, and the practice issues estimates outside the system (ruling 172).
- v1's path ends "thread closed same day". The Thread of a prepaid visit that is cancelled, missed or abandoned now stays open until the patient's money is applied, refunded or moved to a credit case, and a Thread never closes while the practice owes the patient (ruling 179).
- Unchanged otherwise: the money path, the GFE register, the "Why" and the watch-out.

---

## FD-5. Governance primitive

**v2 status:** amended by rulings 9, 12, 33, 37, 119, 120, 129, 150, 167 to 171, 173 and 181; rulings 30, 41, 105, 107, 116, 123, 128, 134 to 137, 139 to 142, 144 to 149, 151, 152, 157, 161, 163, 165, 172, 174, 180, 182, 184 to 186 and 191 to 193 added.

**Decision as it now stands.**

Replace approval with **reversibility, visibility, and delay**, shipped as four fixed tiers (no tier authoring) (v1). FD-5 governs: each action's tier decides whether it needs an approval, a notification or a delay-and-cancel release, including the deadline escapes (ruling 33).

1. **Do + undo** — reversible, silent, undoable from the record (v1).
2. **Do + notify** — reversible, surfaced in Today/close (v1). The EHR-link action `link_existing` and identity-review resolution are tier 2 (ruling 12). What tier 2 requires for them is open (OT-4). Exactly matched remittance lines post and notify, undoable; anything not exact is a proposal for a human (ruling 120). Ruling 169 sets the conditions for an exact match. The plain undo lasts until the payment is matched to a bank deposit; after that a person corrects the posting with a reason, which still reverses everything it did (ruling 170).
3. **Delay + cancel window** — outbound actions (claim batches, statements, outreach) execute after a visible countdown unless cancelled (v1). Claim batches need an explicit release by a named signer, keeping the escape near a filing deadline (ruling 119). The escape opens 14 days before the deadline, and each practice may change that (ruling 167).
4. **Hard stop** — true approval, reserved for the constitutionally irreversible few: refunds leaving the practice, material adjustments, record merges (v1). Also hard delete of a record created in error, and enabling `customer-detail` mode (rulings 9, 12, 33); these two are a conscious FD-5 exception (ruling 12). Tier 4 stays human-only (rulings 33, 37). What makes a write-off material is an amount each practice sets at setup, confirmed by its accountant: below it is do-and-notify, and everything else is human-only (ruling 129). Until the practice has set that amount, a named human does every write-off (ruling 173), and a denial is never written off automatically (ruling 171). Record merges stay on the list, but when two records turn out to be the same person they are linked, not merged, "in version 1": the link is reversible, and a full merge, with its undo, would be its own later change (ruling 150).

**Every gate carries a deadline escape:** a gate that would stall a claim past timely filing auto-releases at tier 3 with notification, because the gate otherwise destroys what it protects (v1). The deadline escape is stated for gates (ruling 12). Two rulings narrow it. Ruling 128 holds a claim when the provider is not enrolled with the payer (hold, never reject, and release in one action once enrolled), and the escape applies to that hold only while an enrollment application is pending (ruling 168). A claim held for missing documentation or a missing authorization never sends itself at the deadline: it escalates to a person, and a named human may release an authorization hold with a recorded reason (ruling 181).

**Who signs.** Where a tier calls for an approval, release decision or attestation, the signer may be a named human or a named AI agent identified by an openWallet ID. Agent signing is opt-in per action type and never on by default. Every sign-off records the signer's identity, including an agent's openWallet ID, with provenance (ruling 33), and the role the signer used (ruling 107). Tier 4 stays human-only (rulings 33, 37). A medical decision in the chart is another matter: it counts only once a practitioner signs it off in openChart, "in version 1" (rulings 151, 186); openChart records no real decision until openWallet and openSig can do their part (ruling 152); and until a practitioner signs, an AI only ever makes recommendations (ruling 157). FD-14 cites these.

**Agents.** An agent's autonomy is set per action type on a ladder that starts at propose, and an agent never acts with less friction than the action's FD-5 tier requires (ruling 37). FD-12 states the ladder.

**One person, several roles.** One person may hold several roles (ruling 107). A rule that needs two people names a one-person alternative, as ruling 98 does. Accountant and counsel sign-offs cannot be self-approved. The readiness checklist and starter policies go in a new change (ruling 107).

- **The accountant.** The accountant sign-off is given by someone engaged from outside the practice who is neither an owner nor an employee; an employed accountant cannot, "in version 1" (ruling 139). That accountant is a licensed CPA, checked against the state board's public record, who attests in their own name (ruling 140). The Roster gains an outside-accountant entry that can read the books and approve accounting policy, estimator versions and the write-off threshold, and nothing else (ruling 141).
- **Counsel.** Counsel needs no sign-in (ruling 141). A named counsel is checked against the state bar's public record and matched against the practice's staff and owners (ruling 149).
- **Owners and roles.** The practice's owners are recorded as dated links from the practice to each owner (ruling 145). Where a rule says "an authorized person" records or verifies an agreement, that is the practice manager unless the rule names another role, and each record keeps the role used (ruling 142). Assigning the medical director role in openPractice carries ruling 98's licence check (ruling 146). A recorded owner grants nothing by itself and is used only to check independence; a power some rules gave "the owner", such as seeing full bank account numbers or setting the self-pay discount, is held by the practice manager role (ruling 193).
- **Nobody else.** Where nobody else in the practice holds the permission for that act (ruling 192), a staff member may act on their own record even though a proposal is never decided by its proposer: both roles are recorded and the outside accountant is notified, and without an outside accountant on the Roster the act waits (ruling 144). The notice lists everyone else who was on the roster (ruling 192). An agent's sponsor does not alone sign that agent's daily digest; a sponsor granting their own agent more scope notifies the other managers; and one person gives two sign-offs only when nobody else can. Each of these applies only where a second eligible person exists (ruling 148). Three more acts are closed: confirming one's own app-account link to a patient's record, confirming one's own authority as a patient's representative, and lifting a payment hold on a balance one owes. Each needs a second person where one exists, and uses ruling 144's one-person alternative where none does (ruling 185).
- **Starter policies.** They are drafted for the practice and reviewed by counsel, an accountant and a physician before release, and each practice's own authority still has to adopt each one (ruling 147).

**Signing in and signing off.** The mobile approver signs in with face, fingerprint or passkey for both steps, never a typed password. Opening skips the prompt if the user verified in the last 60 seconds. Every sign-off still needs its own (ruling 105). A passkey that syncs across a person's devices counts, but only on a device the person has enrolled in openPractice (ruling 134). When face or fingerprint is unavailable, the phone's own unlock code may stand in (ruling 135). At the workstation everyone signs in with two steps, a passkey or a password plus a second step, and each sign-off needs its own fresh check, as on the phone (ruling 136). The second step is an enrolled phone or a security key, never a texted or emailed code, and a session ends after 15 minutes idle or 12 hours; a practice may tighten these (ruling 184). An approval phone is enrolled when a colleague with their own enrolled phone vouches: a first phone works at once, and a replacement waits 24 hours and can be cancelled. With no colleague, codes go to two contact details on file, at least one of them on file for 7 days, with the same wait (ruling 137). For a brand-new one-person practice, which under ruling 137 "waits about eight days to enrol its first approval phone", ruling 161 answers "Allow it during initial set-up?" with "Yes, until the first phone is enrolled or the practice goes live, whichever comes first." A sole administrator who has lost their passkey, their recovery code and access to one contact detail is locked out for good; for that case ruling 191 allows a last-resort recovery started from the server by whoever runs the installation. It is recorded, announced to every contact detail and device on file each day for 7 days, and cancellable from any of them. The new sign-in is handed over in person only, with a record of how the person's identity was confirmed. It trusts whoever runs the server, who already controls the data (ruling 191).

**Approvals that rulings require outside the tier list.** The practice's accountant approves the reporting basis, estimator, classification, materiality and reopening policy before go-live (ruling 30). The medical director approves the payment-hold policy, and the exact policy needs the practice's counsel (ruling 41). The EHR supplies coded facts and a named human biller confirms them (ruling 116). Claim batches need an explicit release by a named signer (ruling 119). The first estimating method is approved by the accountant before accounting goes live (ruling 123). The amount that makes a write-off material is set by each practice at setup and confirmed by its accountant (ruling 129). The accountant also confirms how each kind of money movement maps to the books (ruling 163) and that a claim counts as billed when it is accepted (ruling 165). Counsel verifies the Good Faith Estimate parameters before the system issues any (ruling 172) and confirms the 60 days of ruling 180. A named human moves a balance from one guarantor's account to another, with a notification, and agents may only propose it (ruling 174). Twelve small recording actions that have no tier yet, such as recording a no-show, are done by a named human with a notification (none for snoozing a stuck Thread); agents only propose, and the owner rules on each at ratification (ruling 182). See OT-5.

**Carried from v1, word for word.**

> **Decision:** What replaces the human-approval gate?
>
> **Why:** The second human does not exist; approval queues rot within a month.
> All five reviewers independently converged on this replacement. Tier 4
> preserves the constitution's "no autonomous money movement" line.
>
> **Changes:** deletes the generic approval framework; resolves the
> timely-filing conflict outright; every atomic's "human confirms" language is
> rewritten against the four tiers.

**Changed from v1.**

- Tier 4 adds hard delete of a record created in error and enabling `customer-detail` mode to v1's refunds, material adjustments and record merges (rulings 9, 12, 33).
- `link_existing` and identity-review resolution are assigned tier 2 (ruling 12).
- New: who may sign, opt-in agent signing with an openWallet ID, and the signer's identity recorded with provenance (ruling 33); the role recorded, one-person alternatives to two-person rules, and no self-approval of accountant or counsel sign-offs (ruling 107).
- New: the agent ladder, capped by the FD-5 tier (ruling 37; FD-12).
- New: how a sign-off is verified on a phone (ruling 105).
- New: two approvals outside the tier list, by the accountant (ruling 30) and by the medical director (ruling 41).
- Tier 3: claim batches need an explicit release by a named signer, keeping the escape near a filing deadline (ruling 119), not a countdown alone. `define-revenue-cycle-core` records this as a conscious exception (its T-4). The escape opens 14 days before the deadline (ruling 167).
- Tier 2: exactly matched remittance lines post and notify, undoable (ruling 120), on the conditions of ruling 169, with a plain undo until the payment is matched to a bank deposit and, after that, a correction by a person with a reason (ruling 170).
- Tier 4: what makes a write-off material is an amount the practice sets at setup and its accountant confirms (ruling 129); two records of the same person are linked, not merged, "in version 1" (ruling 150); until the material amount is set, every write-off is a named human's, and a denial is never written off automatically (rulings 171, 173).
- The deadline escape no longer covers every gate: a hold for a provider not enrolled with the payer escapes only while an application is pending (ruling 168), and a hold for missing documentation or a missing authorization escalates to a person instead (ruling 181).
- New: synced passkeys, the phone's unlock code, workstation sign-in and phone enrolment (rulings 134 to 137); the workstation's second step and session limits (ruling 184), a first phone at initial set-up (ruling 161) and last-resort recovery (ruling 191).
- New: who gives the accountant and counsel sign-offs and how each is checked, the outside-accountant roster entry and the record of owners (rulings 139 to 141, 145 and 149); "an authorized person" (ruling 142); the medical director's licence check in openPractice (ruling 146); the case where the practice has nobody else (rulings 144, 148, 185 and 192); who holds a power some rules gave "the owner" (ruling 193); and how starter policies are written (ruling 147).
- New approvals outside the tier list: the biller's confirmation of coded facts (ruling 116) and the first estimating method and write-off threshold (rulings 123 and 129); the accountant's and counsel's confirmations of rulings 163, 165, 172 and 180; and the named-human rules of rulings 174 and 182.
- New: a medical decision in the chart counts only once a practitioner signs it off in openChart (rulings 151, 152, 157 and 186; FD-14).
- Unchanged: the four fixed tiers, the "Why" and the "Changes". The "Why"'s "The second human does not exist; approval queues rot within a month" pulls against rulings 30, 37, 41, 107, 116, 119, 139, 141, 144, 173, 174, 181 and 182 (OT-3, OT-5).

**Open tensions:** OT-3, OT-4, OT-5, OT-7.

---

## FD-6. Differentiator statement

**v2 status:** unchanged. Version 1's text, word for word:

> **Decision:** What is provenance *for*?
>
> **Recommendation — accept.** Provenance is restated as **generated artifacts**:
> the one-click appeal packet, the auto-derived secondary claim, the
> retro-rebill list, the stuck-money worklist, the printed audit binder, and
> **undo**. Rule: every provenance link must name the artifact it emits and the
> minutes it saves — if none can be named, store less. The queryable API remains
> as substrate, not as the headline and not as the build order.
>
> **Why:** A 2–4 person practice never calls a trace API. Artifacts are the same
> data made valuable — and undo (FD-5) is itself a provenance artifact, which
> makes the two decisions one architecture.
>
> **Changes:** feature-list-overview's three differentiator claims are restated;
> the provenance viewer and the AI-specific decision ledger are deleted; build
> order starts from the artifact list.

**Rulings that bear on it:** 33 requires every sign-off to record the signer's identity with provenance, and 107 the role used; 21 marks a manual charge as manual, with provenance. None changes FD-6's text.

**Changed from v1:** nothing.

---

## FD-7. Spine ownership

**v2 status:** unchanged. Version 1's text, word for word:

> **Decision:** Which of the four competing identity spines owns correlation?
>
> **Recommendation — accept.** **One thread identity.** The Visit Thread is
> written as a versioned contract **before any consumer**, and the four current
> descriptions (revenue cycle's financial thread, platform's transaction trace,
> platform's correlation-as-data, the AI decision record) collapse into views
> over it. Non-visit threads (credit balance, unmatched referral, recoupment
> case, migrated balance) are first-class and enumerated.
>
> **Why:** Four spines means provenance built twice and joined never. The
> reviewers flagged this as "one of the two must be authoritative or both will be
> half-built."
>
> **Changes:** The Visit Thread atomic is pulled forward into Wave 3 as the first
> document written; platform's trace/correlation feats become requirements on the
> Thread contract rather than separate builds.

**Rulings that bear on it:** 31 makes openPractice the system of record for the administrative appointment, with the EHR owning the encounter (FD-14). 162 gives the Thread its own published format, with a version and a checksum, which makes "a versioned contract before any consumer" checkable. 179 keeps the Thread of a cancelled, missed or abandoned prepaid visit open until the patient's money is applied, refunded or moved to a credit case: a Thread never closes while the practice owes the patient (FD-4). None changes FD-7's text.

**Changed from v1:** nothing.

---

## FD-8. First external milestone

**v2 status:** unchanged. Version 1's text, word for word:

> **Decision:** What must be live (not test traffic) for the beta to count?
>
> **Recommendation — accept.** Beta acceptance requires **one live ERA posted
> end-to-end and one live merchant settlement reconciled to a bank line**. The
> test-traffic escape is retired.
>
> **Why:** Test traffic proves plumbing; the product's two hardest external
> dependencies (clearinghouse enrollment, merchant settlement data) are exactly
> where healthcare betas die, and deferring them defers discovering the product
> doesn't work.
>
> **Watch-out — critical path:** clearinghouse and merchant enrollment take
> weeks and are partner-paced. Both enrollments start at beta kickoff, tracked as
> the implementation's first two threads.
>
> **Changes:** scope-and-sequencing's acceptance criteria are rewritten; the
> implementation-lifecycle atomic gains "enrollment as first threads."

**Rulings that bear on it:** 5 has openPractice reconcile deposits only, reading revenue bank lines from the accounting destination (FD-15). 8 says that, for the ERPNext integration, the remote topology alone satisfies MVP acceptance. 118 submits claims at first through a simulated payer, plus a manual path that records a claim sent outside the system, with the live clearinghouse as a later feature; it says nothing about beta acceptance. None changes FD-8's text.

**Changed from v1:** nothing.

---

## FD-9. Cutover

**v2 status:** unchanged. Version 1's text, word for word:

> **Decision:** How does a practice move from its legacy system?
>
> **Recommendation — accept, with one addition.** **Progressive activation**
> replaces the cutover project: schedule → eligibility → charges → claims, each
> activated when its "first" succeeds. Migration is scoped to **open items only**
> (future appointments, active patients on the schedule, open balances as summary
> threads). Legacy AR **runs off in the legacy system by default**.
>
> **Addition:** an **"old-system balance" thread type**, so the owner's money
> view still shows whole-practice truth (openPractice money + legacy run-off) and
> the run-off is watched work, not invisible work.
>
> **Why:** Full AR conversion is the classic implementation killer — highest
> effort, highest error rate, lowest durable value. Open-items scoping bounds
> risk to what the practice actually needs on day one.
>
> **Changes:** implementation-and-configuration-lifecycle drops cutover control
> room, config promotion, and policy simulation; gains progressive activation and
> the six "firsts"; the Thread contract gains the migrated-balance type.

**Rulings that bear on it:** 131 defines the record type for balances carried over from an old system now, and builds the import before the first practice that needs it; 176 builds that import in feature 4, or earlier if the first practice has old balances. Neither changes FD-9's text.

**Changed from v1:** nothing.

---

## FD-10. Channels

**v2 status:** amended by ruling 36, for half (b).

**Decision as it now stands.**

- **(a)** **Phone and fax become owned atomics** — the Phone Console / call-disposition capture and the fax side of referral intake (confirmed-destination registry, inbound fax → Pile). The system diagram and the feature set finally agree (v1).
- **(b)** The HealthLinc integration contract and escalation routing come into the MVP; HealthLinc's own app features stay on HealthLinc's roadmap (ruling 36). The **patient money view does not** defer: balance + pay-now behind a texted link, no account required, is beta scope (it is the payments half of FD-4's money path) (v1; v1's Amendments: "The patient money view remains beta scope as ratified").

**Carried from v1, word for word.**

> **Decision:** Do phone and fax get owned, and what does app deferral not defer?
>
> **Why:** Phone is the dominant small-practice channel and is currently
> invisible, making every queue and metric structurally wrong from week one. Fax
> is how referrals actually arrive. The texted pay link is where patient dollars
> actually come from.
>
> **Changes:** two new Tier-1 atomics (phone, inbound pile/fax); engagement and
> referral atomics point at them; patient-billing gains the no-account pay link.

**Changed from v1.**

- v1's (b) said "The companion app stays deferred". The HealthLinc integration contract and escalation routing now come into the MVP, and HealthLinc's own app features stay on HealthLinc's roadmap (ruling 36). This repeats the amendment v1 already records under "Amendments (2026-11-14)".
- Unchanged: half (a), the patient money view, the "Why" and the "Changes".

**Rulings that bear on it:** 34, 39, 41, 50, 51, 52 and 71 govern how patients reach the practice through HealthLinc (FD-13, FD-14). 122 brings the payment processor and the pay link later; it says nothing about beta scope, so half (b)'s text stands.

---

## FD-11. Licensed IP

**v2 status:** unchanged. Version 1's text, word for word:

> **Decision:** How do licensed code sets (CPT®) and X12 implementation guides
> ship in an open-source product?
>
> **Recommendation — accept the shape; verify with counsel.**
> **License-free core + licensed overlay, never licensed content in the repo:**
>
> - The open-source core ships only license-free content: ICD-10, HCPCS Level II,
>   place-of-service, CARC/RARC, and the *structure* for procedural coding.
> - CPT ships as a **bring-your-own-license overlay pack**: first-run import from
>   the practice's own licensed source (practices billing CPT already hold usage
>   rights through their coding workflow; the import formalizes it).
> - X12 knowledge is **abstracted behind the clearinghouse connector** so the
>   product need not redistribute implementation guides; if a direct-to-payer
>   path ever ships, it ships as a hosted service that carries the license.
> - A future hosted offering may bundle licensing commercially; the repo never
>   does.
>
> **Why:** This is the only shape that preserves open-source distribution without
> either shipping unlicensed IP or making the product unusable out of the box.
> It also cleanly forks the onboarding story: first-run setup includes "load your
> code sets," which FD-9's progressive activation already accommodates.
>
> **Watch-out:** AMA licensing terms for practice-held CPT rights need legal
> verification before the onboarding flow is finalized. Track as an explicit
> open risk in scope-and-sequencing.
>
> **Changes:** feature-normalization gains the licensing rule; charge-capture's
> code-catalog feat is split core/overlay; implementation-lifecycle gains the
> code-set import first-run step.

**Rulings that bear on it:** 78 has openChart bundle no drug knowledge, each practice connecting a licensed or verified source. It governs openChart, not openPractice, and does not change FD-11's text.

**Changed from v1:** nothing.

---

## FD-12. Agent operation (new)

**v2 status:** new. Rulings 33, 34, 37, 45, 46, 68, 93, 97, 99, 100, 102, 104, 132, 133, 138, 148, 173, 174 and 182.

**Decision.**

- Administrative questions are answered by Hermes or routed to the office manager or staff (ruling 34). In the MVP, Hermes answers routine administrative questions, and the read-only answer types are in the agent operations contract (1.0), alongside the registry operations (ruling 46).
- Hermes may reply to a patient without a human only with read-only answers. Such an answer changes nothing and tells the signed-in patient only their own administrative information (appointment time, balance, coverage on file) or practice-approved information (hours, policies). During a conversation the agent can read only that one patient's data; each answer type is switched on by the practice; no clinical content, and anything uncertain goes to staff; the patient can always ask for a human. Anything that changes something stays a proposal or follows the ladder (ruling 45).
- Hermes may read the administrative question it is answering, because it cannot answer what it cannot read. Clinical text and documents stay refused (ruling 99).
- Autonomy ladder per action type: observe → propose → act with notice → act after a delay. Every action type starts at propose, and the practice opts each one up. Tier 4 stays human-only. The ladder reconciles with FD-5 and BAB-17, and an agent never acts with less friction than the action's FD-5 tier requires (ruling 37). Where a tier calls for a sign-off, an opted-in named agent identified by an openWallet ID may sign (ruling 33). In the chart, by contrast, an AI only ever makes recommendations until a practitioner signs (ruling 157; FD-14).
- In the revenue-cycle core everything also starts at propose, and a practice may opt up as far as: act with notice for booking, rescheduling, drafting charges, allocating payments and building claims; act after a delay for sending statements; and propose only for cancelling, finalizing charges, issuing estimates, releasing claims, write-offs, refunds and estimator versions (ruling 132). A named human does every write-off until the practice has set its material amount (ruling 173), every move of a balance between guarantors, with a notification (ruling 174), and, until the owner rules on each, each of twelve small recording actions not yet tiered, with a notification except for snoozing a stuck Thread (ruling 182); agents may propose these and nothing more.
- An agent's sponsor does not alone sign that agent's daily digest, and a sponsor granting their own agent more scope notifies the other managers, where a second eligible person exists (ruling 148).
- On a request mode below the rung the practice set for an agent: "Not in version 1. Lower the rung in openPractice instead." (ruling 100)
- Hermes's administrative patient messages are separated from clinical ones in medXfactory's local policy. Administrative messages follow ruling 45's guardrails with no clinical review. Consent and data-sharing conditions still apply (ruling 68).
- Hermes needs no per-patient consent before a model provider processes administrative data: the provider agreement is enough for practice operations. Consent applies to AI answering the patient directly (ruling 97). The follow-up to openPractice's merged drafts carries the consent check (ruling 93). Design rests on ruling 97's answer now, and counsel's confirmation is a go-live condition (ruling 102).
- Model providers keep nothing: zero data retention, verified the way the BAA is. Deletion with a receipt applies to the agent operator only (ruling 104). A provider may hold a short-lived in-memory cache that is never written to disk or logs, with a short stated limit (ruling 133). Counsel's confirmation of that is a go-live condition, as in ruling 102: no patient data goes to a caching model path until it is recorded (ruling 138).

**Changed from v1.** New. Version 1 has no decision on agents. FD-1's frame names the agent-operated practice (ruling 38), and FD-5 caps every agent by the action's tier (ruling 37). FD-6 still deletes "the AI-specific decision ledger"; no ruling restores it.

**Open tensions:** OT-2, OT-3, OT-7.

---

## FD-13. Safety routing (new)

**v2 status:** new. Rulings 34, 41, 42, 43, 44, 50, 51, 52, 71, 101, 106, 143 and 178.

**Decision.**

- openPractice is the gateway and router. If the patient is active and not on payment hold, the patient gets access. Clinical questions go to openChart, the doctor's clinical interface, where the doctor reads and answers. An administrative question is answered by Hermes or routed to the office manager or staff. openPractice keeps only a routing record (who, when, category, urgency, reference), with no clinical content (ruling 34). "Active" means the patient's registry record is active and, where the practice sells memberships, the membership is current (ruling 42).
- Category and urgency are proposed by HealthLinc's coach or by medXfactory's medical AI, and openPractice's routing rules apply them (rulings 44, 51). Both use the conservative defaults: an uncertain category is clinical, an uncertain urgency is urgent, and urgency is never lowered. openPractice's rules never downgrade the proposal, and an escalation to the human doctor may originate from either source (ruling 51).
- When a patient writes straight to the doctor with no AI triage, HealthLinc asks a short red-flag checklist. Any red flag proposes `urgent`, with emergency guidance; no red flags, `routine`; a skipped checklist, `priority`. The patient can raise urgency but never lower it (ruling 71). The gateway change records ruling 71 as the later, more specific ruling for that case, over ruling 51's default (its T-12).
- Staff can re-route a misfiled item but cannot answer outside their lane. A mixed question is split (ruling 44). When a mixed question is found late, openPractice keeps deleting its text; the administrative part comes back as the same question, and a reminder stays on the lane so it is never lost (ruling 106).
- openChart owns the queue and the response clock for routed clinical questions. openPractice gets an acknowledgment for each handoff, and alerts the office manager if openChart does not acknowledge or the clock runs out (ruling 43). When an urgent item has no clinician acknowledgment in time, openPractice alerts the office manager; this changes no clinical path (ruling 101).
- A payment hold blocks non-urgent clinical messaging and new non-urgent scheduling only (ruling 41), and may withhold non-urgent medical-AI consults with the same safeguards (ruling 52). It never blocks urgent escalations, billing contact, or access to the patient's own records (ruling 41), and urgent is never withheld (ruling 52). Placing or lifting a hold is a logged, tiered action, and the patient is told. The medical director approves the hold policy, and the exact policy needs the practice's counsel (ruling 41). The desk may still record the arrival of a patient under a hold who walks in: that is not new scheduling, staff see the hold, and the gateway's closed list of exceptions gains it (ruling 178).
- Cut-offs other than a payment hold get safeguards too. If the access check is unavailable, routine clinical messages still route to openChart. A lapsed membership or a deactivation stops messaging only after the patient is told and a policy-set grace period has passed. A clinician can keep access open for continuity of care (ruling 50).
- In a one-clinician practice an unanswered urgent question may have nobody to escalate to. Naming a covering clinician is not required "in version 1": the escalation that reached no second person is recorded, the readiness checklist warns, and the medical director may name cover in their policy (ruling 143).

**Not stated by any ruling.** Whether the routing rules are fixed or authored by the practice. The gateway change ships them as a fixed, versioned set, with practices configuring only lanes, hours and the on-call roster; that is its design call DC-17, not a ruling. The critique's phrase "fixed safety routing" rests on that design call (OT-1).

**Changed from v1.** New. Version 1 has no decision on routing patient questions. FD-1's "Why" names "no routing engines" among the things a fixed frame deletes (OT-1).

**Open tensions:** OT-1.

---

## FD-14. The clinical boundary (new)

**v2 status:** new. Rulings 10, 11, 17, 21, 27, 29, 31, 34, 35, 39, 51, 52, 53, 54, 69, 70, 87, 88, 115 and 116.

**Decision.**

- openPractice keeps only a routing record, with no clinical content (ruling 34). It also receives billable facts from a linked EHR (rulings 21, 87 and 88, below). What "no clinical content" means once those facts arrive is open (OT-9). For now the physician uses openChart, on mobile or workstation; the openPractice app, on workstation and mobile, covers administrative work and approvals. This is a current-phase decision and can be revisited (ruling 35).
- There is no master patient identity across HealthLinc, openPractice and openChart. HealthLinc links to each backend through that backend's own link contract (ruling 39).
- openPractice is the source for administrative demographics and publishes them to a linked EHR; each system owns its own record (ruling 29). openChart refuses direct edits to a linked patient's projected fields and routes them to openPractice as correction requests, intake flows included (ruling 17). Sex assigned at birth and sexual orientation stay out of openPractice's registry (clinical; owned by openChart) (ruling 11). Confidential-communication instructions are in the registry and carried in the link snapshot, and openChart, and anything it drives (for example HealthLinc), must honor them for every patient communication it originates (rulings 10, 27).
- openPractice is the system of record for the administrative appointment. The EHR owns the encounter and keeps an appointment reference if needed. The 1.0 contracts carry no appointment operations; appointment events are a future MINOR version (ruling 31). In the MVP openPractice books appointments minimally: book, reschedule, cancel, no-show and walk-in, with no reminders, waitlist or self-scheduling (ruling 115).
- openChart publishes only confirmed diagnoses to openPractice as billable facts; if a published diagnosis is later refuted, a retraction is sent (ruling 87). After a patient link is revoked or superseded, retractions of facts already published under it are delivered, as pointers only; no new facts and no reads are served (ruling 88). While an EHR reports no billable-facts capability, manual charge entry is allowed for a linked patient, marked manual, with provenance (ruling 21). The EHR supplies coded facts and a named human biller confirms them; staff enter codes when there are none, and openPractice never infers codes (ruling 116).
- HealthLinc's in-app AI is a coach, not a doctor; clinical AI doctors live in medXfactory (ruling 51). The coach gives general information only, and anything individualized is handed to medXfactory's medical AI or to the doctor (ruling 54). The coach is HealthLinc's own and is never gated by the practice, while non-urgent medical-AI consults sit behind the practice's access check (ruling 52).
- The medical AI's exchanges with a patient are recorded in the chart through medXfactory's own governed path (ruling 53). Its records reach openChart through MedxEHR, which relays them with receipts; medXfactory does not write to openChart directly, and openPractice is never in that path (rulings 53, 70). openChart owns one neutral conversation-retrieval contract, which HealthLinc and medXfactory implement (ruling 69).

**Rulings that bear on it:** 80 sets who and what may add to a patient's chart, what each addition records, and when an addition counts as a decision; 151 has the practitioner sign off any medical decision as a logged-in user, with openWallet and openSig signing and validating the entry. Rulings 152, 157 and 186 settle how that applies: no decision counts until a practitioner signs it off in openChart, "in version 1"; openChart records no real decision until openWallet and openSig can do their part, though it can be built and tested against stand-ins; and until a practitioner signs, an AI only ever makes recommendations, ruling 151 governing over clause 11 of ruling 80. Rulings 153 to 156, 158 to 160, 183, 187 to 190 and 194 settle further details for openChart, openWallet and openSig. All of them concern the chart, in openChart and the companion systems, and none says anything about openPractice's routing record or billable facts; OT-9 notes what ruling 152 means for when billable facts can arrive. The index reproduces ruling 80's approved wording in its public form.

**Changed from v1.** New. Version 1 has no decision on openPractice's boundary with the clinical systems.

**Open tensions:** OT-9.

---

## FD-15. The billing and accounting boundary (new)

**v2 status:** new. Rulings 2, 5, 6, 7, 8, 23, 24, 25, 26, 28, 30, 32, 123, 124, 125, 129, 139 to 141, 150, 163 to 165, 171, 180 and 193.

**Decision.**

- The accounting contract of `define-mvp-practice-foundation` owns the posting mechanics: posting identity and idempotency, batches and their freeze, release, retries, the reconciliation report, the posting modes and the BAA gate. Change `define-customer-financial-data-boundaries` owns customer and party roles, routing by offering, obligation ownership and purpose-bound business views. Membership and retail obligations are owned by that change's routes, and the accounting contract never creates a second collectible balance for them (ruling 26; ruling 24 as revised by ruling 26).
- The MVP default and recommended setup is `summary-journal`: summarized, balanced postings to payer-class control accounts, with patient-level receivable detail kept in openPractice. `customer-detail` is a per-destination opt-in, enabled only after that destination's BAA gate and only before its first release. openPractice's subledger total equals the destination's control account, per payer class (ruling 28). After the first release any mode change is refused until the owner ratifies a continuity procedure, and a BAA lapse halts delivery with no silent conversion (ruling 7). A practice-operated destination satisfies the BAA gate with an attestation naming the operator (ruling 6).
- `summary-journal` carries no customer records or names. In `customer-detail` mode only, the accounting customers are the guarantor account and each payer (rulings 2, 28), and a medical-billing customer may carry its name and billing address or contact once the destination's BAA gate is satisfied. Chart notes, diagnoses, medications, results, full claim payloads, EHR medical-record numbers and service-revealing reason categories are always excluded (ruling 25).
- One Party with roles. "Customer" is a party role only for membership and retail buyers; a medical-billing accounting customer is derived from the guarantor or payer role (ruling 32). When two records turn out to be the same person, they are linked, not merged, "in version 1": the link is reversible, and a full merge, with its undo, would be its own later change (ruling 150).
- Patient balances belong to the guarantor account. Every ledger entry moves money between two fixed buckets, so it always balances (ruling 124); the ledger starts with 18 buckets and 49 kinds of money movement, with the accountant confirming how each kind maps to the books (ruling 163). Reason codes are a fixed list openPractice ships, on two axes, kind of money movement and service category; a service-category field is added to the accounting contract if the gap is confirmed (ruling 125). Ruling 164 treats the gap as confirmed and puts the amendment into the critique corrections now; until the contract is amended, openPractice refuses to send the summaries that need the service category, a second payer class for transfers between payers or three new labels, which blocks accounting go-live (ruling 164). When a patient has a second insurance, the patient's share waits for it (ruling 171). Money a patient pays toward an insured visit before the payer answers waits for at most 60 days before a credit case opens, and the wait ends sooner when the payer answers, the claim is dropped or the visit becomes self-pay; counsel confirms the 60 days, and money from a cancelled or missed visit never waits (ruling 180).
- openPractice reconciles deposits only, reading revenue bank lines from the destination with statement upload as fallback; the accounting system owns bank balances and the full reconciliation (ruling 5). Full bank account and routing numbers are stored encrypted, visible only to owner and billing-admin roles, masked elsewhere, every view audited, and never in logs, errors, exports, postings or keys (ruling 23). The owner's part of that power is held by the practice manager role, since a recorded owner grants nothing by itself (ruling 193).
- Net revenue is the constrained expected entitlement, estimated at the date of service and re-estimated at each close; the gross charge is a memo or gross line carried with its deduction. Billing, adjudication, cash and disputes are evidence events that update the estimate, not revenue triggers. A payer with no history gets a conservative constrained estimate, with no gross-then-adjust fallback. The practice's accountant approves the reporting basis, estimator, classification, materiality and reopening policy before go-live (ruling 30, which supersedes ruling 22). The full estimate record is built now, with a simple first method the accountant approves before accounting goes live (ruling 123). A claim counts as billed in the books when the payer or clearinghouse accepts it, not when it is sent, and a rejection after acceptance reverses the move; the accountant confirms (ruling 165).
- That accountant is engaged from outside the practice, is neither an owner nor an employee, and is a licensed CPA checked against the state board's public record, with an outside-accountant entry on the Roster (rulings 139 to 141; FD-5). What makes a write-off material is an amount each practice sets at setup, confirmed by its accountant (ruling 129).
- The MVP calls ERPNext only through its REST API, same-site or remote, with no in-process calls; in-process use stays conditional on counsel (ruling 8).

**Changed from v1.** New. Version 1 touches the books only in FD-2 (where expected payment comes from) and FD-3 (period close and export-batch freeze).

**Open tensions:** OT-5, OT-6 and OT-8 touch rulings 30 and 123; rulings 123 and 126 answer OT-8.

---

## Open tensions for the owner

Each item names both sources. This draft does not resolve any of them. Where a change's tension table already records the item, that table is named; its proposed disposition is the change's, not a ruling.

| # | Version 1, or one ruling | Pulls against | Recorded in |
|---|---|---|---|
| OT-1 | **FD-1 "Why":** "A fixed frame deletes that entire class: no routing engines, …" Ruling 38 changes FD-1's frame and is silent on this sentence. | **Rulings 34, 44, 51 and 71:** openPractice's routing rules apply the proposed category and urgency, with ruling 51's conservative defaults. No ruling says the rules are fixed rather than authored by the practice. | `define-patient-gateway-and-escalation` T-2 (conscious exception proposed, mitigated by design call DC-17's fixed rule set) |
| OT-2 | **FD-1 "Why":** "no approval ladders, no RBAC matrix". | **Ruling 37:** an autonomy ladder per action type, opted up by the practice, with per-agent grants. | `define-actors-agents-and-work-inbox` T-6 ("Owner to confirm at ratification") |
| OT-3 | **FD-5:** "Replace approval with reversibility, visibility, and delay"; "The second human does not exist; approval queues rot within a month." | **Ruling 37:** "Every action type starts at propose", so agent work waits for a person until the practice opts the action type up. | `define-actors-agents-and-work-inbox` T-3 (conscious exception) |
| OT-4 | **FD-5 tier 2:** "Do + notify — reversible, surfaced in Today/close." | **Ruling 12:** `link_existing` and identity-review resolution are tier 2, read with **ruling 33**'s "where a tier calls for an approval, release decision or attestation". The changes treat these, and the gateway's link and proposal confirmations, as explicit human confirmations. No ruling says whether tier 2 here means do-and-notify or an explicit confirmation; the critique (D.2) asks this version to establish "tier-2 human confirmations". | `define-mvp-practice-foundation` T-17 (conscious exception, citing ruling 12); `define-patient-gateway-and-escalation` T-4 (conscious exception proposed) |
| OT-5 | **FD-5:** tier 4 is "true approval, reserved for the constitutionally irreversible few"; "The second human does not exist." | **Ruling 30** (the accountant approves the policy items before go-live), **ruling 41** (the medical director approves the hold policy), **ruling 107** (two-person rules with one-person alternatives, as ruling 98 does; no self-approval of accountant or counsel sign-offs), **ruling 116** (a named biller confirms coded facts), **ruling 119** (a named signer releases each claim batch), **rulings 139 and 141** (the accountant sign-off needs an outside accountant on the Roster) **ruling 144** (without an outside accountant, a sole staff member's act on their own record waits) and **rulings 173, 174, 181 and 182** (each requires a named human, or for ruling 181's escalation a person, for the acts it names; FD-5). | `define-patient-gateway-and-escalation` T-4, for the hold policy (conscious exception proposed); `define-revenue-cycle-core` T-4 for ruling 119 and T-6 for ruling 116 (conscious exceptions) |
| OT-6 | **FD-3:** "Month-end is printing, not a procedure"; "the practice never sees a lock." | **Ruling 30:** revenue "re-estimated at each close", with a "reopening policy" the accountant approves; **ruling 4:** postings move to the first open date when a period is closed. `define-mvp-practice-foundation` makes the close re-estimate automatic with no sign-off (its governance tiers); no ruling says whether that satisfies FD-3. | `define-mvp-practice-foundation`, "Verification dispositions (fix round 4)", finding M-B "close-snapshot approval versus FD-3", disposed by AI-12: close re-estimates post automatically, and only estimator versions and D8 items need approval. The disposition is the change's, not a ruling. |
| OT-7 | **Ruling 33:** "Automation stays propose-only." | **Ruling 37** (an opted-in agent may act with notice or after a delay), **ruling 45** (Hermes answers patients without a human) and **ruling 120** (exactly matched remittance lines post by themselves, with notice and undo). Ruling 37 says its ladder reconciles with FD-5 and BAB-17, the requirement that carries ruling 33, and `define-actors-agents-and-work-inbox` makes raising a rung the same act as ruling 33's agent-signing opt-in (its design call D-5). No ruling says that ruling 33's "propose-only" is read that way. | For rulings 37 and 45, not recorded as a tension (`define-actors-agents-and-work-inbox` D-5 is that change's reconciliation). For ruling 120, `define-revenue-cycle-core` T-3 (recorded: ruling 120 is the later and more specific ruling) |
| OT-8 | **FD-2 "Changes":** "the expectation engine leaves the beta's critical path." | **Ruling 30:** net revenue is the constrained expected entitlement, estimated at the date of service, and the practice's accountant approves the estimator before go-live. Accounting is a beta slice (Slice 4 of scope-and-sequencing; Feature 3 of `define-mvp-practice-foundation`), and that change's AI-08 proves that the gross-to-net allowance equals the sum of openPractice's current estimates, so an estimator is on the beta's critical path. No ruling said whether that estimator is the expectation engine FD-2 took off it. **Answered by rulings 123 and 126**, for the owner to confirm at ratification: the estimate record is built now, with a simple first method the accountant approves before accounting goes live (ruling 123), and payer expectations come from the estimator, not loaded fee schedules (ruling 126). | `define-revenue-cycle-core` T-5 (resolved for the first method by ruling 126) |
| OT-9 | **Ruling 53**, in its table row: the medical AI's exchanges "never pass through openPractice, which holds no clinical content." | **Rulings 21, 87 and 88:** billable facts reach openPractice from a linked EHR, confirmed diagnoses among them (ruling 87); retractions of published facts are still delivered after a link ends (ruling 88); and charges are entered manually while an EHR reports no billable-facts capability (ruling 21). Ruling 116 adds that the EHR supplies coded facts, a named human biller confirms them, staff enter codes when there are none, and openPractice never infers codes. `define-mvp-practice-foundation` treats billable facts as references and as copies of claims as submitted, with codings that carry a system and a code only (its ownership matrix, its Constitution Check for Principle IV, and its Decision 5); that is the change's own design, not a ruling. The question for the owner is what "no clinical content" means once coded billable facts arrive. It matters now: ruling 110 puts the revenue-cycle core next, ruling 116 sets how codes reach it, and claims carry diagnosis codes. Ruling 152 bears on when such facts can arrive: a diagnosis is a decision under ruling 80, and openChart records no real decision until openWallet and openSig can do their part. | Not recorded as a tension in any change |

## Rulings not folded into a decision

Each is in the [Owner Rulings Index](openpractice-owner-rulings-index.md).

- **Superseded:** 3 (by 25) and 22 (by 30).
- **Packaging and process:** 1, 40, 47, 48, 49, 64, 72, 75, 85, 89 and 93 (its consent check is cited in FD-12); 108 (this draft and the index), 109 (see "Ratification" below), 110 (write the revenue-cycle core next and pause perimeter work; its scope and decisions will follow as their own list), 111 and 112 (the revenue-cycle core is one change with its own ordered multi-feature hand-off).
- **Contract detail:** 13, 14, 15, 16, 18, 19, 20, 103 (one money format for every contract), 117 (one Frappe app with a module per area; adapters as separate packages) and 127 (eligibility and prior authorization recorded by hand as evidence; electronic checks arrive with the clearinghouse).
- **Other systems only** (openChart, openWallet, openSig, medXfactory, MedxEHR or HealthLinc): 55, 56, 57, 58, 59, 60, 61, 62, 63, 65, 66, 67, 73, 74, 76, 77, 78 (also noted under FD-11), 79, 81, 82, 83, 84, 86, 90, 91, 92, 94, 95, 96 and 98 (cited through rulings 107 and 146 in FD-5), 80 and 151 (additions to a patient's chart; noted under FD-14, with ruling 80's public wording in the index), and 152 to 160, 183, 186 to 190 and 194 (how a practitioner's sign-off and additions to the chart work in openChart, openWallet and openSig; noted under FD-14).

## Relationships

Version 2 of [Framing Decisions](openpractice-framing-decisions.md), which stays unchanged as history. Drafted under ruling 108, in answer to item D.2 of the critique of 2026-11-15, `ideation/review/openpractice-spec-critique-2026-11-15.md`; ruling 111 adds that file to this repository through the critique corrections, `opensoft/openPractice-spec#8`. On 2026-11-15 that pull request was an open draft and the path was not yet on `main`, so it is given as text, not as a link. Each ruling is indexed in the [Owner Rulings Index](openpractice-owner-rulings-index.md), and each change's `design.md` table remains the source of truth. Derived, like version 1, from the [Feat Review Synthesis](openpractice-feat-review-synthesis.md).

## Ratification (pending)

**Version 2 claims no ratification.** Ratification is the owner's to do (ruling 109). Until he completes this block, version 1 and its ratification record of 2026-08-27 stand. Under ruling 109, each OpenSpec change cited here is ratified first and archived after its feature lands; none is promoted to a canonical spec before building. Ruling 182 also leaves twelve small recording actions for the owner to rule on at ratification (FD-5).

| # | Decision | v2 status | Owner's ruling |
|---|---|---|---|
| FD-1 | Staff-count frame | amended (rulings 38, 107) | pending |
| FD-2 | Expectation source | amended (rulings 123, 126) | pending |
| FD-3 | Period close | amended (rulings 4, 8, 26, 30) | pending |
| FD-4 | Estimates and GFE | amended (rulings 113, 114, 121, 122, 126, 130, 172, 179; with 166, 175, 177, 193) | pending |
| FD-5 | Governance primitive | amended (rulings 9, 12, 33, 37, 119, 120, 129, 150, 167 to 171, 173, 181; with 30, 41, 105, 107, 116, 123, 128, 134 to 137, 139 to 142, 144 to 149, 151, 152, 157, 161, 163, 165, 172, 174, 180, 182, 184 to 186, 191 to 193) | pending |
| FD-6 | Differentiator statement | unchanged | pending |
| FD-7 | Spine ownership | unchanged | pending |
| FD-8 | First external milestone | unchanged | pending |
| FD-9 | Cutover | unchanged | pending |
| FD-10 | Channels | amended (ruling 36) | pending |
| FD-11 | Licensed IP | unchanged | pending |
| FD-12 | Agent operation | new | pending |
| FD-13 | Safety routing | new | pending |
| FD-14 | The clinical boundary | new | pending |
| FD-15 | The billing and accounting boundary | new | pending |
| OT-1 to OT-9 | Open tensions (OT-8 answered by rulings 123 and 126, for confirmation) | for the owner's disposition | pending |

- Ratified by: pending
- Date: pending
- Revision ratified (commit of this file): pending
