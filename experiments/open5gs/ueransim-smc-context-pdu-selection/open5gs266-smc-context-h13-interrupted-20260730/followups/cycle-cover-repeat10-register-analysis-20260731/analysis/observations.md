# Observations

## Run identity and archive boundary

- `evidence/run-provenance.json` identifies run
  `20260731T064710Z-74bcf3ee`, mode `multi_sequence`, starting at
  `2026-07-31T06:47:10.448426Z`.
- `evidence/run-manifest.json` is finalized and contains 58 core sessions.
  Every session is recorded as `failed` with exit code 134; the final session
  ended at `2026-07-31T07:20:39Z`.
- External `raw/console.log:10,8537` contains Sequence 1 start and Sequence 28
  completion markers. It contains 28 start markers, 28 completion markers,
  and 1,521 `Sequence N step` records.
- The same console contains 58 runtime-contract warnings for
  `scripts/start_core.sh`: expected SHA-256
  `840ef0af...`, actual SHA-256 `031fbab1...`. The supplied run manifest has
  an empty `warnings` array.
- The complete frozen run contains 184 files and 22,626,148 bytes. Its
  content-tree SHA-256 is recorded in `artifacts.yaml` and every member hash
  is recorded in `archive-manifest.json`.

## Runtime and source identity

- `evidence/run-provenance.json` records learner commit
  `11453a35e1c1dba5b49ae2d487f9e48ff0b493a8`, a dirty runtime status, and
  sequence-runner JAR SHA-256
  `4ac7eda3981970478a3214f1a6d45d3dc99f140f7cd2bd4f7257acce9fb86116`.
- The runtime configuration SHA-256 is
  `63955fcee7cb9fbf69c41a57a1a7a119ba7a4436c92c3eccf83bb139c0c5d46f`.
- The UERANSIM/SUL source correlation is inherited from the parent record:
  commit `693678935ff2faf0d818d988de72e2a0505879df`, `src/` tree
  `516aef65c8edb34af33e432e49c3c3c39e2c3614`, medium confidence because no
  deployed binary or source archive was captured.

## Input and trace alignment

- `inputs/hypothesis_13_cycle_cover_repeat10.seq` is byte-identical to the
  parent input. It contains 28 lines and has SHA-256
  `31a645f38976e61953c20c3ba99dc5af137e93926a2a6844d41f500b4f753f20`.
- External `statelearner_trace.jsonl` contains 1,521 records grouped into 28
  complete sequences. Every final `sequence_inputs` vector matches exactly
  one `.seq` line.
- External `statelearner_trace.cleaned.jsonl` contains only 379 records and
  covers the first 13 input lines. It is retained in the external snapshot but
  was not used as the complete analysis source.
- Removing the access prefix and repetition 1 leaves 146 cycle-edge series.
  Repetitions 2-10 contribute 1,314 aligned samples. No aligned sample is
  missing.

## Abstract outputs

- All 1,521 trace outputs equal the output labels obtained by simulating the
  same inputs on the parent H13 DOT.
- All 146 repetition-2-through-10 edge series have one stable abstract output
  label. Missing, extra, duplicate, retransmitted, or reordered abstract
  messages were not observed in the aligned series.
- `null_action` samples use trace note `trace_timeout_no_downlink`; those
  outputs match H13 and were not shifted to a neighboring edge.

## Decoded field series

- No downlink field-key set changes between repetitions 2-10 on the same edge.
- Forty decoded downlink field series are nonconstant. Every one is either
  `auth_request_ksi_value` or `smc_ksi_value`.
- In the original run, the 78 downlink Authentication Request and Security
  Mode Command KSI series were classified as `+1 mod 7` ×30, `+2 mod 7` ×8,
  constant 0 ×24, constant 1 ×14, and piecewise increment-then-hold ×2.
- For all 81 aligned Authentication Requests triggered by a Registration
  Request carrying KSI 0-6, the output satisfies
  `AuthenticationRequest.KSI = (registration_KSI + 1) mod 7`.
- All 306 aligned Security Mode Commands have the same KSI as the latest
  preceding Authentication Request in the same sequence.
- Authentication Request, Registration Accept, Configuration Update Command,
  and protected NAS raw bytes vary. Those cryptographic or count-bearing byte
  changes are not classified as decoded-field anomalies.

## C08 line 14 phase change

- `evidence/c08-line14-trace-lines-385-420.jsonl` is an exact excerpt of
  external `statelearner_trace.jsonl:385-420`, covering repetitions 2-10 of
  C08 line 14.
- SMC KSI over repetitions 2-10 is
  `1,2,3,4,4,4,4,4,4`.
- Registration Request GUTI KSI is
  `1,2,3,3,3,3,3,3,3`, and the corresponding Authentication Request KSI is
  `2,3,4,4,4,4,4,4,4`.
- At original trace line 399, repetition 5 edge 3 records
  `ue_has_sec_ctx=false`. Repetition 5 edge 4 also has no current UE security
  context. From repetition 6 onward, edge 1 starts without a current context,
  while the following Security Mode Reject edge records a recreated current
  context with KSI 4.

## UERANSIM/SUL source observations

At revision `693678935ff2faf0d818d988de72e2a0505879df`:

- `src/ue/nas/mm/base.cpp:355-380` sends a connected-to-idle transition with a
  pending registration procedure into abnormal registration handling.
- `src/ue/nas/mm/register.cpp:989-1030` increments `m_regCounter` up to 5 and
  clears current and non-current NAS security contexts when the counter reaches
  5.
- `src/ue/app/state_learner.cpp:838-866` refreshes the reusable GUTI
  Registration Request KSI only when a current NAS security context exists;
  it has no KSI-reset `else` branch.
- `src/ue/nas/mm/auth.cpp:382-385` stores a received Authentication Request
  KSI in the non-current NAS security context.
- `src/ue/nas/mm/security.cpp:183-193,279-281` selects a context for a Security
  Mode Command and promotes the selected non-current context to current.
- `src/ue/nas/enc.cpp:107-108` advances the uplink NAS count after a
  non-replayed protected send, and `src/ue/types.hpp:451-455` implements an
  8-bit SQN increment with a 16-bit overflow increment on wrap.

These are UERANSIM source facts. This follow-up does not map the observed AMF
behavior to AMF source variables.

## C14 line 27/28 replacement

- Interactive run `20260731T092950Z-e8ae5e2f` replays parent input lines 27
  and 28 exactly. Its 182 abstract outputs are identical to the original
  run's sequence IDs 56 and 58.
- Repetitions 2-10 contain 36 accepted SMC-to-next-current-context pairs and
  36 accepted SMC-to-following-GUTI-template pairs; every pair has equal KSI.
- Eighteen first-GUTI samples occur after current-context clearing and still
  retain the preceding accepted SMC KSI.
- Replacing only these two lines reclassifies eight C14 KSI series from
  `+1 mod 7` to `+2 mod 7`. The effective 78-series distribution is therefore
  `+1 mod 7` ×22, `+2 mod 7` ×16, constant 0 ×24, constant 1 ×14, and
  piecewise increment-then-hold ×2.
- The replacement evidence, source boundary and immutable copy are in
  [the C14 event-sync follow-up](../../c14-ngksi-sync-verify-20260731/README.md).
