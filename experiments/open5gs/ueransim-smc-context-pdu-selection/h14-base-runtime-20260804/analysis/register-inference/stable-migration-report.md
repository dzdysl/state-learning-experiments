# H14 轨迹归类算法 B：全模型稳定性推断迁移

## 阅读入口

这是一份可脱离原聊天记录独立阅读的迁移审计报告。它说明 H14 重复循环实验的寄存器观察如何被物化、
轨迹归类算法 B 如何从二维公式候选逐步得到完整稳定树，以及本阶段如何把两棵树迁移到同 input/output 的全模型边。
本报告只记录黑盒观察支持的候选与迁移结论，不把 `r`、`r_i` 或公式解释为已经确认的 Open5GS 源码变量。

本阶段没有修改 `candidates.json`、算法 B 机器结果或交互 HTML，也没有执行下一轮寄存器推断。
新增边级状态只使用“稳定性推断（迁移）”；未满足本阶段闭合条件的边只写“暂不迁移”。

## 项目结构与证据链

实验记录位于 `experiments/open5gs/ueransim-smc-context-pdu-selection/h14-base-runtime-20260804/`。
关键材料按以下顺序连接：

1. `evidence/hypotheses/hypothesis_14.dot` 给出 H14 的 EID、状态端点和 input/output 语义。
2. `evidence/statelearner_trace.jsonl` 是从冻结 raw 快照无损物化的完整运行轨迹。
3. 兄弟记录 `../h14-complete-teardown-20260801/analysis/cycle-cover/base-result.json` 与本记录的 `analysis/register-inference/config.yaml` 界定循环、R3–R10重复、KSI输入和信号字段。
4. `analysis/register-inference/candidates.json` 保存原始观察区域、候选等级、相对稳定推断、旧迁移检验与前序反推材料。
5. `analysis/register-inference/trajectory-formula-candidates.json` 保存算法 B 的真实八点轨迹、二维候选、稳定聚合和新稳定推断。
6. 本报告生成器只读取以上冻结材料并在内存中计算迁移；输出即本文件，不建立平行 JSON。

输入哈希：

- `candidates`：`analysis\register-inference\candidates.json`；SHA-256 `a5d771b0978ec5bec77ef140ea01aba753026d2c4c0d29ce8c07c95fa3d1b13c`。
- `trajectory-formulas`：`analysis\register-inference\trajectory-formula-candidates.json`；SHA-256 `57e3cedd203e74973a9a51063391bb1feef95d723c369d7e9d5c066797e2f949`。
- `trace`：`evidence\statelearner_trace.jsonl`；SHA-256 `cbab627a7542e72733f2cd5576926bc8621f5e330067e43a985bf341a49a68c6`。
- `cycle-cover`：`..\h14-complete-teardown-20260801\analysis\cycle-cover\base-result.json`；SHA-256 `c600ebcd412db1134b0865dee357671bc833962149ce56de9442c0bd07fe6b80`。
- `config`：`analysis\register-inference\config.yaml`；SHA-256 `1a99fd6bcd442b42e7e6cfc544859731c854d506d83bc9ec442de7710b866f89`。

## 术语与坐标

- `r_before`：一个观察区域开始前最近一次真实 KSI 下行值。
- `r_i`：当前事件之前有效的 `ngksi_uplink` 输入寄存器值；直接观察不到时由冻结 trace 在同一序列内重放。
- `r_after`：观察区域末端真实 KSI 下行值；本阶段反推前序事件时另行标明伪值来源。
- 三元组统一写为 `(r_before,{signal=value},[ngksi_uplink=r_i],r_after)`，并保持信号与数值输入的事件顺序。
- `r_before–r_after` 与 `r_i–r_after` 是算法 B 的两个二维投影；静态点和纯铅垂轨迹不独立建立常数公式。
- 稳定树是用既有相对稳定推断及后续新稳定推断样本逐点验证后的完整更新树。
- 稳定性推断迁移表示已建立的同 input/output 树能够解释目标边的完整证据；它不是源码事实。

## 算法 B 到本阶段的工作脉络

1. 二维公式候选阶段以 EID 为基本对象，在两个投影中发现常数、单位仿射与单阈值分段公式；候选组仅为反向索引。
2. 稳定性推断聚合阶段只联合相对稳定推断源边；优先使用完整简单公式，必要时用唯一铅垂值构造跨投影模型树。
3. 新稳定推断阶段把收紧重划后得到的动态长度1区域与旧稳定样本联合验证；两个信号分支公式相同，因此不保留冗余信号根节点。
4. 本阶段把已经完整的树应用到对应 input/output 的所有 H14 边；长度2区域允许一次前序反推闭合，长度3和长度4反例暂不处理。

## 已完成的两棵稳定树

- `registrationRequest/authenticationRequest`：`r' = ite(r < 6, r + 1, 0)`。旧、新稳定样本已经验证96/96点；本阶段检查15条H14边。
- `registrationRequestGUTI/authenticationRequest`：`r' = ite(r_i = 7, ite(r < 6, r + 1, 0), ite(r_i < 6, r_i + 1, 0))`。旧、新稳定样本已经验证88/88点；本阶段检查10条H14边。
- 两组均保留 `s=0` 与 `s=1` 的样本来源；因为分支公式相同，最终树不生成信号根节点。

## 全模型迁移规则

1. 既有稳定性推断边保持不变。
2. 某条边的全部真实R3–R10样本逐点满足完整树时，标记为“稳定性推断（迁移）”。
3. 剩余完整长度2区域先在值域0…7上反推前序事件的 `r_after`。
4. 数学前像为 `{6,7}` 时，读取旧稳定输入投影的唯一核心铅垂成分 `x=7`，只选择值7，不生成值6分支。
5. 这一步是算法 B 的显式候选消歧规则；逆方程本身仍有两个前像，报告不得把7表述为数学唯一解。
6. 前序 `r_before` 直接取原观察区域起点；本批9条轨迹均有直接值，不需要延伸。前序 `r_i` 必须与冻结 trace 重放值一致。
7. 用重建前序三元组运行与二维公式候选阶段相同的精确拟合，再把选择的前序 `r_after=7` 代回末端稳定树逐点验证。
8. 长度3或长度4中仍有反例时，本阶段整条边暂不迁移。

