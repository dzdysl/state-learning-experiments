# Hypotheses

## Primary causal explanation — high confidence

Free5GC v4.2.1 accepts `IdentityResponse` during Authentication and starts a new T3560 authentication transaction without stopping prior timers on the examined path. Interleaved A/B/C retransmissions can bypass the UERANSIM learner's one-PDU `retransbuf` check whenever the retransmitted PDU differs from the most recently accepted PDU. The asynchronous socket response can then be consumed by a later input step without request correlation.

The controlled reproduction directly confirms overlapping retransmissions, Authentication-state mismatches for the later deregistration inputs, and recurrence of the same B PDU at later steps. The full evidence chain is in `followups/t3560-retransmission-reproduction-20260724/analysis/root-cause.md`.

## Remaining uncertainty

- The original failure's learner commit and JAR hash are unknown, so exact historical binary identity cannot be established.
- The reproduction JAR provenance is dirty and does not include a UERANSIM executable hash.
- The reproduction does not contain UE/gNB raw receive logs or pcap; console/trace disagreement at two steps is therefore consistent with the 800 ms versus 1500 ms windows and FIFO attribution, but is not proven event-by-event.
- Reset timing and SQLite cache corruption are no longer the leading explanation. The 50-sequence comparison provides no positive evidence for either, but it does not independently exclude them from the historical run.
