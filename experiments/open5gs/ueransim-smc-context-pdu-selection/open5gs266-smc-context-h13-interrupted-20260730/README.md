# Open5GS SMC-context PDU-selection learning run — 2026-07-30

## Status

Archived as an interrupted, non-converged experiment iteration. The latest
exported model is H13 (`hypothesis_13.dot`, 17 states and 204 transitions).
The learner continued equivalence work after exporting H13, but the supplied
run ends inside the second repetition of an unfinished query and contains no
normal completion marker.

Later material from the same run ID is frozen separately in
[the H14 completion follow-up](followups/h14-learning-complete-finalizer-failed-20260801/README.md).
It records an 18-state final learner hypothesis, while also preserving the
finalizer failure caused by an Open5GS launcher exit with status 134. This
parent remains the original H13 evidence boundary.

## Scope

This record preserves Open5GS v2.6.6 active-learning run
`20260730T054606Z-60d7ce19`. Structured run data begins at
`2026-07-30T05:46:06Z`; the final protocol event is at
`2026-07-31T02:45:46Z`. This is a separate iteration from the 2026-07-28 H20
run because it uses a later semantic UERANSIM/SUL source tree. It is not yet a
separately bounded failure investigation.

## Version assignment

- The experiment series is assigned to semantic UERANSIM/SUL version
  `runtime/2026-07-30/smc-context-pdu-selection`, commit
  `693678935ff2faf0d818d988de72e2a0505879df`, `src/` tree
  `516aef65c8edb34af33e432e49c3c3c39e2c3614`.
- The closest tagged repository snapshot before the run is
  `runtime/2026-07-30/workflow-doc-consolidation`, commit
  `792add084006069a5da8aefae5caebbd3adab48f`. That commit changes
  documentation only and has the same `src/` tree as the semantic version.
- The run started at `2026-07-30 13:46:06 +08:00`, 22 minutes after the
  closest tag was created. H13's initial-state Identity Response behavior is
  consistent with the registration-state gate in that source tree.
- No deployed UERANSIM binary or source-archive hash was supplied. The
  assignment is therefore medium-confidence timing, source-tree and
  behavior correlation, not byte-for-byte deployment proof.
- The learner JAR independently identifies commit `11453a35...` and SHA-256
  `f1e0d03a...`; it is secondary provenance and does not determine the
  experiment series.

## Confirmed facts

- The learner used `TTTLearnerMealy` with `MealyWpMethodEQOracle` and exported
  13 immutable DOT hypotheses.
- H13 was exported after round 13 reported `used all CEs.` and 7,854
  equivalence queries. It is an intermediate hypothesis, not a converged
  final model.
- The structured trace continues through sequence 7,831 after H13. The final
  record contains only the first symbol of a new sequence.
- The structured run contains 863 core sessions: 862 are recorded as
  `failed` with exit code 134, and the final session remains `running`.
  This inventory is factual; no defect cause is assigned here.
- The complete selected material contains 2,612 files and 165,976,493 bytes.
  It is frozen outside Git as two verified content-tree snapshots.
- H13 has been rendered as an SMP presentation derivative:
  [analysis/derived/hypothesis_13_smp.svg](analysis/derived/hypothesis_13_smp.svg).
  The original DOT remains unchanged under `evidence/hypotheses/`.
- H13 round-by-round refinement reaches 17 singleton classes in effective
  round 4 and confirms convergence in round 5. The final state-split diagram
  is [analysis/derived/refinement/hypothesis_13_round_refinement_flowchart.svg](analysis/derived/refinement/hypothesis_13_round_refinement_flowchart.svg).
- The H13 binary-split backtrace is retained separately at
  [analysis/derived/iterative_backtrace/hypothesis_13_all_binary_backtrace_flowchart.svg](analysis/derived/iterative_backtrace/hypothesis_13_all_binary_backtrace_flowchart.svg).
- The H13 SMP edge set has an exact, signal-constrained cycle cover:
  under the output-only signal rule, simple cycles alone leave E009 and E033
  uncovered, so the analyzer uses the approved closed-walk fallback. The final
  63 valid candidates reduce to 14 selected closed routes covering all 33
  target edges. The minimum maximum route length is 9, total selected length
  is 64, and repeated transition use is 29. Each selected route is shown in
  its own full-SMP SVG with that route highlighted in a distinct color; no
  per-route DOT or PDF is retained. See the
  [cycle-cover report](analysis/derived/cycle_cover/hypothesis_13_cycle_cover_report.md).
