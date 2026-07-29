# Observations

## Run identity and time window

- `evidence/learner.log` starts the learner at `Jul 17, 2026 8:20:38 PM`, uses `TTTLearnerMealy` and `MealyWpMethodEQOracle`, and begins round 1.  The preceding logger initialization is at `8:19:56 PM`.
- The same file records `Starting round 23` at `Jul 19, 2026 11:03:18 AM` and `used all CEs.` at `Jul 19, 2026 11:30:58 AM`.
- The external `open5gs_5gc.log` begins with `Open5GS daemon v2.6.6` and the `sample.yaml` configuration path at `07/17 00:15:13.520`; its final supplied record is `07/19 12:13:25.296`.

## Exported model evidence

- `evidence/hypotheses/` contains 23 unmodified exported DOT files, `hypothesis_1.dot` through `hypothesis_23.dot`.
- `learner.log` records 27 states before round 23.  The last exported DOT explicitly declares `s0` through `s27` (28 displayed nodes); this count difference is preserved as an observation and must be resolved from learner semantics before any claim about the final number of behavioral states.
- At the end of round 22, `learner.log` records 1,684 membership queries and 54,310 equivalence queries.  It then starts round 23 and reports `used all CEs.` without a further exported hypothesis.

## Trace and SQLite cross-check

- `statelearner_trace.jsonl` has 170,045 valid JSONL records.  Its records have no time field; their sequence IDs span 2 through 20,920, and 20,750 terminal sequence records yield 10,312 distinct terminal input words.
- `my_database.sqlite` passes `PRAGMA integrity_check`.  `query_Open5GS` has 3,429 rows and `queryNew_Open5GS` has 10,320 rows.
- Of the 10,312 distinct terminal input words in the trace, 10,205 appear in `queryNew_Open5GS`; every matched word has identical output.  The remaining 107 trace words are absent from that table.  For the shared 3,379 words between the two database tables, results also have zero differences.

## Scope exclusions

- `D:\downloads\Tencent Files` contains QQ application data (3,426 files, 424,127,288 bytes) and was excluded.
- `D:\downloads\resources` contains Clash Verge executable/GeoIP resources dated 2025-07 and was excluded.
