# Ideation Work Area

Brainstorm packets for openPractice concepts live here, under
`brainstorm/`. Packets follow the xFactory three-tier standard:
atomic documents (one concept each), synthesis documents
(recombined clusters), and overview documents (whole-system
lenses). All packet documents carry `Status: brainstorm` —
non-normative, contradiction legal.

Lifecycle: `brainstorm` → `staged` (`ideation/staging/<topic>/`)
→ OpenSpec proposal → ratified → implemented → promoted.
openPractice does not yet define local staging rules; when staging
begins, adopt or reference the openxFactory document-lifecycle
standard rather than inventing a parallel vocabulary here.

Rules:

- One packet anchor (kebab-case) shared by every file in a packet
  via `Topics:`.
- Never overwrite an existing document silently; new files unless
  an update to named files is explicitly requested.
- Domain-owned ideas stay here; neutral or cross-factory ideas
  route to `openxFactory` instead.

## Customer, clinical, and financial separation

The [customer and financial separation packet](brainstorm/openpractice-customer-financial-separation-overview.md)
captures the 2026-11-14 review and the owner's direction: assume necessary
customer identity exists in accounting, minimize unnecessary patient data,
and protect the resulting healthcare financial records. Three atomic documents,
one synthesis and one overview connect shared identity, insurance billing,
memberships/commerce, accounting and business views to the existing designs.
The packet includes a review matrix and links to the
[proposed OpenSpec requirements](../openspec/changes/define-customer-financial-data-boundaries/proposal.md).
It supplements the historical packets and does not change their lifecycle or
declare new beta scope.
