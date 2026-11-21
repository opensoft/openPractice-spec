# OpenSpec Architecture Critique — Round 2 (Post-Corrections, Readiness & Revenue Cycle Core)

Status: brainstorm
Kind: review
Summary: Second-pass architectural and double-entry accounting critique across framing-decisions-v2 (`ca4ad79`), critique-2026-11-15-corrections (`f3bed0a`), define-practice-readiness (`d4724e1`), and define-revenue-cycle-core (`1a1b51f`), enumerating twelve concrete findings (`R2-01`–`R2-12`) for remediation.
Topics: openpractice-spec-review, architecture-critique, openspec, revenue-cycle, accounting-subledger
Captured: 2026-11-15

## Scope & Heads Reviewed

Reviewed against `opensoft/openPractice-spec`:

- `origin/main` at `0a82fc2`
- `origin/docs/framing-decisions-v2` at `ca4ad79` (`ideation/review/openpractice-framing-decisions-v2.md`, `ideation/review/openpractice-owner-rulings-index.md`)
- `origin/change/critique-2026-11-15-corrections` at `f3bed0a` (`PR-15` transitive same-person party groups, `BAB-18` canonical money representation, `AH-02`/`ER-12` ZDR + segment split, `MA-04`/`MA-07` passkey freshness + transaction-bound signing, cross-branch reference cleanup)
- `origin/change/define-practice-readiness` at `d4724e1` (`readiness-checklist` `RC-01`–`RC-07`, `policy-starter-kit` `SK-01`–`SK-06`, `signer-roles` `SR-01`–`SR-04`)
- `origin/change/define-revenue-cycle-core` at `1a1b51f` (15 capability specs: `TH`, `AP`, `SC`, `GF`, `EV`, `EG`, `CG`, `CL`, `CP`, `RM`, `SL`, `RT`, `RE`, `PY`, `PS`, plus `contracts/openpractice-thread-1.0.schema.json` and Fix Round 1)

### Baseline Verification

1. **Round-1 critique items (`ideation/review/openpractice-spec-critique-2026-11-15.md`):** All Round-1 items are substantively addressed across the four branches above.
2. **Contract schema digests:** All seven `openpractice-*.schema.json` files across `define-mvp-practice-foundation`, `define-actors-agents-and-work-inbox`, `define-patient-gateway-and-escalation`, and `define-revenue-cycle-core` parse cleanly and match their `SHA256SUMS` digests byte-for-byte.
3. **Branch mergeability:** All six pairwise `git merge-tree --write-tree` checks across the four active branches exit `0` with zero file-level merge conflicts.

---

## Round-2 Findings (`R2-01`–`R2-12`)

### A. Subledger & Double-Entry Accounting Breaks (`define-revenue-cycle-core`)

#### `R2-01` — Insured check-in/checkout copays break `PY-03`, `PY-04`, and `PY-06`
- **Severity:** High (occurs daily in ambulatory clinics)
- **Target:** `define-revenue-cycle-core` — `specs/patient-payments-and-refunds/spec.md` (`PY-01`, `PY-03`, `PY-04`, `PY-06`), `specs/charge-capture/spec.md` (`CG-09`), `specs/remittance-posting/spec.md` (`RM-07`)
- **Problem:**
  1. When an insured patient pays a copay (e.g., `30.00`) at check-in before the visit, `PY-03` books `prepayment` (`Dr cash_clearing / Cr contract_liability`) and requires it to *"be applied with `prepayment_applied` when that service's charge is finalized."*
  2. However, when an insured charge is finalized (`CG-09`), 100% of the gross charge posts via `service_insured` (`Dr unbilled_receivable / Cr gross_service_revenue`). **`patient_receivable` on that visit is `0.00` until the payer's remittance posts `patient_share_transfer` weeks later (`RM-07`).**
  3. Applying `prepayment_applied` (`Dr contract_liability / Cr patient_receivable`) at `CG-09` drives `patient_receivable` negative (`-30.00`) while `unbilled_receivable` still carries the full gross charge (`200.00`).
  4. Conversely, if an insured check-in or checkout copay sits in `unapplied_credit` waiting 21–60 days for payer adjudication:
     - `PY-04`'s oldest-undisputed-first FIFO rule (and agents opted up to `act_with_notice` for `payment.allocate`) will sweep today's visit copay onto an older visit's balance; and
     - `PY-06` will open false-positive `credit_balance` cases (*"due at once"* before counsel clocks exist) for routine copays waiting on open `claimed` threads.
