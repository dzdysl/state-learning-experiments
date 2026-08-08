# Objective observations

- `raw/learner-export/learner.log:263-273` records round 14, 1,005 membership
  queries, 23,742 equivalence queries, and a final 18-state hypothesis.
- `evidence/hypotheses/hypothesis_14.dot` and
  `raw/learner-export/learnedModel.dot` are byte-identical by SHA-256
  (`19fe51296d78d000d2ab7e9106d4555b0064ca7f5e7b78c608c39f05db5ca8d0`).
- `evidence/final-run/run-manifest.json:8` records `finalizer_failed`.
- `raw/learner-export/learner.log:260-273` ends after the final learner
  summary; it does not contain an independent runtime-success marker.
- The complete structured console log records `[core][ERROR] Open5GS launcher
  finished with status 134` followed by `RUN_CONTEXT finalizer exited non-zero`
  after the final learner summary. The complete log is the external snapshot
  recorded in `artifacts.yaml`.
- The run manifest does not include warnings. It does not identify the cause of
  launcher exit code 134.
