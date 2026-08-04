# H14 基础环覆盖运行时验证

## 状态

运行器完整执行了 H14 基础组 `repeat-10` 输入的 37 条逻辑序列；完整原始
轨迹中的 1,656 个抽象 I/O 步均与 H14 原始 DOT 的确定性模拟一致。基于同一完整
轨迹的 ngKSI 观察寄存器候选推断已经完成。Markdown 以 H13 四列格式摘要 52 个具体
DOT 边组；完整审计工作簿保留 23 个 `cycle_id`、37 个 `expand` 变体、90 条循环—边
使用、逐公式候选和协调证据，见[寄存器推断摘要](analysis/derived/register_inference/h14-base-ngksi-signal-summary.md)
与 [Excel 审计工作簿](analysis/derived/register_inference/h14-base-ngksi-signal-details.xlsx)。
面向阅读的摘要与工作簿将 `unknown/<reason>` 显示为 `unknown`、将
`r_i[ngksi_uplink]` 显示为 `r_i`，并省略输入更新的观察来源括号；完整原始枚举与证据仍保留在 JSON。
该结果不扩展为对未覆盖边、额外短环组或 AMF 内部寄存器的证明。

运行期间的核心网启动/停止不稳定：80 个核心会话均以退出码 134 结束，其中
43 次启动直接遇到 UPF 端口占用。此异常不构成已观察 I/O 与 H14 的反例，但使
本次验证不适合用来评估干净重启、时序或性能。

## 范围

这是 H14 完成记录的独立运行时验证 follow-up，而非原主动学习运行的一部分。
其精确输入与 H14 基础组输入同哈希，运行 ID 为
`20260804T034905Z-2b7f2823`。运行器记录的源树处于 dirty 状态，且实际
Open5GS 二进制没有版本或哈希；因此 H14 行为一致性结论仅依赖冻结的输入、
完整 trace 与 H14 DOT，不把参考源码当作实际部署源码。

## 已确认结论

- `evidence/statelearner_trace.jsonl` 含 37 个会话、1,656 条 I/O 记录；每个会话
  唯一对应 `inputs/hypothesis_14_base_cycle_cover_repeat10.seq` 的一行。
- 排除 `s2` 后，从 `s0` 在 H14 原始 DOT 上逐输入模拟，37/37 条序列、
  1,656/1,656 步的下行抽象输出完全相同；详见
  [模型核对结果](analysis/derived/h14-base-trace-model-check.json)。
- `raw/console.log` 的行 9677–9687 记录 `Primary total: 37`、
  `Primary kept: 37`、`Failed primary ids: []` 与 `Final kept sequences: 37`；
  它确认所有逻辑序列完成。
- `statelearner_trace.cleaned.jsonl` 只保留 498 行，覆盖前 17 条逻辑序列；
  它是派生筛选视图，不能用于本记录的完成度、覆盖率、一致性或寄存器推断。

## 材料与导航

- 精确输入：[inputs/hypothesis_14_base_cycle_cover_repeat10.seq](inputs/hypothesis_14_base_cycle_cover_repeat10.seq)
- 原始小证据：[evidence](evidence)
- 原始 trace 无损物化清单：[analysis/derived/register_inference/h14-base-trace-materialization.json](analysis/derived/register_inference/h14-base-trace-materialization.json)
- 外部完整快照与哈希：[artifacts.yaml](artifacts.yaml)
- 实际事实：[analysis/observations.md](analysis/observations.md)
- ngKSI H13 式摘要：[analysis/derived/register_inference/h14-base-ngksi-signal-summary.md](analysis/derived/register_inference/h14-base-ngksi-signal-summary.md)
- ngKSI 完整 Excel 审计：[analysis/derived/register_inference/h14-base-ngksi-signal-details.xlsx](analysis/derived/register_inference/h14-base-ngksi-signal-details.xlsx)
- 机器可读候选：[analysis/derived/register_inference/h14-base-ngksi-signal-candidates.json](analysis/derived/register_inference/h14-base-ngksi-signal-candidates.json)
- 运行时异常假设与边界：[analysis/hypotheses.md](analysis/hypotheses.md)
- 运行/工具溯源：[provenance.yaml](provenance.yaml)
- H14 模型与基础组路线：[../h14-learning-complete-finalizer-failed-20260801/README.md](../h14-learning-complete-finalizer-failed-20260801/README.md)

## 可复现命令

在本记录目录执行。schema v3 的 `--report` 与 `--workbook` 都是必需输出；Markdown 摘要
不能替代 Excel 的完整审计材料。

```powershell
$tool = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\infer_cycle_ngksi_regions.py'
$node = 'C:\Users\dzdysl\.cache\codex-runtimes\codex-primary-runtime\dependencies\node\bin\node.exe'
$nodeModules = 'C:\Users\dzdysl\.cache\codex-runtimes\codex-primary-runtime\dependencies\node\node_modules'
D:\anaconda3\python.exe $tool `
  --config analysis/derived/register_inference/h14-base-ngksi-signal-inference.yaml `
  --output analysis/derived/register_inference/h14-base-ngksi-signal-candidates.json `
  --report analysis/derived/register_inference/h14-base-ngksi-signal-summary.md `
  --workbook analysis/derived/register_inference/h14-base-ngksi-signal-details.xlsx `
  --workbook-node $node `
  --workbook-node-modules $nodeModules
```

## 后续

在使用额外短环组或下一轮重复环分析前，先修复核心网停止后的端口回收问题，
并冻结实际 Open5GS 二进制和运行时脚本哈希。后续所有 trace 推断只读取完整的
`statelearner_trace.jsonl`；`statelearner_trace.cleaned.jsonl` 不作为分析输入。
