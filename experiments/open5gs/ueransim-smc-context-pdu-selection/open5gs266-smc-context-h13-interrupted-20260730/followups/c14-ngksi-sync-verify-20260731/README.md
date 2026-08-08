# C14 regReqGUTI ngKSI event-sync verification — 2026-07-31

## Status

Completed behavioral verification for H13 C14 input lines 27 and 28. The new
interactive run `20260731T092950Z-e8ae5e2f` exactly replays those two 91-step
sequences. All 182 abstract outputs match the earlier C14 samples and no
same-edge output-label instability appears in repetitions 2-10.

The KSI behavior conforms to the `regReqGUTI` event-sync change:

- after each accepted Security Mode Command, the next UE message observes the
  promoted current context with the SMC KSI;
- the first `registrationRequestGUTI` after deregistration has no current
  context but retains that accepted KSI in the reusable template; and
- after the second accepted SMC, the next `registrationRequestGUTI` carries
  the newly promoted KSI.

Across repetitions 2-10 of both lines, SMC-to-current-context matches are
36/36 and SMC-to-following-GUTI-template matches are 36/36. This run therefore
supersedes the earlier C14 line 27/28 samples in the effective repeat analysis.
The earlier run and its raw evidence remain immutable.

## Scope and replacement boundary

- Source input: parent
  `inputs/hypothesis_13_cycle_cover_repeat10.seq`, lines 27-28.
- Superseded samples: run `20260731T064710Z-74bcf3ee`, trace sequence IDs 56
  and 58.
- Replacement samples: run `20260731T092950Z-e8ae5e2f`, trace sequence IDs 1
  and 2.
- Analysis window: access prefix and repetition 1 are setup; repetitions 2-10
  are aligned without shifting messages.

The supplied run manifest and core-session manifest still say `running`.
Because the two requested sequences are complete and the source directory may
remain interactive, its nine files were frozen byte-for-byte under `raw/`
at the analysis boundary. The frozen copy is 800,076 bytes, and all source/copy
hashes match.

## Version confidence

The observed behavior matches UERANSIM commit
`6b1bcc070331ac2d0443a255afbf5466413fd0b7` and tag
`runtime/2026-07-31/regreq-guti-ngksi-event-sync`. That revision updates the
template immediately after current-context promotion and no longer refreshes
KSI in the send-time GUTI preparation path.

The supplied runtime provenance identifies the Java sequence runner, not the
deployed UE binary or `src.zip`. No UE binary/source-archive hash or commit
marker is present in this run, so deployment identity is not byte-level
confirmed. The conclusion is behavioral conformance with medium-high source
correlation, not proof that a particular UE binary was deployed.

## Navigation

- Series index: [README.md](../../../README.md)
- Runtime and source provenance: [provenance.yaml](provenance.yaml)
- Frozen-copy member hashes: [archive-manifest.json](archive-manifest.json)
- Exact two-line input: [inputs/c14-lines-27-28.seq](inputs/c14-lines-27-28.seq)
- Parent 28-line input copy:
  [inputs/hypothesis_13_cycle_cover_repeat10.seq](inputs/hypothesis_13_cycle_cover_repeat10.seq)
- Objective observations: [analysis/observations.md](analysis/observations.md)
- Source-correlated interpretation: [analysis/hypotheses.md](analysis/hypotheses.md)
- Machine-readable replacement summary:
  [analysis/derived/c14-replacement-summary.json](analysis/derived/c14-replacement-summary.json)