- **Required Fix:**
  - Allow a patient receipt (`PY-01`) or `prepayment_to_credit` (`PY-03`) to be **earmarked to an open visit thread**.
  - On an insured visit, keep a check-in `prepayment` in `contract_liability` until the service is finalized (`CG-09`), then move it via `prepayment_to_credit` into **thread-earmarked `unapplied_credit`** (not `patient_receivable`), and trigger automatic `credit_applied` when `RM-07` posts `patient_share_transfer` on that thread (leaving any excess over actual patient responsibility unearmarked so `PY-06` opens a `credit_balance` case then).
  - Require `PY-04` automatic/FIFO allocation to skip thread-earmarked credits except onto their earmarked thread, and require `PY-06` not to start a `credit_balance` case while the credit is earmarked to an open, unadjudicated thread (unless the thread cancels or closes with zero patient share).

---

#### `R2-02` — Secondary / crossover insurance claims have no legal ledger entry under `SL-01`, `RT-02`, `CL-12`, and `RM-07`
- **Severity:** High
- **Target:** `define-revenue-cycle-core` — `specs/money-subledger/spec.md` (`SL-01`), `specs/reason-taxonomy/spec.md` (`RT-02`), `specs/claim-lifecycle/spec.md` (`CL-02`, `CL-12`), `specs/remittance-posting/spec.md` (`RM-07`)
- **Problem:**
  1. `CL-02` requires secondary and tertiary claims to work now via the manual path (`CP-04`) and requires the data model not to assume a single payer or coverage position.
  2. Trace a `200.00` Medicare primary + Medigap secondary visit:
     - `CG-09`: `Dr unbilled_receivable 200.00 / Cr gross_service_revenue 200.00`.
     - `CL-12` (primary accepted): `Dr payer_receivable[Medicare] 200.00 / Cr unbilled_receivable 200.00` (`unbilled_receivable` is now `0.00`).
     - `RM-07` (Medicare allows `120.00`, pays `96.00`, coinsurance `24.00`): posts `contractual_adjustment` `80.00` and `payer_payment` `96.00`. Because a secondary coverage position exists, `RM-07` (`CR-54`) withholds `patient_share_transfer`, leaving **`24.00` sitting on `payer_receivable[Medicare]`** even though Medicare has finished adjudicating.
  3. When the secondary claim to Medigap is accepted (`CL-12` / `CP-04`):
     - `claim_accepted` (`Dr payer_receivable / Cr unbilled_receivable`) cannot be used because `unbilled_receivable` is already `0.00` (posting it drives `unbilled_receivable` negative); and
     - `SL-01` forbids same-bucket transfers for every kind except `guarantor_transfer` on `patient_receivable`, so **no kind in `RT-02` can move the remaining `24.00` from `payer_receivable[Medicare]` to `payer_receivable[Medigap]`** (nor from `patient_receivable` to `payer_receivable[Medigap]` if patient share was transferred earlier).
- **Required Fix:**
  - Add explicit coordination-of-benefits / coverage-transfer reason kind(s) to `RT-02`:
    - `payer_cob_transfer` (`Dr payer_receivable[next_coverage] / Cr payer_receivable[prior_coverage]`, neutral label `receivable_transfer`) — and amend `SL-01` to permit same-bucket `payer_receivable` counterparty-coverage transfers alongside `guarantor_transfer`.
    - `patient_to_payer_receivable_transfer` (`Dr payer_receivable / Cr patient_receivable`, neutral label `receivable_transfer`) for late secondary discovery or billing a secondary after `patient_share_transfer` already ran.
  - Amend `CL-12` so `claim_accepted` moves from `unbilled_receivable` only when `unbilled_receivable` holds the unclaimed balance (initial claim), and moves the remaining open balance via `payer_cob_transfer` when advancing to a later coverage position after `RM-07`.

---