- The 14 selected closed routes have been converted into 28 executable
  sequences by rotating each route to its lowest-numbered state, prepending
  the shortest H13 access sequence, expanding merged inputs, and repeating
  each concrete route 10 times. See
  [inputs/hypothesis_13_cycle_cover_repeat10.seq](inputs/hypothesis_13_cycle_cover_repeat10.seq).
- The repeat-10 sequence test has completed as run
  `20260731T064710Z-74bcf3ee`. All 1,521 abstract outputs match H13 and no
  same-edge output-label instability was found. Manual black-box register
  exploration and UERANSIM source correlation are recorded in
  [followups/cycle-cover-repeat10-register-analysis-20260731](followups/cycle-cover-repeat10-register-analysis-20260731/README.md).
- C14 input lines 27 and 28 were re-run after the `regReqGUTI` ngKSI
  event-sync change as run `20260731T092950Z-e8ae5e2f`. The new samples retain
  the accepted SMC KSI across current-context clearing and synchronize both
  GUTI-template uses in every analyzed repetition. They supersede only the
  earlier C14 line 27/28 analysis; the original raw run remains immutable. See
  [followups/c14-ngksi-sync-verify-20260731](followups/c14-ngksi-sync-verify-20260731/README.md).

## Material classification

- `D:\downloads\runs\20260730T054606Z-60d7ce19` is the bounded structured run
  and was archived in full.
- Seventeen exact learner exports were selected from
  `D:\downloads\workflow-doc-consolidation`: H1–H13, `learner.log`,
  `learning_queries.log`, `equivalence_queries.log`, and
  `run-provenance.json`.
- Logger lock files and the empty `learnlib.log` were excluded. The
  task-generated `_smp.dot` and SVG were also excluded from raw material;
  the final SMP derivative was regenerated from the archived evidence DOT.

## Navigation

- Runtime and source provenance: [provenance.yaml](provenance.yaml)
- External artifact inventory: [artifacts.yaml](artifacts.yaml)
- Complete per-file hashes: [archive-manifest.json](archive-manifest.json)
- Objective findings: [analysis/observations.md](analysis/observations.md)
- Rendering provenance: [analysis/rendering.md](analysis/rendering.md)
- H13 refinement report:
  [analysis/derived/refinement/hypothesis_13_refinement_report.md](analysis/derived/refinement/hypothesis_13_refinement_report.md)
- H13 iterative-backtrace report:
  [analysis/derived/iterative_backtrace/hypothesis_13_all_binary_backtrace_report.md](analysis/derived/iterative_backtrace/hypothesis_13_all_binary_backtrace_report.md)
- H13 exact minimum-cycle-cover report:
  [analysis/derived/cycle_cover/hypothesis_13_cycle_cover_report.md](analysis/derived/cycle_cover/hypothesis_13_cycle_cover_report.md)
- H13 machine-readable cycle-cover result:
  [analysis/derived/cycle_cover/hypothesis_13_cycle_cover.json](analysis/derived/cycle_cover/hypothesis_13_cycle_cover.json)
- H13 cycle-cover repeat-10 input sequences:
  [inputs/hypothesis_13_cycle_cover_repeat10.seq](inputs/hypothesis_13_cycle_cover_repeat10.seq)
- Repeat-10 output-stability and register-analysis follow-up:
  [followups/cycle-cover-repeat10-register-analysis-20260731](followups/cycle-cover-repeat10-register-analysis-20260731/README.md)
- C14 line 27/28 ngKSI event-sync replacement:
  [followups/c14-ngksi-sync-verify-20260731](followups/c14-ngksi-sync-verify-20260731/README.md)
- H14 基础组运行时验证（37/37 条逻辑序列、1,656/1,656 步与 H14 一致；核心停止/重启异常独立记录）：
  [followups/h14-base-cycle-cover-runtime-verify-20260804](followups/h14-base-cycle-cover-runtime-verify-20260804/README.md)

## Next actions

1. Re-run C08 line 14 in isolation with more repetitions if the observed
   increment-then-hold KSI phase transition needs independent confirmation.
2. Recover the host-side stop or interruption record before deciding whether
   this run can be resumed from H13.
3. If the repeated core exit code 134 is to be investigated as abnormal,
   allocate a separate failure record and freeze one minimal reproducer.
4. Do not treat H13 as converged unless a later equivalence-complete run
   confirms it.
