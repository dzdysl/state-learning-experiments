# Security-context KSI re-entry follow-up — 2026-07-25

## Status

Catalogued and source-correlated from one interactive run.  This word successfully completes a second registration after a Security Mode Reject: the final Security Mode Complete receives `registrationAccept`.  The later Open5GS exit status `134` occurs during the operator's `exit` cleanup, after the query result, and is not evidence that the final NAS step failed.

## Exact observed word

```text
IN : registrationRequest authenticationResponse securityModeReject registrationRequestGUTI identityResponse authenticationResponse securityModeComplete
OUT: authenticationRequest securityModeCommand null_action identityRequest authenticationRequest securityModeCommand registrationAccept
```

The parent H23 model predicts the same relation and path:

```text
s0 → s1 → s3 → s10 → s8 → s1 → s3 → s5
```

## Confirmed KSI explanation

The second authentication is not an attempt to reuse KSI 0 at the AMF.  The UE's GUTI Registration Request advertises its retained KSI 0, after which Open5GS first copies that value into the newly created AMF-UE and then increments the AMF KSI while preparing the new authentication vector.  The observed second Authentication Request and Security Mode Command therefore both carry KSI 1.

UERANSIM retains current KSI 0 until it receives that second Security Mode Command; the second Authentication Request creates a non-current/pending KSI 1.  Consequently, the second Authentication Response is indeed protected with the old current KSI 0 (trace: SHT 2, KSI 0, UL SQN 4).  It does not cause an AMF MAC failure in this run because the new AMF-UE has not yet established a NAS security context.  Open5GS explicitly disables integrity processing in that condition, so it can decode and validate RES* without checking the stale outer MAC.

Once the AMF's RES* check succeeds, it sends the KSI-1 Security Mode Command.  UERANSIM selects its non-current KSI-1 context, resets its uplink counter, makes it current, and sends Security Mode Complete with KSI 1 and UL SQN 1.  That matches the AMF's just-created KSI-1 keys, so the AMF accepts it and returns Registration Accept.

## Scope and provenance

- Run ID: `20260725T082217Z-b0edabac`; interactive mode; one seven-symbol word.
- Complete supplied bundle (11 files, 114,570 bytes) is preserved under `raw/20260725T082217Z-b0edabac/`.
- This is a follow-up to the [2026-07-17 baseline MDF / Service Request experiment](../..), not a new top-level failure record.
- Runtime provenance records learner/UERANSIM-SUL commit `11453a35...`, but `git.dirty=true`; its unsupplied dirty diff and runtime configuration contents limit byte-exact reconstruction.

## Next actions

1. Repeat this word with AMF NAS-security debug logging to confirm the expected no-MAC-check branch directly at runtime.
2. Compare it with the earlier `security-mode-reentry-shutdown-20260725` word, which adds a further protected registration/authentication cycle and reaches the persistent `mac_failed` latch.
3. Re-run the controlled `exit` cleanup separately; it is outside the successful query word.
