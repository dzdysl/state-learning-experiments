# Baseline versus multiSeq-MDF regression

Status: completed; behavior-equivalent regression only.

This follow-up runs the same 50 sequences against a baseline build and a multiSeq-MDF build. It asks whether the newer runner changes the abstract behavior or reproduces the original TTT fatal inconsistency.

## Result

- Both traces contain 50 sessions and 470 steps.
- The input, abstract output, note, session boundary and step boundary are identical for all 470 records.
- Neither available run record contains `FATAL QUERY INCONSISTENCY`, `Could not refine hypothesis` or `IllegalStateException`.
- Both traces still contain 206 `trace_timeout_no_downlink` and 52 `next_input_before_downlink` records.

Therefore this run is evidence of abstract behavior equivalence, not evidence that MDF fixed the original asynchronous output problem. See `analysis/comparison.md`.

## Layout

- `inputs/test-50.seq`: exact 50-sequence test set.
- `raw/statelearner_trace_baseline.jsonl`: complete baseline trace.
- `raw/statelearner_trace_multiseq-mdf.jsonl`: complete multiSeq-MDF trace.
- `raw/multiseq-mdf-run.zip`: multiSeq run envelope and console.
- `evidence/baseline-config-path-error.log`: frozen baseline configuration-path error.
- `provenance.yaml`: known and missing version information.
