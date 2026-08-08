# H14 基础环覆盖运行时验证

## 状态

运行器完整执行了 H14 基础组 `repeat-10` 输入的 37 条逻辑序列；完整原始
轨迹中的 1,656 个抽象 I/O 步均与 H14 原始 DOT 的确定性模拟一致。基于同一完整
轨迹的 ngKSI 观察寄存器候选推断已经完成。Markdown 以 H13 四列格式摘要 52 个具体
DOT 边组；完整公式树、循环局部候选、交集、联合拟合、迁移和反推样本保留在 JSON，见
[寄存器推断摘要](analysis/register-inference/summary.md)、
[机器可读候选](analysis/register-inference/candidates.json)与
[边分类叠加图](analysis/register-inference/edge-categories.svg)。
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
  唯一对应 `inputs/base-cycle-repeat10.seq` 的一行。
- 排除 `s2` 后，从 `s0` 在 H14 原始 DOT 上逐输入模拟，37/37 条序列、
  1,656/1,656 步的下行抽象输出完全相同；详见
  [模型核对结果](analysis/model-check.json)。
- `evidence/console.log` 的行 9677–9687 记录 `Primary total: 37`、
  `Primary kept: 37`、`Failed primary ids: []` 与 `Final kept sequences: 37`；
  它确认所有逻辑序列完成。
- `statelearner_trace.cleaned.jsonl` 只保留 498 行，覆盖前 17 条逻辑序列；
  它是派生筛选视图，不能用于本记录的完成度、覆盖率、一致性或寄存器推断。

## 相对稳定推断与前序反推

工具先按 `cycle_id` 分别构建假设性候选的循环局部最简树。精确交集非空时直接采用交集；交集为空时
合并这些循环的全部样本重新拟合。`E0073` 的联合拟合仍无精确候选，因此记录具体边级
`combined_sample_fit_failed`，顶层候选为空，但继续执行相对稳定推断迁移检验与前序反推。

相对稳定推断按完整有效信号上下文、`input` 和 `output` 合并单边区域；不适用已配置信号的 I/O 才只按
输入输出合并。H14 得到 3 个相对稳定推断：

- 无信号、`authenticationResponse/securityModeCommand`：`r'=r`；没有假设性目标。
- `{isInitMsg=0}`、`registrationRequest/authenticationRequest`：已观察 `s=0`
  分支为 `r'=ite(r<6,r+1,0)`，`s=1` 分支未观察。
- `{isInitMsg=0}`、`registrationRequestGUTI/authenticationRequest`：已观察 `s=0`
  分支为 `r'=ite(r_i=7,ite(r<6,r+1,0),ite(r_i<6,r_i+1,0))`，`s=1` 分支未观察。

迁移只在相同 `{s}/input/output` 分组中执行。9 条假设性目标边共有 22 个循环—边分区：4 个迁移成功，
17 个因目标为 `{isInitMsg=1}` 而没有同组相对稳定推断，只有 `E0073/S008` 迁移失败并进入
前序反推。这里不借用 `s=0` 的模型检验 `s=1` 目标。

第三个相对稳定推断首次产生 `derived_value_guard: r_i == 7` 后，工具立即激活动态偏好 `T=7`，作用于
脚本剩余生命周期中新生成的精确候选；既有候选不回排，配置也没有预设 `T`。后续若精确常数 `r'=7`
覆盖全部样本，它排在首位；含 `r_i==7` 分裂的候选其次；其他候选仍按原复杂度排序并全部保留。

完整迁移实例为 `E0073: s6 → s1, registrationRequest/authenticationRequest`，其待迁移相对稳定推断
绑定 `{isInitMsg=0}`，模型树为
`r'=ite(s=1,unknown,ite(r<6,r+1,0))`：

- `S008`、输入行 10（`V01`）、R3–R10：上行 KSI 为 7，前一下行 KSI 为 0，当前下行 KSI 为 0；
  映射观察区域为 `(0,{isInitMsg=0},[ngksi_uplink=7],0)`。代入模型树预测 1，8/8 个
  样本不匹配。
