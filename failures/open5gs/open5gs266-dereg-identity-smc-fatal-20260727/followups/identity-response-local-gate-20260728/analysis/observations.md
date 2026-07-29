# Observations for run 20260728T083613Z-23b95c25

## Run evidence

- The frozen run contains 8 files and 49,712 bytes. All selected files are copied under `raw/20260728T083613Z-23b95c25/` with their relative paths preserved.
- `statelearner_trace.jsonl:8` records `identityResponse -> null_action`, with `ue_side.concrete_hex: ""`, empty UE-side fields, empty downlink hex, and `note: "local_null_action"`.
- `raw/console.log:58-61` records the executor sending the abstract `identityResponse` command and receiving raw `null_action` from the RRC/SUL.
- `raw/open5gs/core-session-001/core.log:219-226` records an InitialUEMessage carrying the second Registration Request with a 5G-S-TMSI unknown to AMF, creation of one AMF-UE, and an Identity Request.
- The same core log has no `Identity response` entry after line 226. Line 227 records `Retransmission of Identity-Request failed. Stop retransmission` at `16:44:35.421`, 15 seconds after the Identity Request exchange began.
- `core.log:228-232` records UE Context Release and removal of the AMF-UE. The frozen `session.json` still has `status: running` and no exit code, so it is not final process-lifecycle evidence.

## Learner/UERANSIM source correlation

The run provenance fixes learner/UERANSIM commit `11453a35e1c1dba5b49ae2d487f9e48ff0b493a8`.

- `src/ue/nas/mm/register.cpp:278-301` has the original “Registration Accept only in MM_REGISTERED_INITIATED” check commented out and dispatches the accept using the last Registration Request.
- `src/ue/nas/mm/register.cpp:353-357` switches the UE to `MM_REGISTERED_NORMAL_SERVICE` and `U1_UPDATED` upon Initial Registration Accept.
- `src/ue/app/state_learner.cpp:925-929` sends `registrationRequestGUTI` directly after `prepareGutiRegistration()` and performs no MM-state transition.
- `src/ue/app/state_learner.cpp:1084-1098` sends `securityModeReject` and `authenticationResponse` directly and performs no MM-state transition.
- `src/ue/nas/mm/identity.cpp:21-65` constructs and stores an Identity Response when Identity Request is received; `sendNasMessage(resp)` is commented out, and the function performs no MM-state transition.
- `src/ue/app/state_learner.cpp:1144-1156` sends an Identity Response only when `m_mmSubState != MM_REGISTERED_NORMAL_SERVICE`; the else branch emits `notify_response("null_action")`.

## Open5GS source correlation

The run uses Open5GS snapshot `open5gs_old`, v2.6.6 commit `499c70d8be63e5f87c2d1c2bcd4e521d50ce1a6b`.

- The AMF log shows that it accepted and processed the second `registrationRequestGUTI` far enough to send Identity Request.
- Because the trace contains no uplink NAS bytes for the following abstract `identityResponse` and the AMF log contains no Identity Response, this run has no direct evidence of how AMF would process an Identity Response at step 8.