#### `R2-03` — Dead-end receivables on non-billable payer denials, payer credit losses, `EG-05`, and `GF-11`
- **Severity:** High
- **Target:** `define-revenue-cycle-core` — `specs/reason-taxonomy/spec.md` (`RT-02`, `RT-07`, `RT-09`, `RT-10`), `specs/remittance-posting/spec.md` (`RM-07`), `specs/participation-gate/spec.md` (`EG-05`), `specs/good-faith-estimate/spec.md` (`GF-11`), `specs/patient-balance-and-statements/spec.md` (`PS-11`)
- **Problem:**
  1. **Payer write-offs exceeding allowance & payer credit losses:** In `RT-02`, every non-allowance write-off and credit-loss kind (`small_balance_write_off`, `price_concession_write_off`, `charity_care`, `credit_loss_write_off`, `credit_loss_direct_write_off`) credits **`patient_receivable` only**. `payer_receivable` can only be written off via `payer_write_off_against_allowance`. When a payer denies a claim on provider-liable grounds (e.g., CO-29 timely filing or missing prior authorization, where contract/law forbids shifting the balance to `patient_receivable`) and the denied balance exceeds that line's remaining `gross_to_net_allowance`, or when a payer defaults (`RT-07` credit loss), `RT-02` has no kind to clear the remaining `payer_receivable`.
  2. **Insured <-> self-pay conversions (`EG-05` and `GF-11`):** `unbilled_receivable` has only one credit exit in `RT-02` (`claim_accepted`). When `EG-05` tells staff to convert an unclaimed insured visit (`unbilled_receivable`) to self-pay or write it off under `PS-11`, or when `GF-11` tells staff to bill late-discovered insurance on a self-pay visit (`patient_receivable`), no `RT-02` entry moves between `unbilled_receivable` and `patient_receivable`.
- **Required Fix:**
  - For payer denials / defaults: add `payer_price_concession_write_off` (`Dr revenue_deduction / Cr payer_receivable`, label `write_off`) and payer credit-loss entries (`payer_credit_loss_write_off` / `payer_credit_loss_direct_write_off` — or parameterize credit-loss entries across `patient_receivable` and `payer_receivable` with distinct kinds per `RT-01`), applying `RT-09`'s allowance-first rule symmetrically to payer write-offs so excess over `gross_to_net_allowance` reaches `revenue_deduction` without touching `patient_receivable`.
  - For `EG-05` and `GF-11`: either specify explicitly that switching between insured and self-pay before/after finalization works by **reversing the charge line (`CG-08` / `SL-05`) and finalizing a replacement charge line** under the new billing route (and reversing any `self_pay_discount`), or add explicit reclassification kinds in `RT-02`.

---

#### `R2-04` — Bounced checks and chargebacks (`PY-09`) corrupt balances when funds are in `contract_liability`, `unapplied_credit`, or `unidentified_receipts`
- **Severity:** High
- **Target:** `define-revenue-cycle-core` — `specs/patient-payments-and-refunds/spec.md` (`PY-09`), `specs/reason-taxonomy/spec.md` (`RT-02`)
- **Problem:**
  - `PY-09` forbids reversing the original payment or allocation and hardwires `chargeback` and `returned_item` (`RT-02`) to `Dr patient_receivable / Cr cash_clearing`.
  - If a `500.00` check or card payment for an upcoming procedure (`prepayment` in `contract_liability`) bounces or is charged back before the visit, or if a `100.00` check sits wholly or partially in `unapplied_credit` (or `unidentified_receipts`, or was moved to `unapplied_credit` after a `CG-08` charge reversal under `PY-03`), debiting `patient_receivable` creates a **phantom `500.00` receivable for a service never rendered** while leaving a **spendable/refundable `500.00` `contract_liability` or `unapplied_credit`** on the guarantor's account.
- **Required Fix:**
  - Keep `PY-09`'s append-only history (never erase the original receipt), but trace where the dishonored payment's dollars currently sit:
    - For any portion currently in `contract_liability`, `unapplied_credit`, or `unidentified_receipts`, debit that liability/credit bucket against `cash_clearing` (via dedicated Dishonored Prepayment / Dishonored Credit / Dishonored Unidentified Receipt kinds in `RT-02`, or by first reversing the allocation/credit creation under `SL-05` and dishonoring from the holding bucket).
    - Only the portion that satisfied `patient_receivable` should reopen `patient_receivable`.

---