- `S036`、输入行 21/23（`V01/V03`）、R3–R10：上行 KSI 始终为 7，信号同为
  `{isInitMsg=0}`；映射观察区域的下行 KSI 依次为
  `5→6, 0→1, 2→3, 4→5, 6→0, 1→2, 3→4, 5→6`，16/16 个样本迁移成功。

S008 的完整链为
`E0016: s1→s0, deregistrationRequest/null_action` →
`E0002: s0→s6, registrationRequestGUTI/identityRequest` →
`E0073: s6→s1, registrationRequest/authenticationRequest`。前序反推保持 E0016 为 `r'=r`，只对最近的
无 KSI 下行边 E0002 求前像。E0002 自身上下文为 `{isInitMsg=1}`、数值输入为
`[ngksi_uplink=7]`，没有 KSI 下行；在全局观测值域 `{0,1,2,3,4,5,6,7}` 中，8 个样本的允许输出均为
`{6,7}`。六个精确候选全部保留，并因动态 `T=7` 排为：`r'=7`、`r'=6`、`r'=r+6`、`r'=r+7`、
`r'=r_i-1`、`r'=r_i`。

这些结果仍是带相对稳定推断迁移来源、E0016 保持假设和最近前序边归因的
`hypothetical_candidate`，不能确认 E0002 的真实实现更新，也不把 E0073 的联合拟合失败解释为源码事实。

## 材料与导航

- 精确输入：[inputs/base-cycle-repeat10.seq](inputs/base-cycle-repeat10.seq)
- 原始小证据：[evidence](evidence)
- 原始 trace 无损物化清单：[analysis/register-inference/trace-materialization.json](analysis/register-inference/trace-materialization.json)
- 外部完整快照与哈希：[artifacts.yaml](artifacts.yaml)
- 实际事实：[analysis/observations.md](analysis/observations.md)
- ngKSI H13 式摘要：[analysis/register-inference/summary.md](analysis/register-inference/summary.md)
- 边分类叠加图：[analysis/register-inference/edge-categories.svg](analysis/register-inference/edge-categories.svg)
- ngKSI Excel 循环—边审计（此前明确要求时生成，未随本次术语改名刷新）：[analysis/register-inference/audit.xlsx](analysis/register-inference/audit.xlsx)
- 机器可读候选：[analysis/register-inference/candidates.json](analysis/register-inference/candidates.json)
- 运行时异常假设与边界：[analysis/hypotheses.md](analysis/hypotheses.md)
- 运行/工具溯源：[provenance.yaml](provenance.yaml)
- H14 模型与基础组路线：[../h14-complete-teardown-20260801/README.md](../h14-complete-teardown-20260801/README.md)
- 系列索引：[../README.md](../README.md)

## 可复现命令

在本记录目录执行。schema v3 必须生成 Markdown 与完整 JSON；Excel 仅在明确要求审计工作簿时
才额外提供 `--workbook`。下列命令不生成 Excel。

```powershell
$prepare = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\prepare_register_inference_trace.py'
$tool = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\infer_cycle_ngksi_regions.py'
D:\anaconda3\python.exe $prepare `
  --config analysis/register-inference/config.yaml `
  --source-trace D:\state-learning-lab\run-data\open5gs\20260804T034905Z-2b7f2823\statelearner_trace.jsonl `
  --evidence-trace evidence/statelearner_trace.jsonl `
  --manifest analysis/register-inference/trace-materialization.json `
  --overwrite
D:\anaconda3\python.exe $tool `
  --config analysis/register-inference/config.yaml `
  --output analysis/register-inference/candidates.json `
  --report analysis/register-inference/summary.md
```

如明确要求 Excel，额外传入 `--workbook analysis/register-inference/audit.xlsx` 及本机
workbook 渲染器参数；不得把该可选审计材料误写成每次推断的必需交付。

## 后续

在使用额外短环组或下一轮重复环分析前，先修复核心网停止后的端口回收问题，
并冻结实际 Open5GS 二进制和运行时脚本哈希。后续所有 trace 推断只读取完整的
`statelearner_trace.jsonl`；`statelearner_trace.cleaned.jsonl` 不作为分析输入。
