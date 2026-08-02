# Register candidates and explanations

## Interpretation policy

AMF variables below are abstract logical registers inferred from black-box
input/output behavior. They are not claimed to be real AMF member names.
UERANSIM explanations use the source revision recorded in `provenance.yaml`.

## AMF KSI candidate

Let `r_ksi` denote an unknown AMF-side logical KSI register.

When a Registration Request declares an available KSI `k` in 0-6, the
strongest observed edge rule is:

```text
r_ksi' = (k + 1) mod 7
AuthenticationRequest.KSI = r_ksi'
```

This fits 81/81 aligned samples. When the input declares KSI 7, candidate
behavior is either continued modular increment from the current logical
register or reset-to-zero followed by later increments:

```text
r_ksi' = (r_ksi + 1) mod 7
```

or, at a reset boundary:

```text
r_ksi' = 0
```

The Security Mode Command behaves as a copy:

```text
SecurityModeCommand.KSI = latest AuthenticationRequest.KSI
```

This fits 306/306 aligned samples. Confidence is high as an external
input/output relation and deliberately unassigned to an AMF source variable.

## Whole-cycle recurrences

- C01, C02, C03, C05, C07, C10, and C11 expose
  `r'=(r+1) mod 7` on at least one aligned KSI edge.
- C14 lines 25-28 expose `r'=(r+2) mod 7` in the effective analysis; lines
  27-28 use the event-sync replacement run. Each full route contains two
  authentication-state advances, so the two-step recurrence is consistent
  with two applications of the one-step candidate. Confidence: high.
- C04, C06, C08 line 13, C09, C12, and C13 expose constant 0 or constant 1
  series. A reset or fresh logical context before the same edge is the closest
  simple candidate. Confidence: medium; no AMF white-box check was performed.

## C08 line 14

The closest single-rule fit before the break is `r'=(r+1) mod 7`. The first
break occurs at repetition 5:

```text
SMC KSI:   1, 2, 3, 4, 4, 4, 4, 4, 4
closest:   +1 mod 7 through repetition 5, then r'=4
GUTI KSI:  1, 2, 3, 3, 3, 3, 3, 3, 3
Auth KSI:  2, 3, 4, 4, 4, 4, 4, 4, 4
```

The UERANSIM-side candidate is:

```text
attempt' = min(attempt + 1, 5)
if attempt' = 5:
    current_ctx' = null
    noncurrent_ctx' = null
```

Once the current context is absent, the reusable GUTI Registration Request
does not receive a new KSI and retains 3:

```text
if current_ctx exists:
    guti_template_ksi' = current_ctx.ksi
else:
    guti_template_ksi' = guti_template_ksi
```

Combining that UERANSIM behavior with the black-box relation gives:

```text
AuthenticationRequest.KSI = (3 + 1) mod 7 = 4
SecurityModeCommand.KSI = 4
```

This explains the observed hold at 4 without requiring AMF output
nondeterminism. Confidence is medium-high: the trace relation is exact and the
UERANSIM source path is direct, but the deployed UERANSIM byte identity remains
medium-confidence.

## UE uplink count candidate

For a current UE NAS security context, the simple per-protected-send candidate
is:

```text
ul_sqn' = (ul_sqn + 1) mod 256
if ul_sqn' = 0:
    ul_overflow' = (ul_overflow + 1) mod 65536
```

The nonconstant aligned values in C06 and C13 are consistent with a fixed
number of protected sends between samples followed by a context reset. This is
a UERANSIM register candidate supported by the recorded source revision, not
an AMF candidate.

## Unresolved runtime facts

The repeated core exit code 134 and `start_core.sh` runtime-contract mismatch
are outside this follow-up's register-analysis scope. They require a separately
bounded failure investigation before any causal claim is made.
