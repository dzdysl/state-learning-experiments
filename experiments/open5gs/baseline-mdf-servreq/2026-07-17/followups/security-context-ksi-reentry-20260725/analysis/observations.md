# Observations

## Exact result and H23 correspondence

The console (`raw/20260725T082217Z-b0edabac/raw/console.log`) records this seven-symbol word and result:

```text
registrationRequest authenticationResponse securityModeReject registrationRequestGUTI identityResponse authenticationResponse securityModeComplete
authenticationRequest securityModeCommand null_action identityRequest authenticationRequest securityModeCommand registrationAccept
```

Simulation against the immutable parent `evidence/hypotheses/hypothesis_23.dot` gives the same output and final state `s5`:

| Step | Input | Output | H23 transition | Relevant UE trace fields |
|---:|---|---|---|---|
| 1 | `registrationRequest` | `authenticationRequest` | `s0 → s1` | no context; AuthReq KSI 0 |
| 2 | `authenticationResponse` | `securityModeCommand` | `s1 → s3` | no context; SMC KSI 0 |
| 3 | `securityModeReject` | `null_action` | `s3 → s10` | current KSI 0; SHT 2; UL SQN 1 |
| 4 | `registrationRequestGUTI` | `identityRequest` | `s10 → s8` | current KSI 0; SHT 1; declared KSI 0; UL SQN 2 |
| 5 | `identityResponse` | `authenticationRequest` | `s8 → s1` | current KSI 0; SHT 2; UL SQN 3; new AuthReq KSI 1 |
| 6 | `authenticationResponse` | `securityModeCommand` | `s1 → s3` | current KSI 0; SHT 2; UL SQN 4; new SMC KSI 1 |
| 7 | `securityModeComplete` | `registrationAccept` | `s3 → s5` | current KSI 1; SHT 4; UL SQN 1 |

The two values that answer the KSI question are direct trace fields: second Authentication Request has `auth_request_ksi_value=1` (step 5); second SMC has `smc_ksi_value=1` (step 6); final SMC has `ue_sec_ctx_ngksi=1` and `ue_sec_ctx_uplink_count_sqn=1` (step 7).  No `NAS MAC verification failed` line occurs in the supplied core log.

## AMF source-correlated flow

Source basis: `D:\state-learning-lab\sources\open5gs_old\open5gs`, Open5GS tag `v2.6.6`, commit `499c70d8be63e5f87c2d1c2bcd4e521d50ce1a6b`.

1. The Security Mode Reject makes `gmm_state_security_mode` transition to the exception state (`src/amf/gmm-sm.c:1718-1724`); the core log then removes AMF-UE 1.
2. The GUTI Registration Request creates AMF-UE 2.  `gmm_handle_registration_request()` copies its declared UE KSI 0 into `nas.amf.ksi` when a KSI is present (`src/amf/gmm-handler.c:220-231`).
3. After the new authentication vector arrives, `amf_nausf_auth_handle_authenticate()` increments that AMF KSI before sending Authentication Request (`src/amf/nausf-handler.c:108-115`).  Therefore the observed KSI 1 is an intentional rollover from the declared 0, not an AMF reuse of 0.
4. Authentication Request is sent before NAS security has been established.  On any protected uplink received while `security_context_available==0`, `nas_5gs_security_decode()` forces its local integrity/cipher/new-context flags to zero (`src/amf/nas-security.c:125-129`).  It consequently does not enter the MAC calculation and `mac_failed=1` assignment at lines 156-173.
5. The second Authentication Response can thus reach `gmm_handle_authentication_response()`, which validates RES* against the fresh vector and sends authentication confirmation (`src/amf/gmm-handler.c:832-875`).  On confirmation success the AMF enters security mode (`src/amf/gmm-sm.c:1580-1591`) and sends the KSI-1 SMC.
6. The outgoing protected SMC establishes AMF-side NAS-security availability (`src/amf/nas-security.c:95-115`).  The final KSI-1 SMC is then integrity checked; it passes, so `gmm_state_security_mode` clears T3560 and handles it (`src/amf/gmm-sm.c:1670-1709`), producing Registration Accept.

## UERANSIM/SUL source-correlated flow

Source basis: historical source at learner commit `11453a35e1c1dba5b49ae2d487f9e48ff0b493a8`, inspected with `git show` from `D:\state-learning-lab\projects\open5gs-state-learning`.

- The learning wrapper sends `authenticationResponse` through `mm->sendNasMessage(authenticationResponse)` (`src/ue/app/state_learner.cpp:1089-1098`), which uses the current NAS context when one exists (`src/ue/nas/mm/messaging.cpp:137-175`).  The step-6 trace confirms that this current context remains KSI 0.
- The 5G-AKA handler deliberately comments out the normal “ngKSI already in use” rejection (`src/ue/nas/mm/auth.cpp:342-352`) and creates/replaces `m_nonCurrentNsCtx` from the received Authentication Request (`:382-389`).  After step 5, that pending context is KSI 1 while current remains KSI 0.
- The SMC searches current and non-current contexts by the SMC KSI (`src/ue/nas/mm/security.cpp:100-108`).  For a non-current match it resets the UL counter and copies that context to `m_currentNsCtx` (`:219-230`).  That is exactly the KSI-1 / UL-SQN-1 final trace.

## Shutdown boundary

The final Registration Accept is observed at `08:22:47Z`.  The operator later enters `exit`; the run manifest marks the core session failed with exit code 134 after controlled gNB/UE/core shutdown.  This post-query shutdown issue is preserved in the raw bundle but is not used to reinterpret the successful NAS output.

## Limits

The runtime records `git.dirty=true`, and the exact dirty diff, configuration contents, AMF debug logs, packet capture, and runtime binary hash are absent.  The conclusion above is nevertheless directly supported by the captured NAS fields and the named source paths; it does not claim a byte-identical reconstruction of the running executable.
