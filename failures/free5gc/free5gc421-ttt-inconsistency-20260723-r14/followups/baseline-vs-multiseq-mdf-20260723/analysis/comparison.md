# Comparison result

## Scope

The comparison uses `inputs/test-50.seq` and the two complete JSONL traces under `raw/`. A semantic record consists of:

- session, sequence, socket-session, symbol and step identifiers;
- sequence input/output prefixes;
- raw command, abstract input, modifier, abstract output, security modifier and replay flag;
- trace note.

Concrete NAS bytes are deliberately excluded from semantic equality because authentication material varies between runs.

## Counts

| Metric | Baseline | multiSeq-MDF |
|---|---:|---:|
| Sessions | 50 | 50 |
| Trace records | 470 | 470 |
| Semantic differences | 0 | 0 |
| `identityRequest` | 49 | 49 |
| `authenticationRequest` | 163 | 163 |
| `null_action` | 258 | 258 |
| `trace_timeout_no_downlink` | 206 | 206 |
| `next_input_before_downlink` | 52 | 52 |

The multiSeq console contains 203 `Timeout occured` messages and 100 attempts to read the obsolete `/opt/CoreCrisis/Corelearner/core.properties` path. Its manifest remained `running` and the archive contains no core-network raw log. Those are run-provenance defects, not semantic differences between the two traces.

## Interpretation

This comparison did not reproduce the original fatal inconsistency. It also did not exercise a behaviorally distinct MDF recovery path: all 470 abstract records are the same. The high number of timeout and next-input-before-downlink records remains consistent with an asynchronous downlink attribution problem.

The result must therefore be described as “behavior-equivalent regression with unresolved timeouts,” not as a fix verification.