## 迁移总览

- `registrationRequest/authenticationRequest`：覆盖 13/15 条；本阶段迁移 `E0085：s7→s8，registrationRequest/authenticationRequest`、`E0181：s15→s1，registrationRequest/authenticationRequest`、`E0193：s16→s1，registrationRequest/authenticationRequest`；暂不迁移 `E0001：s0→s1，registrationRequest/authenticationRequest`、`E0073：s6→s1，registrationRequest/authenticationRequest`。
- `registrationRequestGUTI/authenticationRequest`：覆盖 10/10 条；暂不迁移 无。

## 两个 input/output 的完整边清单

### `registrationRequest/authenticationRequest`

完整树：`r' = ite(r < 6, r + 1, 0)`。

- `E0001：s0→s1，registrationRequest/authenticationRequest`：暂不迁移；1条轨迹/8点；原三元组逐点匹配 0/8。
- `E0013：s1→s1，registrationRequest/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0037：s3→s1，registrationRequest/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0073：s6→s1，registrationRequest/authenticationRequest`：暂不迁移；3条轨迹/24点；原三元组逐点匹配 16/24。
- `E0085：s7→s8，registrationRequest/authenticationRequest`：稳定性推断（迁移）；8条轨迹/64点；原三元组逐点匹配 1/64。
- `E0097：s8→s1，registrationRequest/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0109：s9→s10，registrationRequest/authenticationRequest`：既有稳定性推断；2条轨迹/16点；原三元组逐点匹配 16/16。
- `E0121：s10→s10，registrationRequest/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0133：s11→s10，registrationRequest/authenticationRequest`：既有稳定性推断；3条轨迹/24点；原三元组逐点匹配 24/24。
- `E0145：s12→s13，registrationRequest/authenticationRequest`：既有稳定性推断；5条轨迹/40点；原三元组逐点匹配 40/40。
- `E0157：s13→s13，registrationRequest/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0169：s14→s13，registrationRequest/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0181：s15→s1，registrationRequest/authenticationRequest`：稳定性推断（迁移）；1条轨迹/8点；原三元组逐点匹配 0/8。
- `E0193：s16→s1，registrationRequest/authenticationRequest`：稳定性推断（迁移）；2条轨迹/16点；原三元组逐点匹配 16/16。
- `E0205：s17→s10，registrationRequest/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。

### `registrationRequestGUTI/authenticationRequest`

完整树：`r' = ite(r_i = 7, ite(r < 6, r + 1, 0), ite(r_i < 6, r_i + 1, 0))`。

- `E0014：s1→s1，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0038：s3→s1，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0050：s4→s1，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；2条轨迹/16点；原三元组逐点匹配 16/16。
- `E0098：s8→s1，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0110：s9→s10，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；2条轨迹/16点；原三元组逐点匹配 16/16。
- `E0122：s10→s10，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0146：s12→s13，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；5条轨迹/40点；原三元组逐点匹配 40/40。
- `E0158：s13→s13，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0170：s14→s13，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。
- `E0206：s17→s10，registrationRequestGUTI/authenticationRequest`：既有稳定性推断；1条轨迹/8点；原三元组逐点匹配 8/8。

## 9条长度2区域的前序反推

本节覆盖 9 条轨迹。每轮先得到数学前像 `{6,7}`，再由核心铅垂成分 `r_i=7` 只选择前序 `r_after=7`。

### `E0085:S010:L13`

- 末端边：`E0085：s7→s8，registrationRequest/authenticationRequest`；信号：`{isInitMsg=1}`。
- 前序边：`E0114：s9→s7，securityModeReject/null_action`。
- 数学前像与选择：R3=[6, 7]→7、R4=[6, 7]→7、R5=[6, 7]→7、R6=[6, 7]→7、R7=[6, 7]→7、R8=[6, 7]→7、R9=[6, 7]→7、R10=[6, 7]→7。
- 重建前序轨迹：R3:(0,[ngksi_uplink=7],7) → R4:(0,[ngksi_uplink=7],7) → R5:(0,[ngksi_uplink=7],7) → R6:(0,[ngksi_uplink=7],7) → R7:(0,[ngksi_uplink=7],7) → R8:(0,[ngksi_uplink=7],7) → R9:(0,[ngksi_uplink=7],7) → R10:(0,[ngksi_uplink=7],7)。
- 来源：`r_before=direct_region_start`，`r_i=frozen_trace_replay`，`r_after=reverse_preimage_vertical_x_preference`。

### `E0085:S017:L16`

- 末端边：`E0085：s7→s8，registrationRequest/authenticationRequest`；信号：`{isInitMsg=1}`。
- 前序边：`E0042：s3→s7，securityModeReject/null_action`。
- 数学前像与选择：R3=[6, 7]→7、R4=[6, 7]→7、R5=[6, 7]→7、R6=[6, 7]→7、R7=[6, 7]→7、R8=[6, 7]→7、R9=[6, 7]→7、R10=[6, 7]→7。
- 重建前序轨迹：R3:(1,[ngksi_uplink=7],7) → R4:(1,[ngksi_uplink=7],7) → R5:(1,[ngksi_uplink=7],7) → R6:(1,[ngksi_uplink=7],7) → R7:(1,[ngksi_uplink=7],7) → R8:(1,[ngksi_uplink=7],7) → R9:(1,[ngksi_uplink=7],7) → R10:(1,[ngksi_uplink=7],7)。
- 来源：`r_before=direct_region_start`，`r_i=frozen_trace_replay`，`r_after=reverse_preimage_vertical_x_preference`。

