# Open5GS baseline MDF / Service Request learning run — 2026-07-17

## Status

Catalogued and cross-checked.  H23 state classification and iterative refinement are complete; AB-cluster and preliminary register-inference analysis remain.  The supplied configuration and runtime JAR were not available, so their exact hashes remain unresolved.

## Scope

This record preserves the baseline Open5GS v2.6.6 MDF/Service Request learning run whose learner began at 2026-07-17 20:19:56 and whose supplied core log ends at 2026-07-19 12:13:25.  It is an ordinary experiment record, not a failure record.

## Confirmed correspondence

- `baseLine_mdf_servReq_0716_log/Open5GS.log` is the learner export: it contains 23 original hypothesis DOT files and its `learner.log` records rounds 1–23.
- `open5gs_5gc.log` is the matching core log: it names Open5GS daemon v2.6.6, starts at `07/17 00:15:13.520`, and ends at `07/19 12:13:25.296`, matching the learner export’s run window.
- `statelearner_trace.jsonl` and `my_database.sqlite` are later exports of the same observed query relation, not a second learning run: 10,205 of 10,312 distinct terminal trace inputs occur in `queryNew_Open5GS`, with zero output mismatches.  The remaining 107 trace inputs need only cache-boundary review, not reinterpretation as contradictory behavior.
- The imported learner baseline is retained at `projects/open5gs-state-learning` commit `a53346596db9520c4971f0f51721af330675ef50`, tag `baseline/2026-07-16-servreq`; its tag message explicitly identifies the 2026-07-16 Service Request snapshot.

## Material intentionally excluded

`D:\downloads\Tencent Files` is a QQ application-data tree and `D:\downloads\resources` is a Clash Verge resource tree.  Their timestamps, contents, and file types do not identify them as experiment evidence; neither was copied or indexed as an artifact.

## Next action

1. Recover or locate the exact `sample.yaml` and runtime learner JAR used for this run, then fill their hashes in `provenance.yaml`.
2. Use the immutable DOT evidence for state refinement; derive any simplified/rerendered figures only under `analysis/`.
3. Start AB-cluster and register hypotheses from trace fields only after source-correlating with the tagged learner/SUL and `open5gs_old` snapshot.

## Completed H23 refinement

The H23 Open5GS-NAS refinement reaches 27 classes after four effective rounds and converges in the fifth.  The canonical report, complete partition diagrams, per-round split-provenance diagrams, and full binary backtrace are indexed in [analysis/refinement/hypothesis_23/README.md](analysis/refinement/hypothesis_23/README.md).

## Follow-up runs

- [Security-mode re-entry and shutdown — 2026-07-25](followups/security-mode-reentry-shutdown-20260725/README.md): one interactive sequence from a later learner/UERANSIM-SUL runtime follows the H23 path `s0→s1→s3→s10→s11→s12→s14→s27→s27` exactly, then exposes a pending-T3560/removed-NG-context condition and an Open5GS thread-destroy abort during controlled shutdown. The follow-up has independent provenance and does not alter the baseline run’s conclusions.
- [Security-context KSI re-entry — 2026-07-25](followups/security-context-ksi-reentry-20260725/README.md): a separate later run follows `s0→s1→s3→s10→s8→s1→s3→s5` and successfully returns `registrationAccept`. It documents why a retained UE KSI 0 does not prevent a newly created AMF context from completing the next KSI-1 exchange.
