# Observations

## Evidence boundary

- The complete source bundle is preserved under `raw/20260725T072113Z-1d6f4338/`. It contains one finalized interactive run, one core session, eight state-learner trace rows, and no manifest warnings (`run-manifest.json` lines 3–28).
- The core session ran from `2026-07-25T07:21:15Z` to `07:23:17Z`, is marked `failed`, and records exit code `134` (`raw/open5gs/core-session-001/session.json` lines 9–13).
- Runtime provenance records learner/SUL commit `11453a35...`, `git.dirty=true`, a dirty-status hash, the runtime JAR SHA-256, and the configuration SHA-256 (`run-provenance.json` lines 7–21). The dirty diff and configuration contents are not in the bundle.

## Exact abstract path

The console preserves the eight-symbol input and output lines at `raw/console.log` lines 60–61. Each of the eight rows in `statelearner_trace.jsonl` records the same cumulative relation.

| Step | Input | Output | H23 transition | UE-side security fields |
|---:|---|---|---|---|
| 1 | `registrationRequest` | `authenticationRequest` | `s0→s1` | no security context; outer SHT 0 |
| 2 | `authenticationResponse` | `securityModeCommand` | `s1→s3` | no current security context; outer SHT 0 |
| 3 | `securityModeReject` | `null_action` | `s3→s10` | KSI 0; outer SHT 2; UL SQN 1 |
| 4 | `registrationRequest` | `authenticationRequest` | `s10→s11` | KSI 0; outer SHT 1; UL SQN 2 |
| 5 | `authenticationResponse` | `securityModeCommand` | `s11→s12` | KSI 0; outer SHT 2; UL SQN 3 |
| 6 | `registrationRequest` | `authenticationRequest` | `s12→s14` | KSI 0; outer SHT 2; UL SQN 4 |
| 7 | `authenticationResponse` | `securityModeCommand` | `s14→s27` | KSI 0; outer SHT 2; UL SQN 5 |
| 8 | `securityModeComplete` | `null_action` | `s27→s27` | KSI 1; outer SHT 4; UL SQN 1 |

The H23 edges are direct evidence in the parent `evidence/hypotheses/hypothesis_23.dot` at lines 31, 49, 72, 151, 169, 175, 205, and 364. The observed word therefore follows `s0→s1→s3→s10→s11→s12→s14→s27→s27` without an abstract-output mismatch.

The trace also establishes that the two `null_action` outputs are one-second learner timeouts with no downlink (`note=trace_timeout_no_downlink`), not explicit NAS `null_action` packets.

This archived run contains one, not two, concrete outgoing Security Mode Complete PDU: `statelearner_trace.jsonl` line 8 (`mm_message_type=94`). It contains three received Security Mode Commands (steps 2, 5, and 7). The UERANSIM source builds and stores a candidate Security Mode Complete after every received command, but `state_learner.cpp:1115–1142` emits it only when the `securityModeComplete` input is selected. A second actual SMC transmission would require another run bundle or a second trace row, neither of which is present in `D:\downloads\runs`.

## Core timeline

All source line numbers below refer to the preserved `raw/open5gs/core-session-001/core.log`.

- Lines 201–207: the first Registration Request creates RAN UE 1 and AMF UE 1.
- Lines 208–213: Security Mode Reject cause 24 is handled, followed immediately by UE Context Release and removal of both the RAN UE and AMF UE.
- Lines 214–220: the next Registration Request creates RAN UE 2 and AMF UE 2 for the same SUCI.
- Lines 221–225: steps 6 and 7 each produce `NAS MAC verification failed`; step 6 is nevertheless logged as a Registration Request in `gmm_state_security_mode`.
- Line 226: the final Security Mode Complete reaches the security-mode state but is rejected with `No Security Context`.
- Lines 227–229: at `15:23:12.192`, interactive cleanup has stopped the gNB, so AMF removes the gNB UE and gNB.
- Lines 230–231: at `15:23:12.680`, a Security Mode Command retry reports that its NG context has already been removed and logs an expectation failure.
- Lines 232–233: SIGTERM arrives at `15:23:12.969`.
- Lines 234–243: at `15:23:17.970`, the five-second thread-destroy wait ends in `ogs_assert_if_reached()` and an abort backtrace.

The operator entered `exit`. The raw console independently records `Bye.` and the cleanup order—UE socket, nr-gnb, nr-ue, then Open5GS core—at lines 63–71.

## Open5GS v2.6.6 source correlation

Source basis: `D:\state-learning-lab\sources\open5gs_old\open5gs`, tag `v2.6.6`, commit `499c70d8be63e5f87c2d1c2bcd4e521d50ce1a6b`. The cited working-copy files match that commit after newline normalization.