### `E0085:S017:L17`

- 末端边：`E0085：s7→s8，registrationRequest/authenticationRequest`；信号：`{isInitMsg=1}`。
- 前序边：`E0042：s3→s7，securityModeReject/null_action`。
- 数学前像与选择：R3=[6, 7]→7、R4=[6, 7]→7、R5=[6, 7]→7、R6=[6, 7]→7、R7=[6, 7]→7、R8=[6, 7]→7、R9=[6, 7]→7、R10=[6, 7]→7。
- 重建前序轨迹：R3:(2,[ngksi_uplink=1],7) → R4:(3,[ngksi_uplink=2],7) → R5:(4,[ngksi_uplink=3],7) → R6:(5,[ngksi_uplink=4],7) → R7:(6,[ngksi_uplink=5],7) → R8:(0,[ngksi_uplink=6],7) → R9:(1,[ngksi_uplink=0],7) → R10:(2,[ngksi_uplink=1],7)。
- 来源：`r_before=direct_region_start`，`r_i=frozen_trace_replay`，`r_after=reverse_preimage_vertical_x_preference`。

### `E0085:S037:L25`

- 末端边：`E0085：s7→s8，registrationRequest/authenticationRequest`；信号：`{isInitMsg=1}`。
- 前序边：`E0042：s3→s7，securityModeReject/null_action`。
- 数学前像与选择：R3=[6, 7]→7、R4=[6, 7]→7、R5=[6, 7]→7、R6=[6, 7]→7、R7=[6, 7]→7、R8=[6, 7]→7、R9=[6, 7]→7、R10=[6, 7]→7。
- 重建前序轨迹：R3:(2,[ngksi_uplink=7],7) → R4:(2,[ngksi_uplink=7],7) → R5:(2,[ngksi_uplink=7],7) → R6:(2,[ngksi_uplink=7],7) → R7:(2,[ngksi_uplink=7],7) → R8:(2,[ngksi_uplink=7],7) → R9:(2,[ngksi_uplink=7],7) → R10:(2,[ngksi_uplink=7],7)。
- 来源：`r_before=direct_region_start`，`r_i=frozen_trace_replay`，`r_after=reverse_preimage_vertical_x_preference`。

### `E0085:S037:L26`

- 末端边：`E0085：s7→s8，registrationRequest/authenticationRequest`；信号：`{isInitMsg=1}`。
- 前序边：`E0042：s3→s7，securityModeReject/null_action`。
- 数学前像与选择：R3=[6, 7]→7、R4=[6, 7]→7、R5=[6, 7]→7、R6=[6, 7]→7、R7=[6, 7]→7、R8=[6, 7]→7、R9=[6, 7]→7、R10=[6, 7]→7。
- 重建前序轨迹：R3:(2,[ngksi_uplink=1],7) → R4:(2,[ngksi_uplink=1],7) → R5:(2,[ngksi_uplink=1],7) → R6:(2,[ngksi_uplink=1],7) → R7:(2,[ngksi_uplink=1],7) → R8:(2,[ngksi_uplink=1],7) → R9:(2,[ngksi_uplink=1],7) → R10:(2,[ngksi_uplink=1],7)。
- 来源：`r_before=direct_region_start`，`r_i=frozen_trace_replay`，`r_after=reverse_preimage_vertical_x_preference`。

### `E0085:S037:L27`

- 末端边：`E0085：s7→s8，registrationRequest/authenticationRequest`；信号：`{isInitMsg=1}`。
- 前序边：`E0042：s3→s7，securityModeReject/null_action`。
- 数学前像与选择：R3=[6, 7]→7、R4=[6, 7]→7、R5=[6, 7]→7、R6=[6, 7]→7、R7=[6, 7]→7、R8=[6, 7]→7、R9=[6, 7]→7、R10=[6, 7]→7。
- 重建前序轨迹：R3:(2,[ngksi_uplink=7],7) → R4:(2,[ngksi_uplink=7],7) → R5:(2,[ngksi_uplink=7],7) → R6:(2,[ngksi_uplink=7],7) → R7:(2,[ngksi_uplink=7],7) → R8:(2,[ngksi_uplink=7],7) → R9:(2,[ngksi_uplink=7],7) → R10:(2,[ngksi_uplink=7],7)。
- 来源：`r_before=direct_region_start`，`r_i=frozen_trace_replay`，`r_after=reverse_preimage_vertical_x_preference`。

### `E0085:S037:L28`

- 末端边：`E0085：s7→s8，registrationRequest/authenticationRequest`；信号：`{isInitMsg=1}`。
- 前序边：`E0042：s3→s7，securityModeReject/null_action`。
- 数学前像与选择：R3=[6, 7]→7、R4=[6, 7]→7、R5=[6, 7]→7、R6=[6, 7]→7、R7=[6, 7]→7、R8=[6, 7]→7、R9=[6, 7]→7、R10=[6, 7]→7。
- 重建前序轨迹：R3:(2,[ngksi_uplink=1],7) → R4:(2,[ngksi_uplink=1],7) → R5:(2,[ngksi_uplink=1],7) → R6:(2,[ngksi_uplink=1],7) → R7:(2,[ngksi_uplink=1],7) → R8:(2,[ngksi_uplink=1],7) → R9:(2,[ngksi_uplink=1],7) → R10:(2,[ngksi_uplink=1],7)。
- 来源：`r_before=direct_region_start`，`r_i=frozen_trace_replay`，`r_after=reverse_preimage_vertical_x_preference`。

### `E0085:S039:L29`

