# Observations

## Run identity and time window

- `evidence/run-provenance.json` identifies run
  `20260728T111737Z-fcf920b5`, mode `active_learning`, learner commit
  `11453a35e1c1dba5b49ae2d487f9e48ff0b493a8`, and a dirty runtime tree.
- `evidence/learner.log:1-10` starts the Core SUL logger at
  `2026-07-28 19:17:37 +08:00`, names `TTTLearnerMealy` and
  `MealyWpMethodEQOracle`, and starts round 1.
- The terminal record in the external `statelearner_trace.jsonl` is sequence
  and session 8419 at `2026-07-29T09:28:13Z`. The adjacent
  `statelearner_trace.meta` also contains `8419`.

## UERANSIM/SUL version evidence

- Open5GS state-learning repository tag
  `runtime/2026-07-28/learner-registration-state-init-buildfix` resolves to
  commit `2d6da6d83421ad20ac98df1a6be860677e23447b`. Its commit time is
  `2026-07-28T18:03:40+08:00`, and its annotated tag time is
  `2026-07-28T18:03:48+08:00`; both precede this run's local start time.
- At revision `2d6da6d`, `src/ue/app/state_learner.cpp:1147-1158` permits
  `identityResponse` only while the MM substate is
  `MM_REGISTERED_INITIATED_PS`. The same revision calls
  `beginLearnerRegistration()` only after a Registration Request is
  successfully sent (`src/ue/app/state_learner.cpp:921-941`).
- At revision `2d6da6d`, `src/ue/nas/mm/register.cpp:21-29` deep-copies the
  sent Registration Request before storing it and switching to
  `MM_REGISTERED_INITIATED_PS`. The immediately preceding
  `learner-registration-state-init` tag used a non-compiling shallow copy; the
  buildfix changes only that storage operation.
- This run's `evidence/hypotheses/hypothesis_20.dot:39` records
  `s0 -> s0 [identityResponse / null_action]`. The older baseline
  `experiments/open5gs/baseline-mdf-servreq/2026-07-17/evidence/hypotheses/hypothesis_23.dot:41`
  records `s0 -> s4 [identityResponse / authenticationRequest]`.
- The clean tagged reference `src.zip` is 4,081,827 bytes with SHA-256
  `3afa5b089fcdd4462ed44312ff7ffc9696247b68daea87adda99aca3e6bfc719`.
  No deployed UERANSIM binary or source archive was supplied with the run, so
  this is a reference-version hash rather than a captured deployment hash.

## Exported hypotheses

- `evidence/hypotheses/` contains 20 unmodified DOT exports,
  `hypothesis_1.dot` through `hypothesis_20.dot`.
- `evidence/learner.log:338-362` records rounds 19 and 20. At
  `2026-07-29 15:31:30 +08:00`, round 20 reports `used all CEs.` and 8,681
  equivalence queries. The log ends there without a completion or convergence
  marker.
- `hypothesis_20.dot` declares 26 state nodes (`s0` through `s25`) and 312
  transitions. Its SHA-256 is
  `175afb542e3fd8401cd529bc6b7c611faddc5fbaf84a809a715b4d045db00bff`.

## Interrupted ending

- `evidence/run-manifest.json` still records status `running`.
- Parsing all 923 external
  `raw/open5gs/core-session-*/session.json` files yields 922 sessions with
  status `failed` and exit code 134, followed by one session with status
  `running` and no exit code. The frozen summary is
  `analysis/derived/session-status-summary.json`.
- `evidence/final-session/session.json` records session 923 starting at
  `2026-07-29T09:26:11Z` with null `ended_utc` and null `exit_code`.
- External `raw/console.log:472627-472661` ends during the second repetition
  of an in-progress query, immediately after sending `securityModeReject`.
  No causal interpretation is assigned to this truncation.

## Archive boundary

- `archive-manifest.json` verifies 2,775 structured-run files
  (172,692,676 bytes) and 24 selected learner-export files (5,470,335 bytes)
  against their external snapshots.
- `D:\downloads\Open5GS.log\console.log` has SHA-256
  `8283123cdea1fd9b3d31f5408257ec79c9c341d181f86bab3fb914f1d7497704`,
  exactly matching the 2026-07-17 baseline console. It was excluded from this
  iteration to prevent cross-run evidence mixing.
