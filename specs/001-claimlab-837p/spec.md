# Feature Spec: claimlab — 837P test generator

## Status
Draft. Test app only; not part of openPractice.

## Summary
claimlab is a throwaway test application to learn the X12 837P (professional
claim, 005010X222A1) file format before claims support is built into
openPractice. It generates, validates, and compares 837P files from synthetic
claim data.

## Goals
- Produce structurally valid 837P files (envelopes, loops 1000A/B, 2000A,
  2000B, 2300, 2400) for simple Medicare Part B scenarios.
- Validate structural invariants (segment counts, control numbers, ISA width).
- Diff two 837 files element-by-element to support field mapping via
  differential testing against PC-ACE output.

## Non-goals
- No PHI. All fixtures are synthetic.
- Not production claims submission. Files are draft until Novitas testing passes.
- No 837I (institutional) support.
- No X12 implementation guide content. Field knowledge comes from free
  payer/CMS companion guides, free X12 example files, and PC-ACE output diffs.

## Requirements
1. build: read a JSON claim description and write an 837P file.
2. validate: check ISA fixed width (106), open/close segment pairing,
   SE segment count, ISA13 == IEA02.
3. diff: compare two files and report element-level differences.
4. Sanitization: strip reserved delimiters from input data.
5. Test mode always on by default (ISA15 = T).

## Method (dispatch mapping)
Differential testing: enter one claim in PC-ACE, save the output, change one
field, save again, then diff. Record (screen, field, value) -> (segment,
element) in field-map.md. Repeat per claim type and scenario.

## Risks and open questions
- X12 copyright/licensing: do not copy X12 guide text or tables; write our own
  definitions. Get legal review before any commercial reuse.
- Puerto Rico contractor: confirm which MAC covers PR before Novitas enrollment.
- Missing loops: rendering/referring providers, ambulance, DME, prior
  authorization, other insurance (COB).
- Real receiver/payer/submitter IDs come from EDI enrollment.

## Acceptance criteria
- Sample fixture builds a file that passes validate.
- A changed surname produces exactly one reported element diff (NM1 IL 03).
- Review by maintainer before merge.