- 末端边：`E0085：s7→s8，registrationRequest/authenticationRequest`；信号：`{isInitMsg=1}`。
- 前序边：`E0042：s3→s7，securityModeReject/null_action`。
- 数学前像与选择：R3=[6, 7]→7、R4=[6, 7]→7、R5=[6, 7]→7、R6=[6, 7]→7、R7=[6, 7]→7、R8=[6, 7]→7、R9=[6, 7]→7、R10=[6, 7]→7。
- 重建前序轨迹：R3:(0,[ngksi_uplink=7],7) → R4:(0,[ngksi_uplink=7],7) → R5:(0,[ngksi_uplink=7],7) → R6:(0,[ngksi_uplink=7],7) → R7:(0,[ngksi_uplink=7],7) → R8:(0,[ngksi_uplink=7],7) → R9:(0,[ngksi_uplink=7],7) → R10:(0,[ngksi_uplink=7],7)。
- 来源：`r_before=direct_region_start`，`r_i=frozen_trace_replay`，`r_after=reverse_preimage_vertical_x_preference`。

### `E0181:S039:L29`

- 末端边：`E0181：s15→s1，registrationRequest/authenticationRequest`；信号：`{isInitMsg=1}`。
- 前序边：`E0210：s17→s15，securityModeReject/null_action`。
- 数学前像与选择：R3=[6, 7]→7、R4=[6, 7]→7、R5=[6, 7]→7、R6=[6, 7]→7、R7=[6, 7]→7、R8=[6, 7]→7、R9=[6, 7]→7、R10=[6, 7]→7。
- 重建前序轨迹：R3:(1,[ngksi_uplink=7],7) → R4:(1,[ngksi_uplink=7],7) → R5:(1,[ngksi_uplink=7],7) → R6:(1,[ngksi_uplink=7],7) → R7:(1,[ngksi_uplink=7],7) → R8:(1,[ngksi_uplink=7],7) → R9:(1,[ngksi_uplink=7],7) → R10:(1,[ngksi_uplink=7],7)。
- 来源：`r_before=direct_region_start`，`r_i=frozen_trace_replay`，`r_after=reverse_preimage_vertical_x_preference`。

## 前序边二维拟合

### `E0042：s3→s7，securityModeReject/null_action`

证据轨迹：`['E0042:S017:L16', 'E0042:S017:L17', 'E0042:S037:L25', 'E0042:S037:L26', 'E0042:S037:L27', 'E0042:S037:L28', 'E0042:S039:L29']`；对应末端：`['E0085:S017:L16', 'E0085:S017:L17', 'E0085:S037:L25', 'E0085:S037:L26', 'E0085:S037:L27', 'E0085:S037:L28', 'E0085:S039:L29']`。
- r_before–r_after：候选 `["r' = 7"]`。
- r_i–r_after：候选 `["r' = 7"]`。

### `E0114：s9→s7，securityModeReject/null_action`

证据轨迹：`['E0114:S010:L13']`；对应末端：`['E0085:S010:L13']`。
- r_before–r_after：不产生公式；原因 `degenerate_only`。
- r_i–r_after：不产生公式；原因 `degenerate_only`。

### `E0210：s17→s15，securityModeReject/null_action`

证据轨迹：`['E0210:S039:L29']`；对应末端：`['E0181:S039:L29']`。
- r_before–r_after：不产生公式；原因 `degenerate_only`。
- r_i–r_after：不产生公式；原因 `degenerate_only`。

因此 `E0042：s3→s7，securityModeReject/null_action` 的动态水平轨迹在两个投影都得到 `r'=7`。
`E0114：s9→s7，securityModeReject/null_action` 与 `E0210：s17→s15，securityModeReject/null_action` 各自只有静态点，按算法B规则不独立产生常数公式；其事件级伪值仍参与末端闭合验证。

## 迁移边的逐点证据

### `E0085：s7→s8，registrationRequest/authenticationRequest`

最终状态：稳定性推断（迁移）。
选择前序 `r_after=7` 后，末端稳定树逐点匹配 64/64。
- `E0085:S010:L13`：R3:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(7,{isInitMsg=1},[ngksi_uplink=7],0)；匹配 8/8。
- `E0085:S017:L16`：R3:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(7,{isInitMsg=1},[ngksi_uplink=7],0)；匹配 8/8。
- `E0085:S017:L17`：R3:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(7,{isInitMsg=1},[ngksi_uplink=7],0)；匹配 8/8。
- `E0085:S037:L25`：R3:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(7,{isInitMsg=1},[ngksi_uplink=7],0)；匹配 8/8。
- `E0085:S037:L26`：R3:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(7,{isInitMsg=1},[ngksi_uplink=7],0)；匹配 8/8。
- `E0085:S037:L27`：R3:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(7,{isInitMsg=1},[ngksi_uplink=7],0)；匹配 8/8。
- `E0085:S037:L28`：R3:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(7,{isInitMsg=1},[ngksi_uplink=7],0)；匹配 8/8。
- `E0085:S039:L29`：R3:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(7,{isInitMsg=1},[ngksi_uplink=7],0)；匹配 8/8。

### `E0181：s15→s1，registrationRequest/authenticationRequest`

