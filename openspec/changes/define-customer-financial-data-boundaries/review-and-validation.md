# Review And Validation

Date: 2026-11-14
Status: proposed documentation; no runtime implementation
Branch: define-customer-financial-data-boundaries
Spec baseline: 261613c1e759469c9f4f96d971336a876ef809f5

## Review result

The existing documents already separate EHR authority, healthcare receivables
and external general accounting. The update fills the customer/membership and
commercial-financial boundary gaps while preserving the existing party register,
Roster, immutable postings, financial authority and export-batch freeze.

The [review overview](../../../ideation/brainstorm/openpractice-customer-financial-separation-overview.md)
contains the source-document matrix and the historical period-close and
constitution-reference discrepancies. Existing brainstorms and the separate
reimbursement proposal were preserved; the ideation index points to this update.

Deliverables: three atomic documents, one synthesis, one overview, and three
proposed capability deltas containing 16 requirements and 32 scenarios. The
OpenSpec checklist records later governance/handoff, not duplicate executable
implementation tasks. Vendor selection and release sequencing remain open.

## Validation environment

The owner selected the existing `py-bench` container for project validation.
OpenSpec version: 1.13.1. No host fallback or new container was used.
The commands below run from the spec worktree unless explicitly stated.

## Checks

| Check | Result |
|---|---|
| `openspec validate define-customer-financial-data-boundaries --strict --no-interactive` | PASS: change is valid |
| `openspec status --change define-customer-financial-data-boundaries` | PASS: proposal, specs, design and tasks present; 4/4 planning artifacts complete |
| Brainstorm packet validator | PASS: 5 documents, comprising 3 atomic, 1 synthesis and 1 overview |
| Read-only Markdown inspection | PASS: relative file links resolve, no trailing whitespace, final newlines present, no machine-specific absolute paths |
| `git diff --check` | PASS for the tracked index change; the Markdown inspection also covered the new untracked documents |
| `make validate` from the assembly root | PASS: naming, manifest, spec/code pin and tree-digest consistency, 11 shape-file digests |

Packet validation command:

```sh
python3 "$HOME/.codex/skills/document-software-brainstorm/scripts/validate_packet.py" \
  --root ideation/brainstorm \
  --packet-prefix openpractice-customer-financial-separation
```

These checks validate the proposed documentation and current assembly
consistency. They do not prove a future integration's HIPAA compliance or
financial behavior. Runtime acceptance belongs to the later feature's tests
and security review.

## Triad disposition

Work is isolated in a spec-leg worktree. The assembly and its pinned spec/code
checkouts remain unchanged. No implementation feature, code change, commit,
push, pull request, merge, pin bump or archival was performed for this update.

## Reconciliation edits (ruling 26 / ruling 25, 2026-11-14)

Brett Heap ruled 25 and 26 on 2026-11-14 in an authoring session and
authorized reconciling them into this change. The agent that authors
`define-mvp-practice-foundation` made the edits below in a separate clone.
Status stays draft. Nothing else in this change, its tasks or the brainstorm
packet was rewritten. The change still has 16 requirements and 32 scenarios.

Ruling 26 assigns ownership. `define-mvp-practice-foundation` owns the
posting mechanics of `openpractice-accounting-integration/1.0`:

- posting identity and idempotency;
- batches and their freeze, and release;
- retries;
- the reconciliation report;
- posting modes;
- the BAA gate.

This change owns customer and party roles, routing by offering, obligation
ownership and purpose-bound business views. It references that contract for
posting.

Ruling 25 lets medical-billing customers carry a name and billing contact in a
destination only after its BAA gate is satisfied, and only in
`customer-detail` mode. The `summary-journal` mode carries no customer records
or names.

