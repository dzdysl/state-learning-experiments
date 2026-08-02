# Observations

## Run identity and time window

- `evidence/run-provenance.json:3-4` identifies active-learning run
  `20260730T054606Z-60d7ce19`, started at
  `2026-07-30T05:46:06.583983521Z`.
- `evidence/run-provenance.json:8,15,21` records configuration SHA-256
  `c8792b...`, learner commit `11453a35...`, and learner JAR SHA-256
  `f1e0d03a...`.
- `evidence/run-manifest.json:3-8` still records the same run as `running`.
- The final external `statelearner_trace.jsonl` record is sequence 7,831,
  step 1, at `2026-07-31T02:45:46Z`; `statelearner_trace.meta` also contains
  `7831`.

## Runtime source correlation

- `D:\state-learning-lab\sources\open5gs_old\open5gs\meson.build:28`
  identifies the core-network snapshot as Open5GS 2.6.6. The runtime session
  records `/opt/CoreCrisis/Corelearner/open5gs-2.6.6`.
- Open5GS state-learning tag
  `runtime/2026-07-30/smc-context-pdu-selection` resolves to commit
  `693678935ff2faf0d818d988de72e2a0505879df` and `src/` tree
  `516aef65c8edb34af33e432e49c3c3c39e2c3614`.
- The later tag `runtime/2026-07-30/workflow-doc-consolidation` resolves to
  commit `792add084006069a5da8aefae5caebbd3adab48f`, changes documentation
  only, and has the same `src/` tree. It predates the run start by 22 minutes.
- At revision `693678935`, `src/ue/nas/mm/security.cpp:36-81` checks the
  Security Mode Command MAC against candidate contexts, and lines 179-185
  invoke that selection before accepting a context.
- At the same revision, `src/ue/app/state_learner.cpp:1147-1159` emits
  `null_action` for Identity Response outside
  `MM_REGISTERED_INITIATED_PS`; `evidence/hypotheses/hypothesis_13.dot:30`
  records `s0 -> s0 [identityResponse / null_action]`.
- At the same revision, `src/ue/nas/mm/register.cpp:21-34` deep-copies the
  sent Registration Request and then enters
  `MM_REGISTERED_INITIATED_PS`.
- These observations correlate the supplied behavior with the recorded
  source tree but do not identify deployed bytes. No deployed UERANSIM
  executable or source archive was supplied.

## Exported hypotheses

- `evidence/hypotheses/` contains 13 unmodified DOT exports,
  `hypothesis_1.dot` through `hypothesis_13.dot`.
- `evidence/learner.log:230-234` starts round 13, reports `used all CEs.`,
  and records 7,854 equivalence queries. The log ends without a convergence
  or shutdown marker.
- H13 declares 17 state nodes (`s0` through `s16`) and 204 transitions. Its
  SHA-256 is
  `465fc8f4e5cba3715ec8745cbc81f9d24f5876fdb37d455f858f02f01815523a`.
- `evidence/hypotheses/hypothesis_13.dot:20-30` records initial transitions
  including Registration Request to Authentication Request,
  Registration Request GUTI to Identity Request, and initial-state Identity
  Response to `null_action`.

## H13 iterative refinement

- `analysis/derived/refinement/hypothesis_13_refinement.json` is the canonical
  refinement result generated from the archived H13 DOT with the
  `open5gs-nas` profile.
- Effective rounds contain 10, 13, 16, and 17 classes. Their split-parent
  counts are 2, 2, 3, and 1 respectively.
- Round 4 is the last effective refinement round. Round 5 produces no new
  partition and confirms convergence.
- All 17 final classes are singletons. The complete round relation is shown in
  `analysis/derived/refinement/hypothesis_13_round_refinement_flowchart.svg`.

## H13 iterative backtrace

- `analysis/derived/iterative_backtrace/hypothesis_13_all_binary_backtrace.json`
  is the schema-v2 canonical audit generated with the strict entry policy.
- The audit contains 12 binary child-class pairs: 8 strict pairs and 4
  non-strict pairs. Four pairs are independent entries and four are covered
  nodes reached from those entries.
- The participating graph contains six inter-round relations and two terminal
  relations, ending at two initial key differences.
