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