最终状态：稳定性推断（迁移）。
选择前序 `r_after=7` 后，末端稳定树逐点匹配 8/8。
- `E0181:S039:L29`：R3:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(7,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(7,{isInitMsg=1},[ngksi_uplink=7],0)；匹配 8/8。

### `E0193：s16→s1，registrationRequest/authenticationRequest`

最终状态：稳定性推断（迁移）。
- `E0193:S037:L25`；信号 `{isInitMsg=0}`：R3:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R4:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R5:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R6:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R7:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R10:(1,{isInitMsg=0},[ngksi_uplink=7],2)；匹配 8/8。
- `E0193:S037:L27`；信号 `{isInitMsg=0}`：R3:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R4:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R5:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R6:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R7:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R10:(1,{isInitMsg=0},[ngksi_uplink=7],2)；匹配 8/8。

## 暂不迁移的反例

### `E0001：s0→s1，registrationRequest/authenticationRequest`

- `E0001:S018:L18`；区域长度 `4`；信号 `{isInitMsg=1}`；匹配 0/8；轨迹 R3:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(0,{isInitMsg=1},[ngksi_uplink=7],0)。
  - 反例：`[{'repetition': 3, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 4, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 5, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 6, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 7, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 8, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 9, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 10, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}]`。
本阶段不处理该边的多步前序反推，因此整条边暂不迁移。

### `E0073：s6→s1，registrationRequest/authenticationRequest`

- `E0073:S008:L10`；区域长度 `3`；信号 `{isInitMsg=0}`；匹配 0/8；轨迹 R3:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R4:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R5:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R6:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R9:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R10:(0,{isInitMsg=0},[ngksi_uplink=7],0)。
  - 反例：`[{'repetition': 3, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 4, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 5, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 6, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 7, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 8, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 9, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}, {'repetition': 10, 'r_before': 0, 'r_i': 7, 'actual_r_after': 0, 'predicted_r_after': 1}]`。
- `E0073:S036:L21`；区域长度 `3`；信号 `{isInitMsg=0}`；匹配 8/8；轨迹 R3:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R4:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R5:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R6:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R7:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R10:(5,{isInitMsg=0},[ngksi_uplink=7],6)。
- `E0073:S036:L23`；区域长度 `3`；信号 `{isInitMsg=0}`；匹配 8/8；轨迹 R3:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R4:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R5:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R6:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R7:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R10:(5,{isInitMsg=0},[ngksi_uplink=7],6)。
本阶段不处理该边的多步前序反推，因此整条边暂不迁移。

## 全部轨迹审计

以下折叠段覆盖两个 input/output 的全部真实R3–R10轨迹，便于新任务直接复核边级结论。

<details>
<summary>registrationRequest/authenticationRequest：展开全部轨迹</summary>

- `E0001：s0→s1，registrationRequest/authenticationRequest` / `E0001:S018:L18` / `{isInitMsg=1}`：R3:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(0,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0013：s1→s1，registrationRequest/authenticationRequest` / `E0013:L001:L32` / `{isInitMsg=0}`：R3:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R4:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R5:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R6:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R7:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R9:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R10:(2,{isInitMsg=0},[ngksi_uplink=7],3)。
- `E0037：s3→s1，registrationRequest/authenticationRequest` / `E0037:S002:L1` / `{isInitMsg=0}`：R3:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R4:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R5:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R6:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R7:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R9:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R10:(2,{isInitMsg=0},[ngksi_uplink=7],3)。
- `E0073：s6→s1，registrationRequest/authenticationRequest` / `E0073:S008:L10` / `{isInitMsg=0}`：R3:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R4:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R5:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R6:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R9:(0,{isInitMsg=0},[ngksi_uplink=7],0) → R10:(0,{isInitMsg=0},[ngksi_uplink=7],0)。
- `E0073：s6→s1，registrationRequest/authenticationRequest` / `E0073:S036:L21` / `{isInitMsg=0}`：R3:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R4:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R5:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R6:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R7:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R10:(5,{isInitMsg=0},[ngksi_uplink=7],6)。
- `E0073：s6→s1，registrationRequest/authenticationRequest` / `E0073:S036:L23` / `{isInitMsg=0}`：R3:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R4:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R5:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R6:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R7:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R10:(5,{isInitMsg=0},[ngksi_uplink=7],6)。
- `E0085：s7→s8，registrationRequest/authenticationRequest` / `E0085:S010:L13` / `{isInitMsg=1}`：R3:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(0,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0085：s7→s8，registrationRequest/authenticationRequest` / `E0085:S017:L16` / `{isInitMsg=1}`：R3:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(1,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0085：s7→s8，registrationRequest/authenticationRequest` / `E0085:S017:L17` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(3,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(4,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(5,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(6,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(2,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0085：s7→s8，registrationRequest/authenticationRequest` / `E0085:S037:L25` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(2,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0085：s7→s8，registrationRequest/authenticationRequest` / `E0085:S037:L26` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(2,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0085：s7→s8，registrationRequest/authenticationRequest` / `E0085:S037:L27` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(2,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0085：s7→s8，registrationRequest/authenticationRequest` / `E0085:S037:L28` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(2,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(2,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0085：s7→s8，registrationRequest/authenticationRequest` / `E0085:S039:L29` / `{isInitMsg=1}`：R3:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(0,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(0,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0097：s8→s1，registrationRequest/authenticationRequest` / `E0097:S017:L16` / `{isInitMsg=0}`：R3:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R4:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R5:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R6:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R7:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R8:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R9:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R10:(0,{isInitMsg=0},[ngksi_uplink=7],1)。
- `E0109：s9→s10，registrationRequest/authenticationRequest` / `E0109:S037:L25` / `{isInitMsg=0}`：R3:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R4:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R5:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R6:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R7:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R8:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R9:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R10:(0,{isInitMsg=0},[ngksi_uplink=7],1)。
- `E0109：s9→s10，registrationRequest/authenticationRequest` / `E0109:S037:L26` / `{isInitMsg=0}`：R3:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R4:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R5:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R6:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R7:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R8:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R9:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R10:(0,{isInitMsg=0},[ngksi_uplink=7],1)。
- `E0121：s10→s10，registrationRequest/authenticationRequest` / `E0121:L003:L34` / `{isInitMsg=0}`：R3:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R4:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R5:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R6:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R10:(3,{isInitMsg=0},[ngksi_uplink=7],4)。
- `E0133：s11→s10，registrationRequest/authenticationRequest` / `E0133:F001:L30` / `{isInitMsg=1}`：R3:(3,{isInitMsg=1},[ngksi_uplink=7],4) → R4:(4,{isInitMsg=1},[ngksi_uplink=7],5) → R5:(5,{isInitMsg=1},[ngksi_uplink=7],6) → R6:(6,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R8:(1,{isInitMsg=1},[ngksi_uplink=7],2) → R9:(2,{isInitMsg=1},[ngksi_uplink=7],3) → R10:(3,{isInitMsg=1},[ngksi_uplink=7],4)。
- `E0133：s11→s10，registrationRequest/authenticationRequest` / `E0133:S003:L3` / `{isInitMsg=1}`：R3:(3,{isInitMsg=1},[ngksi_uplink=7],4) → R4:(4,{isInitMsg=1},[ngksi_uplink=7],5) → R5:(5,{isInitMsg=1},[ngksi_uplink=7],6) → R6:(6,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R8:(1,{isInitMsg=1},[ngksi_uplink=7],2) → R9:(2,{isInitMsg=1},[ngksi_uplink=7],3) → R10:(3,{isInitMsg=1},[ngksi_uplink=7],4)。
- `E0133：s11→s10，registrationRequest/authenticationRequest` / `E0133:S039:L29` / `{isInitMsg=1}`：R3:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R4:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R5:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R6:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R7:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R9:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R10:(0,{isInitMsg=1},[ngksi_uplink=7],1)。
- `E0145：s12→s13，registrationRequest/authenticationRequest` / `E0145:S005:L6` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=7],3) → R4:(3,{isInitMsg=1},[ngksi_uplink=7],4) → R5:(4,{isInitMsg=1},[ngksi_uplink=7],5) → R6:(5,{isInitMsg=1},[ngksi_uplink=7],6) → R7:(6,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R9:(1,{isInitMsg=1},[ngksi_uplink=7],2) → R10:(2,{isInitMsg=1},[ngksi_uplink=7],3)。
- `E0145：s12→s13，registrationRequest/authenticationRequest` / `E0145:S012:L14` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=7],3) → R4:(3,{isInitMsg=1},[ngksi_uplink=7],4) → R5:(4,{isInitMsg=1},[ngksi_uplink=7],5) → R6:(5,{isInitMsg=1},[ngksi_uplink=7],6) → R7:(6,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R9:(1,{isInitMsg=1},[ngksi_uplink=7],2) → R10:(2,{isInitMsg=1},[ngksi_uplink=7],3)。
- `E0145：s12→s13，registrationRequest/authenticationRequest` / `E0145:S022:L19` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=7],3) → R4:(3,{isInitMsg=1},[ngksi_uplink=7],4) → R5:(4,{isInitMsg=1},[ngksi_uplink=7],5) → R6:(5,{isInitMsg=1},[ngksi_uplink=7],6) → R7:(6,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R9:(1,{isInitMsg=1},[ngksi_uplink=7],2) → R10:(2,{isInitMsg=1},[ngksi_uplink=7],3)。
- `E0145：s12→s13，registrationRequest/authenticationRequest` / `E0145:S036:L21` / `{isInitMsg=1}`：R3:(4,{isInitMsg=1},[ngksi_uplink=7],5) → R4:(6,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(1,{isInitMsg=1},[ngksi_uplink=7],2) → R6:(3,{isInitMsg=1},[ngksi_uplink=7],4) → R7:(5,{isInitMsg=1},[ngksi_uplink=7],6) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R9:(2,{isInitMsg=1},[ngksi_uplink=7],3) → R10:(4,{isInitMsg=1},[ngksi_uplink=7],5)。
- `E0145：s12→s13，registrationRequest/authenticationRequest` / `E0145:S036:L22` / `{isInitMsg=1}`：R3:(4,{isInitMsg=1},[ngksi_uplink=7],5) → R4:(6,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(1,{isInitMsg=1},[ngksi_uplink=7],2) → R6:(3,{isInitMsg=1},[ngksi_uplink=7],4) → R7:(5,{isInitMsg=1},[ngksi_uplink=7],6) → R8:(0,{isInitMsg=1},[ngksi_uplink=7],1) → R9:(2,{isInitMsg=1},[ngksi_uplink=7],3) → R10:(4,{isInitMsg=1},[ngksi_uplink=7],5)。
- `E0157：s13→s13，registrationRequest/authenticationRequest` / `E0157:L005:L36` / `{isInitMsg=0}`：R3:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R4:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R5:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R6:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R10:(3,{isInitMsg=0},[ngksi_uplink=7],4)。
- `E0169：s14→s13，registrationRequest/authenticationRequest` / `E0169:S006:L8` / `{isInitMsg=0}`：R3:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R4:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R5:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R6:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R10:(3,{isInitMsg=0},[ngksi_uplink=7],4)。
- `E0181：s15→s1，registrationRequest/authenticationRequest` / `E0181:S039:L29` / `{isInitMsg=1}`：R3:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R4:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R5:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R6:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R7:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R8:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R9:(1,{isInitMsg=1},[ngksi_uplink=7],0) → R10:(1,{isInitMsg=1},[ngksi_uplink=7],0)。
- `E0193：s16→s1，registrationRequest/authenticationRequest` / `E0193:S037:L25` / `{isInitMsg=0}`：R3:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R4:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R5:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R6:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R7:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R10:(1,{isInitMsg=0},[ngksi_uplink=7],2)。
- `E0193：s16→s1，registrationRequest/authenticationRequest` / `E0193:S037:L27` / `{isInitMsg=0}`：R3:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R4:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R5:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R6:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R7:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R10:(1,{isInitMsg=0},[ngksi_uplink=7],2)。
- `E0205：s17→s10，registrationRequest/authenticationRequest` / `E0205:S004:L4` / `{isInitMsg=0}`：R3:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R4:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R5:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R6:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R7:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R8:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R9:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R10:(3,{isInitMsg=0},[ngksi_uplink=7],4)。

</details>

<details>
<summary>registrationRequestGUTI/authenticationRequest：展开全部轨迹</summary>

- `E0014：s1→s1，registrationRequestGUTI/authenticationRequest` / `E0014:L002:L33` / `{isInitMsg=0}`：R3:(2,{isInitMsg=0},[ngksi_uplink=7],3) → R4:(3,{isInitMsg=0},[ngksi_uplink=7],4) → R5:(4,{isInitMsg=0},[ngksi_uplink=7],5) → R6:(5,{isInitMsg=0},[ngksi_uplink=7],6) → R7:(6,{isInitMsg=0},[ngksi_uplink=7],0) → R8:(0,{isInitMsg=0},[ngksi_uplink=7],1) → R9:(1,{isInitMsg=0},[ngksi_uplink=7],2) → R10:(2,{isInitMsg=0},[ngksi_uplink=7],3)。
- `E0038：s3→s1，registrationRequestGUTI/authenticationRequest` / `E0038:S002:L2` / `{isInitMsg=0}`：R3:(2,{isInitMsg=0},[ngksi_uplink=2],3) → R4:(3,{isInitMsg=0},[ngksi_uplink=3],4) → R5:(4,{isInitMsg=0},[ngksi_uplink=4],5) → R6:(5,{isInitMsg=0},[ngksi_uplink=5],6) → R7:(6,{isInitMsg=0},[ngksi_uplink=6],0) → R8:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R9:(1,{isInitMsg=0},[ngksi_uplink=1],2) → R10:(2,{isInitMsg=0},[ngksi_uplink=2],3)。
- `E0050：s4→s1，registrationRequestGUTI/authenticationRequest` / `E0050:F002:L31` / `{isInitMsg=0}`：R3:(2,{isInitMsg=0},[ngksi_uplink=2],3) → R4:(3,{isInitMsg=0},[ngksi_uplink=3],4) → R5:(4,{isInitMsg=0},[ngksi_uplink=4],5) → R6:(5,{isInitMsg=0},[ngksi_uplink=5],6) → R7:(6,{isInitMsg=0},[ngksi_uplink=6],0) → R8:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R9:(1,{isInitMsg=0},[ngksi_uplink=1],2) → R10:(2,{isInitMsg=0},[ngksi_uplink=2],3)。
- `E0050：s4→s1，registrationRequestGUTI/authenticationRequest` / `E0050:S009:L12` / `{isInitMsg=0}`：R3:(2,{isInitMsg=0},[ngksi_uplink=2],3) → R4:(3,{isInitMsg=0},[ngksi_uplink=3],4) → R5:(4,{isInitMsg=0},[ngksi_uplink=4],5) → R6:(5,{isInitMsg=0},[ngksi_uplink=5],6) → R7:(6,{isInitMsg=0},[ngksi_uplink=6],0) → R8:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R9:(1,{isInitMsg=0},[ngksi_uplink=1],2) → R10:(2,{isInitMsg=0},[ngksi_uplink=2],3)。
- `E0098：s8→s1，registrationRequestGUTI/authenticationRequest` / `E0098:S017:L17` / `{isInitMsg=0}`：R3:(0,{isInitMsg=0},[ngksi_uplink=2],3) → R4:(0,{isInitMsg=0},[ngksi_uplink=3],4) → R5:(0,{isInitMsg=0},[ngksi_uplink=4],5) → R6:(0,{isInitMsg=0},[ngksi_uplink=5],6) → R7:(0,{isInitMsg=0},[ngksi_uplink=6],0) → R8:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R9:(0,{isInitMsg=0},[ngksi_uplink=1],2) → R10:(0,{isInitMsg=0},[ngksi_uplink=2],3)。
- `E0110：s9→s10，registrationRequestGUTI/authenticationRequest` / `E0110:S037:L27` / `{isInitMsg=0}`：R3:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R4:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R5:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R6:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R7:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R8:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R9:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R10:(0,{isInitMsg=0},[ngksi_uplink=0],1)。
- `E0110：s9→s10，registrationRequestGUTI/authenticationRequest` / `E0110:S037:L28` / `{isInitMsg=0}`：R3:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R4:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R5:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R6:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R7:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R8:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R9:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R10:(0,{isInitMsg=0},[ngksi_uplink=0],1)。
- `E0122：s10→s10，registrationRequestGUTI/authenticationRequest` / `E0122:L004:L35` / `{isInitMsg=0}`：R3:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R4:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R5:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R6:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R7:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R8:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R9:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R10:(1,{isInitMsg=0},[ngksi_uplink=0],1)。
- `E0146：s12→s13，registrationRequestGUTI/authenticationRequest` / `E0146:S005:L7` / `{isInitMsg=1}`：R3:(1,{isInitMsg=1},[ngksi_uplink=0],1) → R4:(1,{isInitMsg=1},[ngksi_uplink=0],1) → R5:(1,{isInitMsg=1},[ngksi_uplink=0],1) → R6:(1,{isInitMsg=1},[ngksi_uplink=0],1) → R7:(1,{isInitMsg=1},[ngksi_uplink=0],1) → R8:(1,{isInitMsg=1},[ngksi_uplink=0],1) → R9:(1,{isInitMsg=1},[ngksi_uplink=0],1) → R10:(1,{isInitMsg=1},[ngksi_uplink=0],1)。
- `E0146：s12→s13，registrationRequestGUTI/authenticationRequest` / `E0146:S012:L15` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=2],3) → R4:(3,{isInitMsg=1},[ngksi_uplink=3],4) → R5:(4,{isInitMsg=1},[ngksi_uplink=4],5) → R6:(5,{isInitMsg=1},[ngksi_uplink=5],6) → R7:(6,{isInitMsg=1},[ngksi_uplink=6],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=0],1) → R9:(1,{isInitMsg=1},[ngksi_uplink=1],2) → R10:(2,{isInitMsg=1},[ngksi_uplink=2],3)。
- `E0146：s12→s13，registrationRequestGUTI/authenticationRequest` / `E0146:S022:L20` / `{isInitMsg=1}`：R3:(2,{isInitMsg=1},[ngksi_uplink=2],3) → R4:(3,{isInitMsg=1},[ngksi_uplink=3],4) → R5:(4,{isInitMsg=1},[ngksi_uplink=4],5) → R6:(5,{isInitMsg=1},[ngksi_uplink=5],6) → R7:(6,{isInitMsg=1},[ngksi_uplink=6],0) → R8:(0,{isInitMsg=1},[ngksi_uplink=0],1) → R9:(1,{isInitMsg=1},[ngksi_uplink=1],2) → R10:(2,{isInitMsg=1},[ngksi_uplink=2],3)。
- `E0146：s12→s13，registrationRequestGUTI/authenticationRequest` / `E0146:S036:L23` / `{isInitMsg=1}`：R3:(4,{isInitMsg=1},[ngksi_uplink=4],5) → R4:(6,{isInitMsg=1},[ngksi_uplink=6],0) → R5:(1,{isInitMsg=1},[ngksi_uplink=1],2) → R6:(3,{isInitMsg=1},[ngksi_uplink=3],4) → R7:(5,{isInitMsg=1},[ngksi_uplink=5],6) → R8:(0,{isInitMsg=1},[ngksi_uplink=0],1) → R9:(2,{isInitMsg=1},[ngksi_uplink=2],3) → R10:(4,{isInitMsg=1},[ngksi_uplink=4],5)。
- `E0146：s12→s13，registrationRequestGUTI/authenticationRequest` / `E0146:S036:L24` / `{isInitMsg=1}`：R3:(4,{isInitMsg=1},[ngksi_uplink=4],5) → R4:(6,{isInitMsg=1},[ngksi_uplink=6],0) → R5:(1,{isInitMsg=1},[ngksi_uplink=1],2) → R6:(3,{isInitMsg=1},[ngksi_uplink=3],4) → R7:(5,{isInitMsg=1},[ngksi_uplink=5],6) → R8:(0,{isInitMsg=1},[ngksi_uplink=0],1) → R9:(2,{isInitMsg=1},[ngksi_uplink=2],3) → R10:(4,{isInitMsg=1},[ngksi_uplink=4],5)。
- `E0158：s13→s13，registrationRequestGUTI/authenticationRequest` / `E0158:L006:L37` / `{isInitMsg=0}`：R3:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R4:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R5:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R6:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R7:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R8:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R9:(1,{isInitMsg=0},[ngksi_uplink=0],1) → R10:(1,{isInitMsg=0},[ngksi_uplink=0],1)。
- `E0170：s14→s13，registrationRequestGUTI/authenticationRequest` / `E0170:S006:L9` / `{isInitMsg=0}`：R3:(3,{isInitMsg=0},[ngksi_uplink=3],4) → R4:(4,{isInitMsg=0},[ngksi_uplink=4],5) → R5:(5,{isInitMsg=0},[ngksi_uplink=5],6) → R6:(6,{isInitMsg=0},[ngksi_uplink=6],0) → R7:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R8:(1,{isInitMsg=0},[ngksi_uplink=1],2) → R9:(2,{isInitMsg=0},[ngksi_uplink=2],3) → R10:(3,{isInitMsg=0},[ngksi_uplink=3],4)。
- `E0206：s17→s10，registrationRequestGUTI/authenticationRequest` / `E0206:S004:L5` / `{isInitMsg=0}`：R3:(3,{isInitMsg=0},[ngksi_uplink=3],4) → R4:(4,{isInitMsg=0},[ngksi_uplink=4],5) → R5:(5,{isInitMsg=0},[ngksi_uplink=5],6) → R6:(6,{isInitMsg=0},[ngksi_uplink=6],0) → R7:(0,{isInitMsg=0},[ngksi_uplink=0],1) → R8:(1,{isInitMsg=0},[ngksi_uplink=1],2) → R9:(2,{isInitMsg=0},[ngksi_uplink=2],3) → R10:(3,{isInitMsg=0},[ngksi_uplink=3],4)。

</details>

## 结论与边界

- `registrationRequestGUTI/authenticationRequest` 已覆盖10/10条H14边。
- `registrationRequest/authenticationRequest` 已覆盖13/15条H14边；`E0001：s0→s1，registrationRequest/authenticationRequest`、`E0073：s6→s1，registrationRequest/authenticationRequest` 暂不迁移。
- 反推只为这9条证据轨迹选择事件级伪值7。`E0042：s3→s7，securityModeReject/null_action` 的 `r'=7` 是二维拟合候选；`E0114：s9→s7，securityModeReject/null_action` 与 `E0210：s17→s15，securityModeReject/null_action` 没有边级公式。
- 铅垂线 `x=7` 是候选消歧依据，不消除逆方程存在值6前像这一数学事实。
- 本报告不修改原候选、不把迁移结论回写算法A、不继续处理长度3或长度4区域，也不声称确认真实AMF实现。

## 可复现命令

```powershell
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\report_full_model_stable_migration.py `
  --candidates analysis/register-inference/candidates.json `
  --trajectory-formulas analysis/register-inference/trajectory-formula-candidates.json `
  --trace evidence/statelearner_trace.jsonl `
  --cycle-cover ../h14-complete-teardown-20260801/analysis/cycle-cover/base-result.json `
  --config analysis/register-inference/config.yaml `
  --output analysis/register-inference/stable-migration-report.md
```

## 排版检查

报告不使用宽表格。长公式、绝对路径、EID组合和R3–R10轨迹均放在独立段落或可折叠块中，允许自然换行；
具体边始终同时写出 `EID、src/dst、input/output`，避免脱离上下文的边号。