- The integrated diagram is
  `analysis/derived/iterative_backtrace/hypothesis_13_all_binary_backtrace_flowchart.svg`.

## H13 signal-constrained cycle cover

- `analysis/derived/hypothesis_13_smp.dot` supplies 33 directed state-pair
  target edges. `evidence/hypotheses/hypothesis_13.dot`, after excluding
  `s2` and the synthetic start, supplies additional return transitions used
  only to close cycles.
- A route is retained only when it contains at least one exact output match
  (`authenticationRequest` or `securityModeCommand`). With simple cycles only,
  E009 and E033 are not coverable under this output-only rule, so the analyzer
  uses the approved fallback of joining two simple cycles at a shared state to
  form a closed walk. This produces 63 signal-valid candidates.
- Candidate lengths 2 through 12 have counts
  `6, 6, 6, 7, 2, 6, 5, 7, 9, 5, 4`, respectively.
- Exact lexicographic optimization first minimizes maximum cycle length, then
  route count, repeated transition uses, total length, and canonical candidate
  IDs. The optimum contains 14 routes with maximum length 9, total length
  64, and 29 repeated transition uses.
- The selected length distribution is three length-2 routes, four length-3
  routes, one length-4 route, two length-5 routes, one length-7 route, two
  length-8 routes, and one length-9 route. Twelve selected routes are simple
  directed cycles; two are composite closed walks.
- `analysis/derived/cycle_cover/hypothesis_13_cycle_cover.json` verifies
  33/33 target-edge coverage and the required-signal condition for every
  selected route. The corresponding report links 14 SVG figures. Each figure
  preserves the complete SMP graph and highlights exactly one selected route
  in its own color; closure-only edges from the original H13 are shown as
  same-color dashed transitions. No per-route DOT or PDF is retained.

## H13 cycle-cover input sequences

- For every selected route, the sequence exporter rotates the route to the
  first occurrence of its numerically smallest state and computes one shortest
  access prefix from `s0` on the original H13 after excluding `s2` and the
  synthetic start.
- The 14 route start states are, in C01-C14 order:
  `s1, s12, s13, s0, s1, s7, s12, s1, s0, s9, s4, s1, s1, s1`.
  The seven distinct access targets are all reachable.
- Expanding every merged SMP input produces per-route variant counts
  `2, 2, 2, 2, 1, 1, 2, 2, 1, 1, 2, 2, 4, 4`, totaling 28 lines.
  Each line contains its unique shortest prefix followed by the same concrete
  closed route repeated 10 times.
- The generated line lengths range from 21 to 91 input symbols. Simulation
  against the filtered original H13 verifies that every concrete transition
  exists and every line returns to its selected route start after all ten
  repetitions.
- `inputs/hypothesis_13_cycle_cover_repeat10.seq` contains 28 non-empty,
  single-space-delimited lines and 32,857 bytes. Its SHA-256 is
  `31a645f38976e61953c20c3ba99dc5af137e93926a2a6844d41f500b4f753f20`.

## Interrupted ending

- `analysis/derived/session-status-summary.json` inventories 863 core
  sessions: 862 have status `failed` and exit code 134; the final session is
  the only one still marked `running`.
- `evidence/final-session/session.json:6-12` records core session 863
  starting at `2026-07-31T02:44:34Z` with null `ended_utc` and null
  `exit_code`.
- External `raw/console.log:437414-437428` starts the second repetition of
  an in-progress query and ends after Authentication Response receives
  Security Mode Command. No causal interpretation is assigned to the
  truncation or exit-code pattern.

## Archive boundary

- `archive-manifest.json` records and hashes all 2,595 structured-run files
  (162,359,537 bytes) and the 17 selected learner-export files (3,616,956
  bytes).
- The structured-run snapshot content-tree SHA-256 is
  `02007630bef7fd64eafd8edab3fc0ea56a3831e753fb9f292a6446c0129b3a07`.
  The learner-export snapshot content-tree SHA-256 is
  `d69db1f559aea4d776c3d48eb007b47063a063ba3cd5fcf45b981e74891b9866`.
- Lock files, the empty `learnlib.log`, and task-generated SMP files were
  excluded from raw evidence.
