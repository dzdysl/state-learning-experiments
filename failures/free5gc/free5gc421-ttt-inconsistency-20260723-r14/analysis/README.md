# Analysis index

This directory contains analysis of the original TTT failure. Raw follow-up runs and their run-specific conclusions are kept under `../followups/`.

## Main findings

1. `hypothesis_13` predicts `null_action` for steps 7--9 of the frozen failure input.
2. None of the three observed output sequences equals that model prediction.
3. A 50-sequence baseline versus multiSeq-MDF comparison did not reproduce the fatal inconsistency and showed no abstract behavioral difference.
4. A controlled A/B/C authentication reproduction exposed old T3560 retransmissions being consumed by later deregistration steps.

## Files

- `observations.md`: facts tied to evidence, traces or historical source.
- `hypotheses.md`: causal explanation, confidence and unresolved provenance.
- `rendering.md`: reproducible DOT-to-SVG/PDF commands and hashes.
- `figures/hypothesis_13.svg`: unmodified rendering of the original model.
- `derived/hypothesis_13_smp.svg`: default simplified model.
- `derived/hypothesis_13_failure_query_smp.svg`: simplified model with the failed query highlighted.

## Follow-up records

- `../followups/baseline-vs-multiseq-mdf-20260723/`
- `../followups/t3560-retransmission-reproduction-20260724/`