#### `R2-05` — `customer-detail` mode mismatches between `AI-11`/`AI-13` (`define-mvp-practice-foundation`) and `define-revenue-cycle-core`
- **Severity:** Medium
- **Target:** `define-revenue-cycle-core` (`design.md` patch table `PA-*`, `PS-05`, `PY-03`, `PY-09`, `PS-12`) and `define-mvp-practice-foundation` (`specs/accounting-integration/spec.md` `AI-11`, `AI-13`, `contracts/openpractice-accounting-integration-1.0.schema.json`)
- **Problem:**
  1. **Check-in payments:** `AI-13` posts a pre-invoice collect-at-visit payment as `payment.post` (`unapplied_payment` on the guarantor account), whereas `PY-03` requires money collected before the service is given to sit in `contract_liability` (`prepayment`), distinct from `unapplied_credit`.
  2. **Chargebacks & returned items:** `AI-13` represents a chargeback or returned item as `reversal.post` of the original payment, whereas `PY-09` forbids reversing the original payment and posts new `chargeback` / `returned_item` entries.
  3. **Invoice due date vs. statement due date:** `PS-05` states that a statement's due date (`statement_issue_date + terms`) *"SHALL be the due date that `customer-detail` postings carry (AI-11)"*, whereas `AI-11` emits `invoice.post` at **charge finalization** (`CG-09`), before any statement has been issued (and even when no statement will ever be issued).
  4. **Guarantor transfer (`PS-12`):** `customer-detail` mode (`AI-13`) has no specified posting pattern for `guarantor_transfer` moving an open balance from Customer A to Customer B without inflating revenue.
- **Required Fix:**
  - Add a patch entry (`PA-8`) in `define-revenue-cycle-core/design.md` (and apply it to `AI-11`/`AI-13` when `PA-1`/`PA-7` land) aligning `AI-13` on `prepayment` vs. `unapplied_payment`, specifying how `PY-09` maps to `customer-detail` postings, clarifying `AI-11`/`PS-05` (`invoice_date + statement_terms` at invoice posting vs. `statement_issue_date + statement_terms` on statements), and defining `guarantor_transfer` in `customer-detail` mode (`journal.post` or credit-note/debit-note pair with zero net revenue impact).

---

### B. Thread Contract & State-Machine Gaps (`define-revenue-cycle-core`)

#### `R2-06` — Circular `amount_at_risk` definition between `TH-03` and `openpractice-thread-1.0.schema.json`
- **Severity:** High
- **Target:** `define-revenue-cycle-core` — `specs/encounter-thread/spec.md` (`TH-03`), `contracts/openpractice-thread-1.0.schema.json` (`$defs.amountAtRisk`, `SHA256SUMS`)
- **Problem:**
  - `TH-03` states: *"derived from ledger entries (SL-08) **by the rule the canonical schema states for each station (design call CR-6, PROPOSED)**."*
  - `openpractice-thread-1.0.schema.json` (`$defs.amountAtRisk.description`) states: *"Derived from ledger entries **by the rule for the thread's station (TH-03)**; never stored as a balance."*
  - Neither file contains the per-station derivation table. Implementers have to guess:
    - Whether `amount_at_risk` is omitted/`null` or `0.00` at `booked`, `ready`, `arrived`, and `seen` (where `CG-03` forbids ledger entries).
    - Whether an insured thread at `charged` or `claimed` displays **gross** `unbilled_receivable` / `payer_receivable` (`200.00`) or **constrained expected entitlement** (`114.00`, i.e., gross receivable less remaining `gross_to_net_allowance`).
    - What `amount_at_risk` displays at `remitted` (when both a secondary payer and guarantor or appeal are open) and at `banked` (when `cash_clearing > 0` awaits `deposit_explained` vs. after deposit explanation).
- **Required Fix:**
  - Put the explicit, station-by-station derivation table in `TH-03` (and mirror it concisely in `$defs.amountAtRisk.description`, updating `SHA256SUMS`).

---

