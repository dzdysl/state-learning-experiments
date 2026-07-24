# Observations

- `learner.log` records `TTTLearnerMealy`, `MealyWpMethodEQOracle`, and the start of round 14.
- `console.log` records the fatal query inconsistency and the resulting `Could not refine hypothesis` exception.
- `cache_mismatch.log` records two live observations of the same eight-symbol prefix whose final output differs at `deregistrationAccept`.
- `cache_mismatch_trace.log` and `cache_mismatch_core.log` preserve the exact trace and core-log segments captured at the mismatch times.
- `analysis/derived/hypothesis_13_failure_query_simulation.json` simulates the frozen nine-symbol input against `evidence/hypotheses/hypothesis_13.dot`: it reaches `s1` and predicts `identityRequest authenticationRequest null_action authenticationRequest null_action authenticationRequest null_action null_action null_action`.
- Comparing that model output with the three sequences in `inputs/failure-query.txt` finds no complete equality: all three differ at step 7; run 1 additionally differs at step 8; runs 2 and 3 additionally differ at step 9.
- The two traces under `followups/baseline-vs-multiseq-mdf-20260723/raw/` each contain 50 sessions and 470 steps. Their session/step boundaries, input, abstract output and note fields are identical.
- Each regression trace contains 206 `trace_timeout_no_downlink` and 52 `next_input_before_downlink` records. The multiSeq-MDF console contains 203 timeout messages but no fatal inconsistency.
- `followups/t3560-retransmission-reproduction-20260724/analysis/root-cause.md` records a 2026-07-24 reproduction in which the PDU observed on `deregistrationRequest` steps 6 and 9 is byte-identical to the earlier second authentication request. The matching AMF log records Authentication-state mismatches for the deregistration inputs and no new authentication request caused by those inputs.
- Historical source inspection with `git show 8448e4173816b7c33dceb0c4a89a79a3c4067ae7:src/...` confirms that the reproduction revision uses a single global `retransbuf`, an 800 ms trace timeout, and an uncorrelated socket `readLine()` path. The Free5GC v4.2.1 snapshot assigns a new `amfUe.T3560` without first stopping the previous timer in the examined authentication-send path.
