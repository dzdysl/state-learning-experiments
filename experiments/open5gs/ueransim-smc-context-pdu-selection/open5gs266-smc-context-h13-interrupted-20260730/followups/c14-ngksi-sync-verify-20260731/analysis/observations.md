# Observations

## Frozen run boundary

- `raw/run-manifest.json` identifies interactive run
  `20260731T092950Z-e8ae5e2f`, started at
  `2026-07-31T09:29:50.279507Z`, and still records status `running`.
- `raw/open5gs-session.json` also records status
  `running`, with no end time or exit code.
- The frozen run has 9 files and 800,076 bytes. `archive-manifest.json` records
  every member SHA-256 and confirms byte identity with the supplied directory.
- `raw/statelearner_trace.jsonl` has 182 complete records: sequence IDs 1
  and 2 each contain symbol indexes 1-91.

## Input and abstract-output identity

- The final input vector of trace sequence 1 is byte-for-symbol identical to
  parent input line 27; sequence 2 is identical to parent input line 28.
- The replacement sequences have the same 91 abstract outputs as the original
  run's sequence IDs 56 and 58. There are 0/182 abstract-output differences.
- Repetitions 2-10 have no missing, extra, duplicated, retransmitted, reordered,
  or unstable abstract output on the aligned nine-edge route.

## KSI event chain

Each repetition contains two accepted Security Mode Commands. The first is
followed by `securityModeComplete`, then deregistration clears the current UE
context before the first `registrationRequestGUTI`. The second is followed by
`securityModeReject`, then the second `registrationRequestGUTI`.

For both lines over repetitions 2-10:

- 36/36 SMC values equal the `ue_sec_ctx_ngksi` observed on the immediately
  following UE message.
- 36/36 SMC values equal the `registration_ksi_value` on the corresponding
  following `registrationRequestGUTI`.
- In all 18 first-GUTI samples after deregistration,
  `ue_has_sec_ctx=false`, yet the GUTI template retains the preceding accepted
  SMC KSI.
- In all 18 second-GUTI samples, the template carries the second accepted SMC
  KSI.

For each of input lines 27 and 28, repetitions 2-10 are:

```text
first accepted SMC / post-clear first GUTI:  2,4,6,1,3,5,0,2,4
second accepted SMC / second GUTI:           3,5,0,2,4,6,1,3,5
```

Representative trace locations are:

- line 27 repetition 2: trace lines 11-18;
- line 27 repetition 10: trace lines 83-90;
- line 28 repetition 2: trace lines 102-109; and
- line 28 repetition 10: trace lines 174-181.

The original run's corresponding GUTI template series were:

```text
first GUTI:   1,2,3,4,5,6,0,1,2
second GUTI:  2,3,4,5,6,0,1,2,3
```

Those values lag the newly accepted contexts. This is the concrete field-level
difference that the replacement run removes.

## Effective C14 replacement

Using original run lines 1-26 and replacement run lines 27-28 leaves all
abstract-stability totals unchanged. The eight aligned C14 KSI series from
lines 27-28 change classification from `+1 mod 7` to `+2 mod 7`. Therefore the
effective 78-series distribution is:

```text
+1 mod 7: 22
+2 mod 7: 16
constant 0: 24
constant 1: 14
piecewise increment-then-hold: 2
```

The black-box relations remain unchanged:

- `AuthenticationRequest.KSI = (declared registration KSI + 1) mod 7` for
  81/81 applicable samples; and
- `SecurityModeCommand.KSI = latest AuthenticationRequest.KSI` for 306/306
  samples.

## Source observations

At UERANSIM revision `6b1bcc070331ac2d0443a255afbf5466413fd0b7`:

- `src/ue/nas/mm/security.cpp:279-286` promotes the selected non-current
  context and immediately calls `updateRegistrationRequestGutiNgKsi()` before
  exposing the accepted SMC to the learner.
- `src/ue/app/state_learner.cpp:1673-1678` copies both `tsc` and `ngKsi` into
  the reusable GUTI Registration Request under its dedicated mutex.
- `src/ue/app/state_learner.cpp:838-862` updates GUTI, NSSAI and TAI at send
  preparation, but does not read the current context or alter KSI.

In Open5GS v2.6.6 snapshot `open5gs_old`:

- `src/amf/nausf-handler.c:108-113` advances AMF KSI modulo the available
  0-6 values before sending Authentication Request.
- `src/amf/gmm-build.c:368-369,437-438` copies that AMF KSI into
  Authentication Request and Security Mode Command.

The source paths explain the observed values, but the supplied run does not
capture byte-level UE deployment identity.
