# H13 cycle-cover repeat-10 register analysis — 2026-07-31

## Status

Completed planned multi-sequence measurement. All 28 generated H13 cycle-cover
sequences ran to completion, and the full trace contains 1,521 aligned
input/output steps. No abstract output instability or H13 output mismatch was
found.

C14 input lines 27 and 28 now use the replacement samples from interactive run
`20260731T092950Z-e8ae5e2f`. The original run remains the source for lines
1-26 and remains preserved in full. The replacement boundary and KSI evidence
are recorded in the sibling
[C14 ngKSI event-sync verification](../c14-ngksi-sync-verify-20260731/README.md).

This is a follow-up to the
[H13 interrupted learning record](../../README.md), not a separate failure
record. The 58 core-session records all report exit code 134, and every core
start emitted the same runtime-contract warning; those runtime facts are
preserved but are not causally analyzed here.

## Scope and method

Run `20260731T064710Z-74bcf3ee` executes
`inputs/hypothesis_13_cycle_cover_repeat10.seq`: one line per concrete cycle
variant, with the access prefix and repetition 1 treated as setup. Repetitions
2-10 are aligned by cycle edge and logical input.

The register exploration is deliberately asymmetric:

- AMF behavior is treated as a black box. Equations are logical-register
  candidates inferred from input/output observations, not AMF internal member
  names.
- UERANSIM/SUL behavior is source-correlated against commit
  `693678935ff2faf0d818d988de72e2a0505879df` for original lines 1-26. The
  replacement C14 lines 27-28 are behavior-correlated against commit
  `6b1bcc070331ac2d0443a255afbf5466413fd0b7` in their separate follow-up.
- No fixed register-analysis script was created; this is the required initial
  manual exploration.

## Confirmed results

- All 28 input lines map exactly to 28 complete trace groups.
- All 1,521 abstract outputs equal the corresponding H13 predictions.
- Repetitions 2-10 contain 146 same-edge series and 1,314 aligned samples,
  with no missing sample and no output-label instability.
- Every decoded downlink field other than KSI is constant on its aligned edge.
- Across the effective 78 downlink KSI series, the observed patterns are:
  `+1 mod 7` ×22, `+2 mod 7` ×16, constant 0 ×24, constant 1 ×14,
  and piecewise increment-then-hold ×2.
- For all 81 aligned Authentication Requests whose triggering Registration
  Request declares KSI 0-6,
  `AuthenticationRequest.KSI = (declared_KSI + 1) mod 7`.
- All 306 aligned Security Mode Commands copy the latest observed
  Authentication Request KSI.
- C08 line 14 is the only piecewise downlink KSI case. Its SMC KSI is
  `1,2,3,4,4,4,4,4,4` for repetitions 2-10. The associated UERANSIM context
  transition and cached GUTI KSI are preserved in
  `evidence/c08-line14-trace-lines-385-420.jsonl`.

## Archive

The complete 184-file, 22,626,148-byte run is frozen with its original
directory hierarchy at:

`D:\state-learning-lab\run-data\open5gs\20260731T064710Z-74bcf3ee`

Its content-tree SHA-256 is
`88a27d881468f9cecd345a0ebf9d2e95a687588a31c0de24a725f622c688b9ec`.
The original download directory was not modified or removed.

## Navigation

- Runtime/source provenance: [provenance.yaml](provenance.yaml)
- External artifact inventory: [artifacts.yaml](artifacts.yaml)
- Complete snapshot member hashes: [archive-manifest.json](archive-manifest.json)
- Objective observations: [analysis/observations.md](analysis/observations.md)
- Register candidates and explanations:
  [analysis/hypotheses.md](analysis/hypotheses.md)
- Machine-readable analysis summary:
  [analysis/derived/repeat2-10-analysis-summary.json](analysis/derived/repeat2-10-analysis-summary.json)
- C14 replacement record:
  [../c14-ngksi-sync-verify-20260731](../c14-ngksi-sync-verify-20260731/README.md)
- Exact sequence input:
  [inputs/hypothesis_13_cycle_cover_repeat10.seq](inputs/hypothesis_13_cycle_cover_repeat10.seq)

## Boundaries and next actions

1. Re-run C08 line 14 in isolation with more than ten repetitions if the
   registration-attempt threshold and cached-GUTI phase transition need an
   independent confirmation.
2. Allocate a separate failure record before investigating the repeated core
   exit code 134 or runtime-contract hash mismatch.
3. Do not promote the black-box equations to named AMF variables without a
   separate, explicitly authorized AMF white-box analysis.

The C14 line 27/28 event-sync verification is complete; do not use the older
line 27/28 field series as the effective result.
