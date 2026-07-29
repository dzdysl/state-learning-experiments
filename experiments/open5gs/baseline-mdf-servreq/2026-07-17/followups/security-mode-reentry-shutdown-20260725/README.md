# Security-mode re-entry and shutdown follow-up — 2026-07-25

## Status

Source-correlated and catalogued from one interactive run. The eight observed Mealy outputs reproduce the corresponding H23 path exactly. The operator entered `exit`, after which Open5GS exits with status 134 during controlled shutdown. This run alone does not establish that the eighth NAS input directly crashed the AMF or identify which Open5GS worker failed to stop.

## Relationship to the parent experiment

This is a follow-up to the [2026-07-17 baseline MDF / Service Request experiment](../..), not a new top-level failure record. It uses the same Open5GS v2.6.6 core version but a later learner/UERANSIM-SUL commit and a dirty runtime build. Its observations and hypotheses therefore remain scoped to this follow-up.

## Run scope

- Run ID: `20260725T072113Z-1d6f4338`
- Mode: interactive
- One eight-symbol input sequence
- One Open5GS core session
- Core session result: `failed`, exit code `134`
- Complete supplied run copied byte-for-byte under `raw/20260725T072113Z-1d6f4338/`

The exact input is preserved in `inputs/interactive-sequence.seq`. It is an observed test input, not a minimized or independently reproduced `reproducer.seq`.

## Confirmed result

The observed input/output relation is:

```text
IN : registrationRequest authenticationResponse securityModeReject registrationRequest authenticationResponse registrationRequest authenticationResponse securityModeComplete
OUT: authenticationRequest securityModeCommand null_action authenticationRequest securityModeCommand authenticationRequest securityModeCommand null_action
```

On the parent H23 model this is exactly:

```text
s0 → s1 → s3 → s10 → s11 → s12 → s14 → s27 → s27
```

The core log then records, in order:

1. two NAS MAC verification failures during the repeated registration/authentication portion;
2. `No Security Context` for the final Security Mode Complete;
3. gNB connection removal during interactive-console cleanup;
4. a T3560 Security Mode Command retry after the NG context had been removed;
5. SIGTERM to the Open5GS test core;
6. an assertion in `ogs_thread_destroy()` after a thread remained running for the five-second wait loop.

The `gmm_state_security_mode` expectation failure is a logged non-fatal expectation for `OGS_NOTFOUND`; the recorded abort is the later thread-destroy assertion.

The final Security Mode Complete is not rejected because its NAS container is missing: the UE trace records an integrity/ciphered SHT 4 message with KSI 1 and a present NAS container. It receives no downlink because the immediately preceding two NAS MAC failures set AMF field `mac_failed=1`. The v2.6.6 validity macro requires `mac_failed==0`; it then logs `No Security Context` and breaks before it parses the container, clears T3560, or sends a response.

## Provenance limitations

- The run records learner commit `11453a35...`, but also records `git.dirty=true`; the exact dirty diff was not supplied.
- The exact runtime configuration contents were not supplied. Only its path and SHA-256 are known.
- The runtime Open5GS executable hash was not supplied. Source correlation uses the local v2.6.6 snapshot at tag commit `499c70d8...`; the relevant source files match that commit after newline normalization.
- This is one observation. No minimal sequence, timing threshold, or cleanup-order comparison has yet been run.

## Next actions

1. Re-run the exact sequence from a clean learner/SUL commit with the exact configuration copied into the run record.
2. Compare immediate `exit` with a delay longer than T3560 after step 8.
3. Compare the current `gNB → UE → core` cleanup order with a shutdown that keeps NG context available until the core has terminated.
4. Minimize the repeated registration/authentication suffix while preserving the MAC failures and `No Security Context`.
5. Capture per-thread stacks or a core dump when `ogs_thread_destroy()` times out; the supplied backtrace identifies the asserting caller, not the worker that remained active.