#### `R2-07` — Missing legal transitions in `TH-04` (`ready -> charged` for no-show fees; `banked` reopenings)
- **Severity:** Medium
- **Target:** `define-revenue-cycle-core` — `specs/encounter-thread/spec.md` (`TH-04`), `specs/administrative-appointment/spec.md` (`AP-04`), `specs/patient-payments-and-refunds/spec.md` (`PY-09`)
- **Problem:**
  1. **`ready -> charged` is missing:** `TH-04` allows `booked -> charged` (*"A no-show fee was charged"*) and `booked, ready -> closed` (*"Cancelled, or a no-show with no fee"*), **but omits `ready -> charged`**. Because `TH-04` refuses unlisted moves, any appointment that passed readiness (`booked -> ready`) and then no-showed cannot be charged a missed-appointment fee (`AP-04`).
  2. **`banked` reopenings are too narrow:** `TH-04` allows `closed -> any earlier station` for chargebacks/recoupments/late coverage, and `remitted, banked -> charged` *only* for *"Coverage was found late, so a claim will be built (GF-11)."* If a payment bounces or charges back (`PY-09`), a payer takes money back (`RM-09`), or an EHR clinical retraction (`EB-15`) arrives while the thread is still at `banked` (before advancing to `closed`), `TH-04` has no legal transition for it.
- **Required Fix:**
  - Change `booked | charged` in `TH-04` to `booked, ready | charged`.
  - Expand transitions out of `banked` (e.g., `banked -> charged` or `banked -> remitted`) to cover chargebacks, returned items, takebacks/recoupments, and post-payment charge corrections alongside `GF-11`.

---

### C. Governance, Readiness, Roster & Privacy Gaps

#### `R2-08` — Cross-PR go-live blockers in `AM-01`/`AM-02` (`RI-51` / `T-16`) and unapplied `PA-1`–`PA-7`
- **Severity:** High
- **Target:** `define-actors-agents-and-work-inbox` (`specs/actor-model/spec.md` `AM-01`, `AM-02`) and `define-mvp-practice-foundation` (`PA-1`, `PA-7`)
- **Problem:**
  1. **Missing external actor types & accountant recipe (`AM-01` / `AM-02`):** Even with all four active branches merged, `AM-01` only recognizes human roster actor types `on-site staff`, `remote VA`, `nurse`, `clinician`, `medical director`, and `controller or office manager`, and `AM-02` has no `accountant` recipe. Neither an outsourced RCM biller (`FD-1`) nor an outside CPA (`SR-04`, `RE-02`, `RE-09`, `PS-11`, `AI-20`) is `on-site staff` or a `remote VA`. As noted in `RI-51` and `T-16`, every accountant sign-off fails closed until `AM-01`/`AM-02` can hold the practice's external accountant (and external biller).
  2. **Unapplied `PA-1`–`PA-7` patches:** `define-revenue-cycle-core/design.md` lists `PA-1`–`PA-7` against sibling changes (`service_category` on summary lines, `write_off_against_allowance`, `bank_debit_clearing_release`, `openpractice-agent-operations` 1.1 action types, `PH-01`/`PH-06`/`PH-07` pointers, `RC-02`/`SK-01` starter-kit item 15), none of which is applied yet in the target change specs/schemas. Without `PA-1`, `RT-04` refuses 100% of `summary-journal` revenue/deduction batches.
- **Required Fix:**
  - Extend `AM-01` and `AM-02` to include external professional actor type(s) (`external_biller`, `external_accountant`, or `external_vendor_professional`) and the `accountant` recipe (bounded to D8 policy, estimator-version, credit-loss/charity-care election, and accounting read/sign-off authority, with zero clinical or routine billing mutation authority), proposing the concrete ruling text if Decisions 140/141 remain open.
  - Apply `PA-1`–`PA-7` (plus `PA-8` from `R2-05`) across the target changes and update `SHA256SUMS` once `define-revenue-cycle-core` merges or in a coordinated integration branch.

---

#### `R2-09` — `SR-04` state-board CPA license check ambiguity & common-name false-positive lockout
- **Severity:** Medium
- **Target:** `define-practice-readiness` — `specs/signer-roles/spec.md` (`SR-04`), `design.md` (`RD-11`, `RD-13`)
- **Problem:**
  1. **Automated vs. recorded license verification:** `SR-04` requires *"a check of the accountant's licence against the licensing board's public record... A sign-off whose licence cannot be checked there SHALL be refused (fail closed)."* US state boards of accountancy (55 jurisdictions) do not provide a uniform, public, machine-queryable API; if an implementer reads `SR-04` as requiring an automated live query, every CPA sign-off will fail closed.
  2. **Hard string-match lockout on common names:** `SR-04` states that *"a match of the licensee's name, as the board's public record gives it, against the parties behind the practice's roster entries, other than the accountant's own engagement entry, and against its recorded owners. Any match SHALL refuse the sign-off."* If a practice employs a nurse or MA named "John Smith" or "Sarah Kim" and engages an unrelated external CPA with the same legal name, `SR-04` permanently blocks accounting go-live with no resolution path.
