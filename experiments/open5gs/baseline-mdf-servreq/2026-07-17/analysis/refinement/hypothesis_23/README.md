# H23 状态分类与迭代细化

## 输入与方法

- 原始模型：`evidence/hypotheses/hypothesis_23.dot`
- 原始模型 SHA-256：`e0ff218edf17bcbc8af9cf5a4a07d530c3ed62ad0ffab45e150e9d475109d6e2`
- 配置：`open5gs-nas` 初始七类 D/N/NG/A/S/R/X
- 验证结果：28 个状态均具有相同的 12 个输入转移；模型通过确定性与完备性验证。
- signature：按照全局输入顺序，将转移目标替换为**上一轮**类别标签；输出动作不参与 signature。

初始分类和逐轮 signature 的唯一事实来源是 [hypothesis_23_refinement.json](hypothesis_23_refinement.json)（SHA-256 `48d0f62835f9b6c4481ac8f7ee37a12f45f3b74020cf668e64f4f34472553d37`）。

## 细化结果

| 阶段 | 类别数 | 实际拆分父类 |
| --- | ---: | --- |
| 初始 | 7 | — |
| 第 1 轮 | 16 | A、D、N、NG |
| 第 2 轮 | 20 | A1、N1 |
| 第 3 轮 | 25 | D1、D2、N1、N2、N5 |
| 第 4 轮 | 27 | A4、NG1 |
| 第 5 轮 | 27 | 无；固定点 |

最后有效细化轮是第 4 轮；第 5 轮确认收敛。最终 27 类中，只有 `D1 = {s0, s26}` 仍含两个状态，其余类别均为单状态。

## 图件

`rounds/` 中每张 SVG 都有同名 DOT 源文件。分类图完整展示该轮分区；溯源图只展示发生实际拆分的父类，并在边上标注该子类相对于基准 signature 的差异输入。

| 轮次 | 完整状态分类 | 细化溯源 |
| --- | --- | --- |
| 初始 | [初始七类](rounds/hypothesis_23_round_0_state_classification.svg) | — |
| 第 1 轮 | [分类](rounds/hypothesis_23_round_1_state_classification.svg) | [溯源](rounds/hypothesis_23_round_1_refinement_trace.svg) |
| 第 2 轮 | [分类](rounds/hypothesis_23_round_2_state_classification.svg) | [溯源](rounds/hypothesis_23_round_2_refinement_trace.svg) |
| 第 3 轮 | [分类](rounds/hypothesis_23_round_3_state_classification.svg) | [溯源](rounds/hypothesis_23_round_3_refinement_trace.svg) |
| 第 4 轮 | [分类](rounds/hypothesis_23_round_4_state_classification.svg) | [溯源](rounds/hypothesis_23_round_4_refinement_trace.svg) |
| 第 5 轮 | [分类](rounds/hypothesis_23_round_5_state_classification.svg) | [固定点](rounds/hypothesis_23_round_5_refinement_trace.svg) |

另外，分析器生成的完整轮次流图是 [round-refinement flowchart](hypothesis_23_round_refinement_flowchart.svg)。严格二分类全量回溯共 30 个候选对子，其中 16 个满足单 signature 位置入口条件、14 个为非严格对子；按公共尾链覆盖去重后有 10 个独立入口。产物包括 [主报告](backtrace/hypothesis_23_all_binary_backtrace_report.md)、[流程图 SVG](backtrace/hypothesis_23_all_binary_backtrace_flowchart.svg)、[可编辑 DOT](backtrace/hypothesis_23_all_binary_backtrace_flowchart.dot)、[PDF](backtrace/hypothesis_23_all_binary_backtrace_flowchart.pdf) 和 [审计 JSON](backtrace/hypothesis_23_all_binary_backtrace.json)。

回溯审计使用 schema v2：分类对子本身无方向，独立入口按 canonical
子类顺序建立固定 branch A/B，并依据每一步实际目标状态连续追踪全部
成员。详尽的初始分组、逐轮 signature 与差异列表见
[canonical refinement report](hypothesis_23_refinement_report.md)；二分类入口、覆盖、排除、公共尾链、固定 A/B 轨迹和终点可观察输出审计已统一合并进主报告。
