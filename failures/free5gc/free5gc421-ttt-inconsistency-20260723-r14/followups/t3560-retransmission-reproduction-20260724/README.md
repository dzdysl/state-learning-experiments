# T3560 retransmission misattribution reproduction

Status: mechanism reproduced; fix not tested.

This follow-up deliberately creates three authentication transactions A/B/C and then sends repeated `deregistrationRequest` inputs. It isolates whether an old authentication retransmission can be consumed as the output of a later deregistration step.

## Result

- Free5GC emitted interleaved T3560 retries for A, B and C.
- Each deregistration input reached the AMF in Authentication state and produced a state-mismatch log rather than a new authentication procedure.
- The trace recorded the earlier B authentication PDU again at later deregistration steps.
- The learner console and trace disagreed at two steps, consistent with their different wait windows and lack of a shared step identifier.

The complete reasoning, timeline and source references are in `analysis/root-cause.md`.

## Layout

- `inputs/reproducer.seq`: exact controlled sequence.
- `raw/run-20260724T094326Z-d078312d.zip`: complete frozen run package.
- `provenance.yaml`: run, JAR, config and source revisions.
- `analysis/root-cause.md`: evidence chain and remaining uncertainty.