- **Required Fix:**
  - Clarify in `SR-04` that the licensing-board check is satisfied by a **structured verification record** (licensing jurisdiction/board, license number, licensee legal name as listed, status/expiry date, lookup date, public source reference/URL, and the authorized verifier—matching `EG-01` and `AI-14`), without requiring an automated state-board scraper.
  - Change a name match against an existing roster party or owner from a permanent unappealable refusal to a **fail-closed hold resolved only by a tier-4 conflict-of-interest non-identity determination** linked to `PR-15`'s explicit *not-the-same-person* record (citing independent identifiers such as license number / state / business TIN, audited and surfaced on `RC-06`), while still unconditionally refusing when the party is linked as the *same person* in `PR-15`.

---

#### `R2-10` — `PS-06` statement privacy leak when a patient restricts disclosure *against* their guarantor
- **Severity:** Medium (HIPAA 45 CFR § 164.522(b) / state confidential-care safety)
- **Target:** `define-revenue-cycle-core` — `specs/patient-balance-and-statements/spec.md` (`PS-06`, `PS-12`), `specs/patient-registry/spec.md` (`PR-05` cross-reference)
- **Problem:**
  - `PS-06` honors confidential-communication instructions (`PR-05`) on *where* the guarantor's mail is sent (e.g., `no_home_mail` with an alternate address), and Question 21 notes counsel review for minor-consent services.
  - However, an adult dependent on a spouse's or parent's policy—or any patient exercising HIPAA § 164.522(b) confidential communications—may have a `PR-05` restriction withholding billing/visit disclosures **from the person who currently holds the guarantor role**. Addressing a statement to that guarantor showing the patient's name, visit date, and administrative service name (`PS-03`, `PS-06`) violates the restriction even if sent to the guarantor's alternate address.
- **Required Fix:**
  - Amend `PS-06` so that whenever a visit's patient has an active `PR-05` confidential-communication restriction that excludes the party holding the guarantor account (including across `PR-15` same-person groups) or requires patient-only billing contact, **openPractice MUST omit/hold those visit lines from any statement addressed to that guarantor and raise owned work** for staff to either transfer the visit's balance to the patient's own guarantor account (`PS-12`) or record the required legal/patient authorization.

---

### D. Repository Integrity & OpenSpec Lifecycle Hygiene

#### `R2-11` — Missing `.gitattributes` in `opensoft/openPractice-spec` (and `opensoft/openPractice-code`)
- **Severity:** Medium
- **Target:** `opensoft/openPractice-spec` root `.gitattributes` (and `opensoft/openPractice-code` root `.gitattributes`)
- **Problem:**
  - The assembly root `opensoft/openPractice` has `.gitattributes`, **but the `spec/` submodule repository has no `.gitattributes` file**.
  - Because all seven `openpractice-*.schema.json` contracts are verified by byte-exact SHA-256 digests (`SHA256SUMS`), any developer or CI runner on Windows with `git config core.autocrlf=true` checks out `\r\n` line endings inside `spec/` and breaks every `SHA256SUMS` check.
- **Required Fix:**
  - Add a `.gitattributes` file at the root of `opensoft/openPractice-spec` (and `opensoft/openPractice-code`) enforcing LF line endings (`* text=auto eol=lf`, explicitly `*.json text eol=lf`, `SHA256SUMS text eol=lf`, `*.md text eol=lf`, `*.yaml text eol=lf`, `*.sh text eol=lf`).

---

