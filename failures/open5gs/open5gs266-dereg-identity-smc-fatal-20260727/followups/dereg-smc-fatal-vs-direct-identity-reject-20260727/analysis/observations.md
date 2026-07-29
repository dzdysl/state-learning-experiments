# Observations for run 20260727T052451Z-3196bc78

## Run structure

- `run-manifest.json` lists five Open5GS core sessions, all with `exit_code: 134`.
- The complete selected input material contains 24 files and 389,037 bytes. It is copied under `raw/20260727T052451Z-3196bc78/` with its original relative structure preserved.
- `statelearner_trace.jsonl` contains 24 trace rows across four sequence IDs: 1, 4, 7 and 8.
- `statelearner_sht_debug.jsonl` contains deregistration / GUTI refresh debug events only for sequence 1 and sequence 4.

## Sequence 1 and 4: deregistration then identity

- `statelearner_trace.jsonl` lines 1-8 and 9-16 both execute:
  `registrationRequest authenticationResponse securityModeComplete registrationComplete deregistrationRequest identityResponse authenticationResponse securityModeComplete`.
- Both runs output:
  `authenticationRequest securityModeCommand registrationAccept configurationUpdateCommand null_action authenticationRequest securityModeCommand null_action`.
- At the post-deregistration `identityResponse`, trace lines 6 and 14 show a new Authentication Request with `auth_request_ksi_value: 1`, RAND present, and AUTN present.
- At the following `authenticationResponse`, trace lines 7 and 15 show the AMF response as `securityModeCommand`, not `authenticationReject`.
- At the final `securityModeComplete`, trace lines 8 and 16 return `null_action` with `trace_timeout_no_downlink`.
- `raw/open5gs/core-session-001/core.log:226-228` and `raw/open5gs/core-session-003/core.log:226-228` both contain `Invalid OGS_NAS_5GS[69]`, `gmm_state_security_mode: should not be reached`, and a backtrace.

## Sequence 7 and 8: direct identity

- `statelearner_trace.jsonl` lines 17-20 and 21-24 both execute:
  `identityResponse authenticationResponse securityModeComplete registrationComplete`.
- Both runs output:
  `authenticationRequest authenticationReject null_action null_action`.
- Trace lines 18 and 22 decode the downlink as NAS message type 88 with `auth_reject_has_eap: absent`.
- `raw/open5gs/core-session-005/core.log:207-213` records the first direct-identity attempt: Identity response, SUCI, MAC failure, and Authentication reject.
- `raw/open5gs/core-session-005/core.log:229-235` records the second direct-identity attempt with the same pattern.

## Source correlation

- Open5GS v2.6.6 sends Authentication Reject from `src/amf/gmm-sm.c` only on authentication-processing failure paths: failed `gmm_handle_authentication_response()`, unrecovered UE Authentication Failure, T3560 exhaustion, or AUSF authenticate / confirmation failure. The final send path is `src/amf/nas-path.c:523-540`.
- For sequence 1 and 4, AMF did not reject the post-deregistration `authenticationResponse`; it accepted authentication confirmation far enough to send `securityModeCommand`, then hit the known `gmm_state_security_mode` fatal path on the next input.
- For sequence 7 and 8, AMF logged `MAC failure` before Authentication Reject. These rows are evidence for the direct-identity authentication rejection path, not for the post-deregistration SMC fatal path.
- Open5GS v2.6.6 builds the AUSF authentication request serving network name from `amf_ue->nr_tai.plmn_id` in `src/amf/nausf-build.c:51-53`.
- The NGAP InitialUEMessage handler decodes UserLocationInformation into `ran_ue->saved.nr_tai` and `ran_ue->saved.nr_cgi` in `src/amf/ngap-handler.c:557-560`; it does not copy those values to `amf_ue->nr_tai` at that point.
- Registration Request handling later copies `ran_ue->saved.nr_tai` and `ran_ue->saved.nr_cgi` into `amf_ue->nr_tai` and `amf_ue->nr_cgi` in `src/amf/gmm-handler.c:288-289`.
- Identity Response handling starts AUSF authentication in `src/amf/gmm-sm.c:1231-1263`, but it does not perform the Registration Request `nr_tai` copy and does not reset `amf_ue->nas.message_type`.

## Authentication-vector cross-check

The run uses UE key `465B5CE8B199B49FAA5F0A2EE238A6BC`, OPC `E8ED289DEBA952E4283B54E88E6183CA`, PLMN `999/70`, and SNN `5G:mnc070.mcc999.3gppnetwork.org` from `config/open5gs-ue.yaml` and `Corelearner_seqTest_pack/scripts/init_db.py`. Recomputing 5G-AKA RES* from those values matches every UE-side `AuthenticationResponse` in the trace, including the two direct-identity rejects.

| Trace row | Sequence | RAND | UE RES* | Recomputed with SNN `mnc070/mcc999` |
| --- | --- | --- | --- | --- |
| 7 | seq 1 post-dereg | `6B1381EA66738CDA589CEA53E102FD27` | `1693D0FFB40CAAAE8C973897AAE7CF6F` | match |
| 15 | seq 4 post-dereg | `85203B61C1FB8E81B05C3ABD80DE3AA7` | `5C383C085427BAA568AC8F8FFBDC764D` | match |
| 18 | seq 7 direct identity | `CD9BA291982A1B265C45827FE5DAA496` | `F8BC0B2398B389A60ABF0C5BA309AEE6` | match |
| 22 | seq 8 direct identity | `70A227D522B9E7ADA3E2FBEA91F532F6` | `2430886D13B3A6EEA88DE03AF261B70B` | match |

For direct identity, `core-session-005/core.log:209-212` and `:231-234` print three hexdumps after `MAC failure`: the UE RES*, AMF's HXRES* recomputed from that RES*, and AMF's stored HXRES*. The AMF recomputed HXRES* matches `SHA256(RAND || UE_RES*)[16..31]`; the stored HXRES* does not. Recomputing RES*/HXRES* with SNN `5G:mnc000.mcc000.3gppnetwork.org` matches the stored AMF HXRES* exactly:

| Sequence | Stored AMF HXRES* in log | HXRES* recomputed with SNN `mnc000/mcc000` |
| --- | --- | --- |
| seq 7 direct identity | `1E605A10743974C51D30CC1DEB181D55` | match |
| seq 8 direct identity | `D0CE22E85E306955CF36FB5ECDE7DE3B` | match |

This cross-check supports the following source-correlated explanation: the direct-identity path lets AMF start AKA before `amf_ue->nr_tai` has been initialized from NGAP location information by Registration Request handling. The AMF/AUSF authentication vector is therefore generated with the wrong serving network name, while UE/SUL computes RES* with the configured serving PLMN.

## Interpretation boundary

This run should not be summarized as simply "no Authentication Reject". It contains both outcomes:

- post-deregistration identity sequence: no Authentication Reject, then SMC fatal;
- direct identity sequence after reset: Authentication Reject after MAC failure.

The difference is the preceding UE/core context, not the literal suffix `identityResponse authenticationResponse` alone. With a preceding Registration Request, `amf_ue->nr_tai` is initialized and authentication can pass, exposing the later `nas.message_type = 69` SMC fatal. Without that preceding Registration Request, direct Identity Response starts AKA with an uninitialized/default `amf_ue->nr_tai`, so authentication fails first.