| # | Place | Edit | Reason |
|---|---|---|---|
| 1 | `specs/financial-domain-routing/spec.md`, "Each economic event follows one accounting posting path" | The requirement now cites the contract (change `define-mvp-practice-foundation`) for event identity, mapping, retries, batches, release, the reconciliation report, the modes and the BAA gate. It states that this change defines no second posting mechanism. Kept: source domain, immutable identifier, entity, one path per event, recognition once, and distinguishable stages. | ruling 26, 2026-11-14 |
| 2 | Same requirement, scenario "Invoice and summary contain the same event" | The scenario now names the `customer-detail` and `summary-journal` modes. The contract's mode lock and idempotency prevent the duplicate, and its reconciliation report exposes the acknowledgement. | ruling 26, 2026-11-14 |
| 3 | Same requirement, scenario "Posting is retried after timeout" | The retry follows the contract's idempotency and its lookup of indeterminate outcomes, under the original posting identity. | ruling 26, 2026-11-14 |
| 4 | Same file, "Mixed payments and corrections preserve reconciliation controls" | "Immutable exported batches" is replaced by a reference to the contract's export batches, their freeze and the reconciliation report. The boundary and control list is kept. | ruling 26, 2026-11-14 |
| 5 | Same requirement, scenario "Refund follows an exported settlement" | Correcting entries follow the contract's correction rules, and the original batch is called frozen. | ruling 26, 2026-11-14 |
| 6 | `specs/customer-role-boundaries/spec.md`, "Accounting receives a limited customer and financial record" | One sentence is added. A PHI-bearing customer's name and billing address/contact reach a destination only after its BAA gate. Medical-billing customers go only in `customer-detail` mode, and `summary-journal` carries no customer records or names. | ruling 25, 2026-11-14 |
| 7 | `proposal.md`, the What Changes bullet on posting routes | The options are now named as the contract's `customer-detail` and `summary-journal` modes. Posting mechanics are attributed to `define-mvp-practice-foundation`. | ruling 26, 2026-11-14 |
| 8 | `design.md` §4, first two paragraphs | The "baseline posting route" sentence is replaced by the contract rule (that change's AI-09 and AI-14). Every destination starts in `summary-journal` and may move to `customer-detail` only before its first released batch, after its BAA gate. The currency, version, mapping and retry mechanics now sit in the contract reference. No double recognition and no second collectible balance are kept. | ruling 26, 2026-11-14 |

Validation of the reconciled change:

| Check | Result |
|---|---|
| `openspec validate define-customer-financial-data-boundaries --strict --no-interactive`, run with OpenSpec 1.2.0 in a scratch clone outside `py-bench` | PASS: change is valid |
| The same command and `openspec validate --all --strict --no-interactive`, run in a temporary unpushed merge with branch `change/define-mvp-practice-foundation` | PASS: 3 passed, 0 failed |
| `git diff --check` | PASS |

The `py-bench` run with OpenSpec 1.13.1 was not repeated.

## Re-review edits (fix round 3, 2026-11-14)

A re-review of the three coordinated changes found defects, and Brett Heap
ruled 27 to 31 the same day. The agent that authors `define-mvp-practice-foundation`
made the edits below in a separate clone. Status stays draft. The change now has
16 requirements and 34 scenarios, because two scenarios were added.

| # | Place | Edit | Reason |
|---|---|---|---|
| 9 | `specs/financial-domain-routing/spec.md`, scenario "Invoice and summary contain the same event" | Replaced by "An event could reach accounting twice". The contract refuses a batch in a second mode for a destination (AI-09), and routing gives each event one owner. The cross-mode deduplication it relied on does not exist. | re-review N3, 2026-11-14 |
| 10 | Same file, "Mixed payments and corrections preserve reconciliation controls" | Each settlement names one deposit poster. For a deposit openPractice explains, openPractice is that poster (companion BAB-14) and posts other-route shares as `other_route_share`. A scenario covers a card settlement that funds medical and membership balances. | re-review N4, 2026-11-14 |
| 11 | Same file, "Offerings determine financial routing at line level" | Medical charge finalization checks the route and the membership entitlement before any medical invoice or claim (companion BAB-16). | re-review N4, 2026-11-14 |
| 12 | Same file, "Membership and retail obligations use connected business ledgers" | openPractice's offering routing refuses to route a PHI-bearing membership obligation to a destination without a satisfied BAA gate in openPractice's destination register. It is named as the enforcement point. A scenario covers the refusal. | re-review N7, 2026-11-14 |
| 13 | `specs/customer-role-boundaries/spec.md`, "Accounting receives a limited customer and financial record" | Several changes:<br>- Service-revealing reason categories are added to the exclusions (ruling 25).<br>- Medical-membership labels must be neutral.<br>- PHI-bearing records are defined.<br>- Any system writing one needs the destination's BAA gate, recorded in openPractice's register (companion AI-14) and enforced by its routing.<br>- The recipient profile is the companion's AI-23 shape. | re-review N7 and N9, ruling 25, 2026-11-14 |
| 14 | `specs/purpose-bound-business-data/spec.md`, "Every business output has a scoped recipient profile" | Uses the companion's single recipient-profile shape (AI-23) instead of defining a second one. | re-review N9, 2026-11-14 |
| 15 | `design.md` §2 | The same exclusion, label and BAA-gate statements as edit 13. | re-review N7, 2026-11-14 |
| 16 | `design.md` §5 | The recipient profile references the companion's AI-23 shape. | re-review N9, 2026-11-14 |
| 17 | `design.md` §4 | `summary-journal` is described as the default (balanced category totals to payer-class control accounts), and `customer-detail` as an opt-in. This replaces the round-2 phrase "the existing deposit-level medical summary journal". | ruling 28, 2026-11-14 |
| 18 | `owner-ratification.md` (new) | A PENDING owner gate mirroring the companion's: validation evidence, companion consistency, handoff shape and the required statement. | re-review N15, 2026-11-14 |
| 19 | `tasks.md` 1.3, 1.4, 2.1 | A strict-validation task and an owner-ratification task now come before the single Speckit handoff. | re-review N15, 2026-11-14 |

Validation of the round-3 revision:

| Check | Result |
|---|---|
| `openspec validate define-customer-financial-data-boundaries --strict --no-interactive`, run with OpenSpec 1.2.0 in a scratch clone | PASS: change is valid |
| `openspec validate --all --strict --no-interactive`, run in a temporary unpushed merge with `change/define-mvp-practice-foundation` | PASS: 3 passed, 0 failed |
| `git diff --check` | PASS |

## Verification edits (fix round 4, 2026-11-14)

Brett Heap ruled 32 and 33 on 2026-11-14. A verification pass also found one remaining gap. The change now has 16 requirements and 36 scenarios. Status stays draft.

| # | Place | Edit | Reason |
|---|---|---|---|
| 20 | `specs/customer-role-boundaries/spec.md`, "Parties have distinct effective roles" | The customer role applies only to membership and retail buyers. A medical-billing accounting customer is derived from the guarantor or payer role. Adds a scenario. | ruling 32, 2026-11-14 |
| 21 | Same file, "Cross-system identity links are controlled", and `specs/financial-domain-routing/spec.md`, "Mixed payments and corrections preserve reconciliation controls" | Sign-offs follow FD-5 tiers under the companion's BAB-17. An opted-in named AI agent with an openWallet ID may sign; tier 4 is human-only. | ruling 33, 2026-11-14 |
| 22 | `specs/financial-domain-routing/spec.md`, "Membership and retail obligations use connected business ledgers" | A configuration-time gate refuses to activate a PHI-bearing offering against an ungated destination. Writes made outside openPractice are recorded as a deployment control. Adds a scenario. | verification N7 remainder, 2026-11-14 |
| 23 | `design.md` | Adds an owner-rulings table (rulings 25, 26, 28, 32 and 33), the ruling-32 statement in §1, the ruling-33 statement in §5, and a risk entry for ledger-side writes. | rulings 32 and 33, N7 remainder |