- `src/amf/gmm-sm.c:1718–1724` maps Security Mode Reject to `gmm_state_exception`. On exception entry, `gmm-sm.c:2217–2235` clears all UE timers and sends a normal UE-context release.
- `src/amf/nas-security.c:151–173` advances the AMF uplink count, computes the NAS MAC, and sets `amf_ue->mac_failed=1` on mismatch, matching core-log lines 221 and 225.
- `src/amf/context.h:300–306` defines `SECURITY_CONTEXT_IS_VALID()`: it requires `security_context_available==1`, `mac_failed==0`, and an available UE KSI. In this source snapshot, the only AMF assignment to `mac_failed` is the `=1` assignment in `nas-security.c`; no AMF code path clears it back to zero. Thus either MAC warning at lines 221 or 225 is sufficient to make the final Security Mode Complete fail the validity gate.
- `src/amf/gmm-sm.c:1725–1747` handles a Registration Request while already in `gmm_state_security_mode` and starts authentication again. This matches the step-6 Registration Request log.
- `src/amf/gmm-sm.c:1670–1684` checks for integrity protection and a valid AMF security context before clearing T3560. When the context is invalid, it logs `No Security Context` and exits that message case without reaching `CLEAR_AMF_UE_TIMER`.
- The final SMC has outer SHT 4, UE KSI 1, and a NAS message container (`statelearner_trace.jsonl` line 8). It is therefore not rejected by the later container-presence check in `gmm-handler.c:961–1022`: the early `gmm-sm.c:1676–1678` validity gate prevents that handler from being called. The observed `null_action` follows because this branch only logs and `break`s; it sends neither a GMM reject nor Registration Accept.
- `src/amf/gmm-sm.c:1777–1793` handles T3560 expiry by retransmitting Security Mode Command. `src/amf/nas-path.c:554–567` returns `OGS_NOTFOUND` if the RAN/NG context has already been removed.
- `lib/core/ogs-errno.h:77–82` defines `OGS_ERROR=-1` and `OGS_NOTFOUND=-5`. At `gmm-sm.c:1791–1792`, `ogs_expect(r == OGS_OK)` logs the observed error, but `ogs_assert(r != OGS_ERROR)` does not abort for `OGS_NOTFOUND`.
- `lib/core/ogs-log.h:120–126` confirms that `ogs_expect` only logs. The process abort seen in this run instead matches `lib/core/ogs-thread.c:109–134`: wait up to five seconds, then call `ogs_assert_if_reached()` if the thread is still marked running.

The fatal text says “after 3 seconds,” while the implementation uses a five-second deadline; the timestamps `15:23:12.969→15:23:17.970` agree with the implementation.

## Learner and UERANSIM/SUL source correlation

Source basis: `D:\state-learning-lab\projects\open5gs-state-learning`, commit `11453a35e1c1dba5b49ae2d487f9e48ff0b493a8`, inspected with `git show`.

- `Corelearner_seqTest_pack/README.md:64–68` defines interactive input as one symbol or a space-separated sequence, confirming the syntax preserved in `inputs/interactive-sequence.seq`.
- `Corelearner_seqTest_pack/mylearner/src/main/java/org/example/corelearner/devices/Open5GSSUL.java:104–211` sends each symbol to UERANSIM, waits one second for an abstract response, and maps socket timeout to `null_action`.
- `src/ue/app/state_learner.hpp:159–173` maps `registrationRequest` to the stored IMSI Registration Request and maps the other four symbols to their concrete message types.
- `src/ue/app/state_learner.cpp:1079–1142` sends Security Mode Reject cause 24, refreshes Authentication Response from UERANSIM’s stored message, and sends a stored Security Mode Complete only when a UE-side security context exists.
- `src/ue/nas/mm/messaging.cpp:137–239` protects later NAS messages with UERANSIM’s current security context; for Registration Request it selects different wrapping based on connection state. This is consistent with the trace moving from SHT 2 at step 3 to SHT 1 at step 4, then SHT 2 at steps 5–7.
- `src/ue/nas/mm/security.cpp:212–230` installs the non-current context selected by a new Security Mode Command as the current UE context and resets its uplink count. Lines 243–279 build and store Security Mode Complete, including the prior Registration Request in a NAS message container when required. This matches step 8’s KSI 1, UL SQN 1, SHT 4, and present NAS container.
- `Corelearner_seqTest_pack/mylearner/src/main/java/org/example/corelearner/Learner.java:473–488` invokes cleanup on `exit`; `Corelearner_seqTest_pack/mylearner/src/main/java/org/example/corelearner/core/CoreSUL.java:496–502` fixes its order as gNB, UE, then core. The observed cleanup has that order.

## What the evidence does not show

- No core failure or process exit occurs at step 8 itself. The final step completes as a learner timeout around `07:23:07Z`; gNB removal begins about five seconds later during console cleanup.
- The log does not identify which Open5GS thread remains running. The abort backtrace is for the thread-destroy caller, not the stuck worker.
- The run does not prove that the pending T3560 event caused the later thread to remain active. It proves only their temporal ordering and the exact source paths that generated the messages.
- Because the learner/SUL runtime was dirty and the exact configuration contents and core binary hash are absent, source correlation is strong for the recorded behavior but not a byte-exact runtime reconstruction.
