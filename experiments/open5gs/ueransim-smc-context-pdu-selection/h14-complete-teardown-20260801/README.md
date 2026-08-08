# H14 active-learning completion with teardown anomaly

## Status

The learner completed 14 rounds and exported a final 18-state hypothesis. The
enclosing run finalizer then reported Open5GS launcher exit status 134 while
stopping the test core. Under the repository outcome rule, this is a successful
active-learning run with a recorded teardown anomaly, not a failed experiment.

## Scope

This follow-up freezes the later material delivered for the same active-learning
run `20260730T054606Z-60d7ce19`. It supersedes the parent's H13-only conclusion
about the eventual learner output while preserving the original H13 frozen
boundary and its analyses.

## Confirmed facts

- `learner.log` reports `Rounds []: 14`, 1,005 membership queries, 23,742
  equivalence queries, and `States in final hypothesis: 18` at
  `2026-08-01T22:49:54+08:00`.
- `evidence/hypotheses/hypothesis_14.dot` and
  `raw/learner-export/learnedModel.dot` have the same SHA-256:
  `19fe51296d78d000d2ab7e9106d4555b0064ca7f5e7b78c608c39f05db5ca8d0`.
- The final run manifest says `finalizer_failed`. The end of the complete
  console log records Open5GS launcher exit status 134 followed by
  `RUN_CONTEXT finalizer exited non-zero`.
- The exit-134 condition is retained as a teardown anomaly. It must not be
  used to relabel this completed active-learning result as failed.

## Material

- The complete 6,710-file structured run is an external verified snapshot;
  see [artifacts.yaml](artifacts.yaml) and [archive-manifest.json](archive-manifest.json).
- The complete 20-file learner export is retained under
  [raw/learner-export](raw/learner-export) because it is 9,127,201 bytes.
- The final hypothesis and final runtime manifest/provenance are direct small
  evidence under [evidence](evidence).

## Navigation

- Objective facts: [analysis/observations.md](analysis/observations.md)
- H14 SMP 派生图与可复现渲染记录：[analysis/rendering.md](analysis/rendering.md)
- H14 分层基础环覆盖：
  [报告](analysis/cycle-cover/base-report.md)
- 基础组和额外组各自只执行一次 Graphviz 布局；组内独立 SVG 共用固定底图，仅当前路线的
  具体边颜色不同。因此 `S008` 与 `S036` 的节点、边和标签位置可直接对照。
- H14 基础组彩色环总览：[analysis/cycle-cover/base-overlay.svg](analysis/cycle-cover/base-overlay.svg)
- H14 额外短环与自环增强路线：
  [报告](analysis/cycle-cover/extra-report.md)
- 基础输入：[基础组](inputs/base-cycle-repeat10.seq)；
  额外输入：[额外组](inputs/extra-cycle-repeat10.seq)
- 基础组运行时验证：37/37 条逻辑序列、1,656/1,656 步与 H14 一致；运行时退出码
  134 另行归档。见 [运行记录](../h14-base-runtime-20260804/README.md)
  与 [ngKSI 寄存器候选](../h14-base-runtime-20260804/analysis/register-inference/summary.md)。
- Runtime/source provenance: [provenance.yaml](provenance.yaml)
- Original H13 frozen boundary:
  [open5gs266-smc-context-h13-interrupted-20260730](../open5gs266-smc-context-h13-interrupted-20260730/README.md)
- Series index: [README.md](../README.md)

## Next action

Keep the representative exit-134 diagnosis with the matching Open5GS source
snapshot as lifecycle evidence; it does not invalidate the completed final
model.
