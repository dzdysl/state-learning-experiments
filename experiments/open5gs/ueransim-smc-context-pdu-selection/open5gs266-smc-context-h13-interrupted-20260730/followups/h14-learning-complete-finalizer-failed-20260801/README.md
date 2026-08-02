# H14 active-learning completion with teardown anomaly

## Status

The learner completed 14 rounds and exported a final 18-state hypothesis. The
enclosing run finalizer then reported Open5GS launcher exit status 134 while
stopping the test core. Under the repository outcome rule, this is a successful
active-learning run with a recorded teardown anomaly, not a failed experiment.

## Scope

This follow-up freezes the later material delivered for the same active-learning
run `20260730T054606Z-60d7ce19`. It supersedes the parent's H13-only conclusion
about the eventual learner output while preserving the original H13 frozen
boundary and its analyses.

## Confirmed facts

- `learner.log` reports `Rounds []: 14`, 1,005 membership queries, 23,742
  equivalence queries, and `States in final hypothesis: 18` at
  `2026-08-01T22:49:54+08:00`.
- `evidence/hypotheses/hypothesis_14.dot` and
  `raw/learner-export/learnedModel.dot` have the same SHA-256:
  `19fe51296d78d000d2ab7e9106d4555b0064ca7f5e7b78c608c39f05db5ca8d0`.
- The final run manifest says `finalizer_failed`. The end of the complete
  console log records Open5GS launcher exit status 134 followed by
  `RUN_CONTEXT finalizer exited non-zero`.
- The exit-134 condition is retained as a teardown anomaly. It must not be
  used to relabel this completed active-learning result as failed.

## Material

- The complete 6,710-file structured run is an external verified snapshot;
  see [artifacts.yaml](artifacts.yaml) and [archive-manifest.json](archive-manifest.json).
- The complete 20-file learner export is retained under
  [raw/learner-export](raw/learner-export) because it is 9,127,201 bytes.
- The final hypothesis and final runtime manifest/provenance are direct small
  evidence under [evidence](evidence).

## Navigation

- Objective facts: [analysis/observations.md](analysis/observations.md)
- Runtime/source provenance: [provenance.yaml](provenance.yaml)
- Original H13 frozen boundary: [../../README.md](../../README.md)

## Next action

Keep the representative exit-134 diagnosis with the matching Open5GS source
snapshot as lifecycle evidence; it does not invalidate the completed final
model.
