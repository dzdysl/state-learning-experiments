# Hypotheses

## Confirmed code path — the MAC-failure latch suppresses the final SMC response

This is a source-confirmed explanation for the final `null_action`, not a hypothesis. Both core-log MAC warnings execute `nas-security.c:169–173`, setting the current AMF UE's `mac_failed=1`. `context.h:300–304` then makes `SECURITY_CONTEXT_IS_VALID()` false. When the final, otherwise well-formed Security Mode Complete reaches `gmm_state_security_mode`, lines 1676–1678 log `No Security Context` and break without calling the SMC handler or sending any response. The source snapshot contains no subsequent assignment that clears `mac_failed` for that AMF UE.

The evidence bundle contains only one actual SMC transmit. If “two SMCs” refers to a second manual attempt, its run directory is needed to confirm that it hit the same latch rather than a different earlier guard.

## H1 — stale UE-side context explains the two MAC failures

**Confidence: medium-high.**

After Security Mode Reject, Open5GS enters its exception state and removes AMF/RAN UE context. UERANSIM, however, still reports a current UE security context with KSI 0 and increasing uplink SQN. The later protected Registration Request and Authentication Response are therefore constructed from UE-side context that need not match the newly created AMF UE context. This is the most direct explanation for the two recorded NAS MAC mismatches.

This should be tested by logging both sides’ KSI, NAS count, and selected integrity key identity at steps 4–7. The present trace contains UE-side values but not the AMF key material or complete AMF security-context lifecycle.

## H2 — repeated Registration Request leaves T3560 active after the final null action

**Confidence: high for the timer remaining active; medium for the larger causal role.**

The step-6 Registration Request is processed inside `gmm_state_security_mode` and starts another authentication/security-mode exchange. At step 8, the Security Mode Complete fails the AMF security-context validity check before `CLEAR_AMF_UE_TIMER(t3560)`. The T3560 retry about 5.5 seconds later is therefore expected from the source and timestamps.

This explains the post-sequence retry and `NG context has already been removed`. It does not by itself prove why a worker thread failed to terminate.

## H3 — cleanup order exposes the pending-timer/removed-NG-context window

**Confidence: high for exposure of the window; medium for contribution to exit 134.**

The `exit` cleanup path stops gNB before stopping the core. In this run, gNB removal precedes the T3560 retry by about 488 ms, so `nas_5gs_send_security_mode_command()` necessarily sees no valid NG context and returns `OGS_NOTFOUND`.

A useful controlled comparison is:

1. keep gNB alive for more than one T3560 interval after step 8;
2. stop the core before gNB; and
3. repeat the current cleanup order.

If only the current order produces exit 134, cleanup sequencing is a contributor. If all variants do, the stuck thread lies elsewhere.

## H4 — exit 134 is a shutdown failure, not the logged GMM expectation

**Confidence: high.**

`OGS_NOTFOUND` triggers `ogs_expect(r == OGS_OK)` logging but passes the following `r != OGS_ERROR` assertion. The fatal backtrace occurs 5.001 seconds after SIGTERM at `ogs_thread_destroy()` and exactly matches that function’s deadline assertion. The direct cause of exit 134 is therefore the shutdown-time assertion.

The identity and reason of the still-running thread remain unknown. Claiming an AMF timer-thread deadlock, queue drain failure, or sequence-triggered Open5GS defect would require per-thread stacks or a core dump.

## H5 — H23 behavior remains valid despite the shutdown anomaly

**Confidence: high for this one run.**

All eight abstract outputs and transitions match the parent H23 model, including the final `s27 --securityModeComplete/null_action→ s27` self-loop. The shutdown anomaly does not contradict the learned Mealy relation because it occurs after the query word completes and during environment teardown.

This conclusion is limited to the observed word. Repeated runs are needed before treating the output relation as deterministic under the newer learner/SUL runtime.