#### `R2-12` — Empty canonical `openspec/specs/` forces cross-change patching (`PA-1`–`PA-8`) and repeated `1.0` digest churn
- **Severity:** Low / Governance hygiene
- **Target:** `opensoft/openPractice-spec` — `openspec/specs/`, `openspec/changes/*`
- **Problem:**
  - Even after five changes merged to `main` (`0a82fc2`) and four more branches were drafted, `openspec/specs/` still has zero canonical capability specs.
  - As a result, `critique-2026-11-15-corrections` had to mutate already-merged `openspec/changes/*` directories in place, `define-revenue-cycle-core` had to carry `PA-1`–`PA-7` patch tables against sibling change folders, and `1.0` contract schemas are having their SHA-256 digests rewritten across branches instead of being promoted once and evolved cleanly via `1.0` -> `1.1` MINOR deltas.
- **Required Fix:**
  - After merging this wave (`framing-decisions-v2`, `critique-2026-11-15-corrections`, `define-practice-readiness`, `define-revenue-cycle-core`, plus `R2-01`–`R2-11` fixes) and completing owner ratification, promote/archive the merged capability specs into canonical `openspec/specs/<capability>/spec.md` and canonical `contracts/` so future changes write clean OpenSpec deltas (`ADDED` / `MODIFIED` / `REMOVED`) and bump `1.x` contract versions rather than editing historical change folders.

---

## Summary Remediation Checklist for Agent Execution

| ID | Severity | Primary Target Branch / Change | Short Action |
|---|---|---|---|
| `R2-01` | High | `change/define-revenue-cycle-core` | Thread-earmark insured check-in/checkout copays; apply on `RM-07` `patient_share_transfer`, skip in `PY-04` FIFO & `PY-06` credit-balance cases while thread is open |
| `R2-02` | High | `change/define-revenue-cycle-core` | Add `payer_cob_transfer` (+ `patient_to_payer_receivable_transfer`) in `RT-02`, permit counterparty-coverage transfer in `SL-01`, fix `CL-12` for secondary/tertiary claims |
| `R2-03` | High | `change/define-revenue-cycle-core` | Add non-billable payer write-off & payer credit-loss kinds in `RT-02` (`RT-09`/`RT-10`); specify `CG-08` reversal+replacement (or reclass kinds) in `EG-05` & `GF-11` |
| `R2-04` | High | `change/define-revenue-cycle-core` | Fix `PY-09` / `RT-02` so dishonored prepayments, unapplied credits, and unidentified receipts debit the holding liability/credit bucket instead of creating phantom `patient_receivable` |
| `R2-05` | Medium | `change/define-revenue-cycle-core` + `change/critique-2026-11-15-corrections` | Add/apply `PA-8` reconciling `AI-11`/`AI-13` (`customer-detail` mode) with `PY-03` prepayments, `PY-09` non-reversal chargebacks, `PS-05` due dates, and `PS-12` `guarantor_transfer` |
| `R2-06` | High | `change/define-revenue-cycle-core` | Break circular `TH-03` <-> `openpractice-thread-1.0.schema.json` reference by writing the explicit per-station `amount_at_risk` derivation table and updating `SHA256SUMS` |
| `R2-07` | Medium | `change/define-revenue-cycle-core` | Add `ready -> charged` (no-show fee after readiness) and `banked` reopening moves (chargeback/return/takeback/retraction) in `TH-04` |
| `R2-08` | High | `change/critique-2026-11-15-corrections` (or integration follow-up) | Add external accountant/biller actor type(s) + `accountant` recipe in `AM-01`/`AM-02` (`RI-51`), and apply `PA-1`–`PA-8` across target specs/schemas |
| `R2-09` | Medium | `change/define-practice-readiness` | Clarify `SR-04` state-board check as a structured verification record (not live scraping) and allow tier-4 `PR-15` non-identity resolution on common-name collisions |
| `R2-10` | Medium | `change/define-revenue-cycle-core` | Make `PS-06` fail closed (hold statement lines + raise work for `PS-12` transfer) when a patient's `PR-05` restriction excludes the visit's guarantor |
| `R2-11` | Medium | `change/critique-2026-11-15-corrections` (and `openPractice-code`) | Add `.gitattributes` in `openPractice-spec` enforcing `eol=lf` on `*.json`, `SHA256SUMS`, `*.md`, `*.yaml`, `*.sh` |
| `R2-12` | Low | Post-merge consolidation | Ratify and promote merged changes into canonical `openspec/specs/<capability>/spec.md` so `1.0` contract digests stop churning |
