# Open5GS UERANSIM registration-state-init buildfix learning run — 2026-07-28

## Status

Archived as an interrupted, non-converged experiment iteration. The latest
exported model is H20 (`hypothesis_20.dot`, 26 displayed states). The learner
continued equivalence work after exporting H20, but the supplied run ends
inside an unfinished query and contains no normal completion marker.

## Scope

This record preserves the Open5GS v2.6.6 active-learning run
`20260728T111737Z-fcf920b5`. Structured run data begins at
`2026-07-28T11:17:37Z`; the final protocol-trace record is at
`2026-07-29T09:28:13Z`. The experiment series is assigned from the actual
UERANSIM/SUL semantic version, not from the learner JAR version or the legacy
output-directory name. It is not a replacement for the 2026-07-17 baseline
and not yet a separately bounded failure investigation.

## Version assignment

- Primary UERANSIM/SUL version:
  `runtime/2026-07-28/learner-registration-state-init-buildfix`, commit
  `2d6da6d83421ad20ac98df1a6be860677e23447b`.
- The run began at `2026-07-28 19:17:37 +08:00`, after the buildfix tag was
  created at `18:03:48 +08:00`.
- H20 reports `identityResponse / null_action` from `s0`, matching the
  registration-state gate introduced by this UERANSIM version. The 2026-07-17
  baseline H23 instead reports `identityResponse / authenticationRequest`.
- The exact deployed UERANSIM binary or source-archive hash was not supplied.
  The version assignment is therefore high-confidence behavioral and timing
  correlation, not a byte-for-byte deployment proof.
- The learner JAR independently identifies commit `11453a35...`,
  `runtime/2026-07-24/core-output-filter-v3-dirty`; it remains secondary
  runtime provenance and does not determine this record's series.

## Confirmed facts

- The learner used `TTTLearnerMealy` with `MealyWpMethodEQOracle` and exported
  20 immutable DOT hypotheses.
- The UERANSIM/SUL behavior differs from the 2026-07-17 baseline at the
  initial-state Identity Response transition and matches the
  registration-state-init variant.
- `learner.log` reaches round 20 and reports `used all CEs.` at
  `2026-07-29 15:31:30 +08:00`; it contains no later counterexample,
  convergence, or shutdown record.
- The complete run tree contains 2,775 files and 172,692,676 bytes. It is
  frozen outside Git with a verified content-tree SHA-256.
- The final core session is still marked `running`, while the console ends
  during the second repetition of an in-progress query.
- The latest model has been rendered as an SMP presentation derivative:
  [analysis/derived/hypothesis_20_smp.svg](analysis/derived/hypothesis_20_smp.svg).
  The original DOT remains unchanged under `evidence/hypotheses/`.

## Material classification

- `D:\downloads\runs\20260728T111737Z-fcf920b5` is the bounded structured run
  and was archived in full.
- The current H1–H20 DOT files, `learner.log`, `learning_queries.log`,
  `equivalence_queries.log`, and `run-provenance.json` were selected from
  `D:\downloads\Open5GS.log` into a separate learner-export snapshot.
- `D:\downloads\Open5GS.log\console.log` was excluded because its bytes and
  SHA-256 exactly match the console already archived for the 2026-07-17
  baseline. Lock files and empty logger outputs were also excluded.

## Navigation

- Runtime and source provenance: [provenance.yaml](provenance.yaml)
- External artifact inventory and hashes: [artifacts.yaml](artifacts.yaml)
- Full per-file archive manifest: [archive-manifest.json](archive-manifest.json)
- Objective findings: [analysis/observations.md](analysis/observations.md)
- SVG rendering provenance: [analysis/rendering.md](analysis/rendering.md)

## Next actions

1. Recover the termination command or host-side shutdown record before
   deciding whether this run can be resumed from H20.
2. If the repeated core exit code 134 is to be investigated as a defect,
   allocate a separate failure record and freeze one minimal reproducer.
3. Treat H20 as the latest exported intermediate hypothesis, not as a learned
   final model.
