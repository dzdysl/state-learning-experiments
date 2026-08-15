# 轨迹归类算法 B：二维公式候选、稳定性聚合与新稳定推断

## 范围与口径

本结果合并分析配置的三组 input/output，不区分相对稳定推断与假设性候选，也不按信号 `s=0/1` 拆分。
EID 是拥有公式候选的基本对象；候选组只是规范化公式的反向索引，允许重叠，不进行唯一分簇。
算法只采用精确整数关系与每个 EID 内去重的有向段，不读取算法 A 的任何聚类结果。

共覆盖 29 个 EID、87 条真实 R3–R10 轨迹、696 个样本点，形成 6 个候选组。

各 input/output 的覆盖为：

- `authenticationResponse/securityModeCommand`：4 个 EID、39 条轨迹、312 个点、2 个候选组。
- `registrationRequest/authenticationRequest`：15 个 EID、32 条轨迹、256 个点、2 个候选组。
- `registrationRequestGUTI/authenticationRequest`：10 个 EID、16 条轨迹、128 个点、2 个候选组。

## 稳定性推断聚合

### `authenticationResponse/securityModeCommand`

- 稳定源边：`['E0019', 'E0103', 'E0127', 'E0163']`；39 条轨迹、312 个真实点；信号适用条件：`不适用`。
- r_before–r_after：全部稳定样本可由单一简单公式解释。
- r_i–r_after：仅提供后台局部候选。
- 聚合公式：`r' = r`；层级 `simple_projection`；逐点验证 312/312。

### `registrationRequest/authenticationRequest`

- 稳定源边：`['E0013', 'E0037', 'E0097', 'E0109', 'E0121', 'E0157', 'E0169', 'E0205']`；9 条轨迹、72 个真实点；信号适用条件：`isInitMsg=0`。
- r_before–r_after：全部稳定样本可由单一简单公式解释。
- r_i–r_after：仅有单一铅垂轨迹，不纳入聚合。
- 聚合公式：`r' = ite(r < 6, r + 1, 0)`；层级 `simple_projection`；逐点验证 72/72。

### `registrationRequestGUTI/authenticationRequest`

- 稳定源边：`['E0014', 'E0038', 'E0098', 'E0110', 'E0122', 'E0158', 'E0170', 'E0206']`；9 条轨迹、72 个真实点；信号适用条件：`isInitMsg=0`。
- r_before–r_after：仅提供后台局部候选。
- r_i–r_after：仅提供后台局部候选。
- 聚合公式：`r' = ite(r_i = 7, ite(r < 6, r + 1, 0), ite(r_i < 6, r_i + 1, 0))`；层级 `cross_projection_guard`；逐点验证 72/72。

## 新稳定推断

本阶段只使用前序重划后完整、动态的新增长度1区域。先核对旧稳定三元组包含、主要方向和旧树逐点验证；全部成立时复用旧聚合公式，否则按相同 input/output 与 s 联合旧、新样本重新聚合。
不同 s 分支公式相同时化简为单一公式，但分支样本来源和计数仍分别保存；只有公式不同时才生成信号根节点。

### `registrationRequest/authenticationRequest`

- 方法：复用旧稳定聚合；旧轨迹 9 条/72 点，新轨迹 3 条/24 点。
- `s=0`：旧样本 72 点，新样本 0 点；新轨迹 `[]`。
- `s=1`：旧样本 0 点，新样本 24 点；新轨迹 `['E0133:S003:L3', 'E0145:S005:L6', 'E0145:S012:L14']`。
- `E0133:S003:L3`：`s11→s10` `registrationRequest/authenticationRequest`；完整=是，落入旧稳定轨迹=是，方向一致=是，旧公式精确=是。
- `E0145:S005:L6`：`s12→s13` `registrationRequest/authenticationRequest`；完整=是，落入旧稳定轨迹=是，方向一致=是，旧公式精确=是。
- `E0145:S012:L14`：`s12→s13` `registrationRequest/authenticationRequest`；完整=是，落入旧稳定轨迹=是，方向一致=是，旧公式精确=是。
- 最终公式：`r' = ite(r < 6, r + 1, 0)`；相同信号分支已合并；逐点验证 96/96。

### `registrationRequestGUTI/authenticationRequest`

- 方法：复用旧稳定聚合；旧轨迹 9 条/72 点，新轨迹 2 条/16 点。
- `s=0`：旧样本 72 点，新样本 8 点；新轨迹 `['E0050:S009:L12']`。
- `s=1`：旧样本 0 点，新样本 8 点；新轨迹 `['E0146:S012:L15']`。
- `E0050:S009:L12`：`s4→s1` `registrationRequestGUTI/authenticationRequest`；完整=是，落入旧稳定轨迹=是，方向一致=是，旧公式精确=是。
- `E0146:S012:L15`：`s12→s13` `registrationRequestGUTI/authenticationRequest`；完整=是，落入旧稳定轨迹=是，方向一致=是，旧公式精确=是。
- 最终公式：`r' = ite(r_i = 7, ite(r < 6, r + 1, 0), ite(r_i < 6, r_i + 1, 0))`；相同信号分支已合并；逐点验证 88/88。

## 前序最简归因与伪下行重划

本阶段直接读取全部已选 I/O 的假设性观察区域；旧迁移状态只作审计，不参与筛选，也不读取旧前序反推候选。
前序保持只把最近真实 KSI 下行的可观察值连续延伸到保持边；中间出现非延伸假设性边即中断，后续保持边不能自行恢复连续性或建立观察锚点。
输入清单共 30 条轨迹；旧迁移状态计数 `{'migration_failed': 1, 'migration_succeeded': 6, 'no_matching_relatively_stable_inference': 23}`，其中不满足长度2或动态门槛者仍保留排除原因。
长度2且R3–R10完整、循环内三元组动态变化的轨迹共 6 条：5 条落入稳定三元组并集且主要方向一致，1 条进入集合前像计算。

### 动态长度2轨迹

- `E0050:S009:L12`：前序 `E0046` `s3→s4` `securityModeComplete/registrationAccept`；信号 `{isInitMsg=0}`；支持前序最简不变推断；旧迁移状态 `migration_succeeded`（仅审计）；三元组 `[[0, 0, 1], [1, 1, 2], [2, 2, 3], [3, 3, 4], [4, 4, 5], [5, 5, 6], [6, 6, 0]]`。
- `E0085:S017:L17`：前序 `E0042` `s3→s7` `securityModeReject/null_action`；信号 `{isInitMsg=1}`；未落入稳定轨迹，执行集合前像计算；旧迁移状态 `no_matching_relatively_stable_inference`（仅审计）；三元组 `[[0, 7, 0], [1, 7, 0], [2, 7, 0], [3, 7, 0], [4, 7, 0], [5, 7, 0], [6, 7, 0]]`。
- `E0133:S003:L3`：前序 `E0124` `s10→s11` `deregistrationRequest/null_action`；信号 `{isInitMsg=1}`；支持前序最简不变推断；旧迁移状态 `no_matching_relatively_stable_inference`（仅审计）；三元组 `[[0, 7, 1], [1, 7, 2], [2, 7, 3], [3, 7, 4], [4, 7, 5], [5, 7, 6], [6, 7, 0]]`。
- `E0145:S005:L6`：前序 `E0160` `s13→s12` `deregistrationRequest/null_action`；信号 `{isInitMsg=1}`；支持前序最简不变推断；旧迁移状态 `no_matching_relatively_stable_inference`（仅审计）；三元组 `[[0, 7, 1], [1, 7, 2], [2, 7, 3], [3, 7, 4], [4, 7, 5], [5, 7, 6], [6, 7, 0]]`。
- `E0145:S012:L14`：前序 `E0172` `s14→s12` `deregistrationRequest/null_action`；信号 `{isInitMsg=1}`；支持前序最简不变推断；旧迁移状态 `no_matching_relatively_stable_inference`（仅审计）；三元组 `[[0, 7, 1], [1, 7, 2], [2, 7, 3], [3, 7, 4], [4, 7, 5], [5, 7, 6], [6, 7, 0]]`。
- `E0146:S012:L15`：前序 `E0172` `s14→s12` `deregistrationRequest/null_action`；信号 `{isInitMsg=1}`；支持前序最简不变推断；旧迁移状态 `no_matching_relatively_stable_inference`（仅审计）；三元组 `[[0, 0, 1], [1, 1, 2], [2, 2, 3], [3, 3, 4], [4, 4, 5], [5, 5, 6], [6, 6, 0]]`。

### 前序最简不变推断

- `E0046` `s3→s4` `securityModeComplete/registrationAccept`：`r' = r`；支持轨迹 `['E0050:S009:L12']`；末端 I/O `['registrationRequestGUTI/authenticationRequest']`。
- `E0124` `s10→s11` `deregistrationRequest/null_action`：`r' = r`；支持轨迹 `['E0133:S003:L3']`；末端 I/O `['registrationRequest/authenticationRequest']`。
- `E0160` `s13→s12` `deregistrationRequest/null_action`：`r' = r`；支持轨迹 `['E0145:S005:L6']`；末端 I/O `['registrationRequest/authenticationRequest']`。
- `E0172` `s14→s12` `deregistrationRequest/null_action`：`r' = r`；支持轨迹 `['E0145:S012:L14', 'E0146:S012:L15']`；末端 I/O `['registrationRequest/authenticationRequest', 'registrationRequestGUTI/authenticationRequest']`。

### 反向集合前像与候选赋值方案

- `E0085:S017:L17` 的前序 `E0042` `securityModeReject/null_action`：仅计算事件级伪 `r_after`，不生成边级公式。
  - 来源稳定树 `r' = ite(r < 6, r + 1, 0)`；全值域逐轮前像：R3=[6, 7]、R4=[6, 7]、R5=[6, 7]、R6=[6, 7]、R7=[6, 7]、R8=[6, 7]、R9=[6, 7]、R10=[6, 7]；跨R3–R10一致值 `[6, 7]`。
- 候选赋值方案 `A6`：E0085:S017:L17:E0042=6；它是跨重复采用同一前像值的附加假设，不代表边级公式。
- 候选赋值方案 `A7`：E0085:S017:L17:E0042=7；它是跨重复采用同一前像值的附加假设，不代表边级公式。

### 重划后的下一阶段入口

两个候选赋值方案中去重后共有 5 个真实、完整且动态的新增长度1区域；本阶段只标记资格，不重新拟合公式：
- `E0050:S009:L12`：`s4→s1` `registrationRequestGUTI/authenticationRequest`；候选赋值方案 `['A6', 'A7']`。
- `E0133:S003:L3`：`s11→s10` `registrationRequest/authenticationRequest`；候选赋值方案 `['A6', 'A7']`。
- `E0145:S005:L6`：`s12→s13` `registrationRequest/authenticationRequest`；候选赋值方案 `['A6', 'A7']`。
- `E0145:S012:L14`：`s12→s13` `registrationRequest/authenticationRequest`；候选赋值方案 `['A6', 'A7']`。
- `E0146:S012:L15`：`s12→s13` `registrationRequestGUTI/authenticationRequest`；候选赋值方案 `['A6', 'A7']`。

## 边级候选

### E0001: s0→s1

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`hypothetical_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 1}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0001:S018:L18`；{isInitMsg=1}：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)

</details>

### E0013: s1→s1

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0013:L001:L32`；{isInitMsg=0}：R3:(2,[ngksi_uplink=7],3) → R4:(3,[ngksi_uplink=7],4) → R5:(4,[ngksi_uplink=7],5) → R6:(5,[ngksi_uplink=7],6) → R7:(6,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],2) → R10:(2,[ngksi_uplink=7],3)

</details>

### E0014: s1→s1

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0014:L002:L33`；{isInitMsg=0}：R3:(2,[ngksi_uplink=7],3) → R4:(3,[ngksi_uplink=7],4) → R5:(4,[ngksi_uplink=7],5) → R6:(5,[ngksi_uplink=7],6) → R7:(6,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],2) → R10:(2,[ngksi_uplink=7],3)

</details>

### E0019: s1→s3

- input/output：`authenticationResponse/securityModeCommand`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{}]`；不参与本阶段拆分或拟合。
- 循环轨迹：16 条；R3–R10 样本：128 个。
- r_before–r_after：
  - `r' = r`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - `r' = ite(r_i < 6, r_i + 1, 0)`；作用域 `functional_subset`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0019:F002:L31`；无信号：R3:(2,[ngksi_uplink=1],2) → R4:(3,[ngksi_uplink=2],3) → R5:(4,[ngksi_uplink=3],4) → R6:(5,[ngksi_uplink=4],5) → R7:(6,[ngksi_uplink=5],6) → R8:(0,[ngksi_uplink=6],0) → R9:(1,[ngksi_uplink=0],1) → R10:(2,[ngksi_uplink=1],2)
- `E0019:S002:L1`；无信号：R3:(2,[ngksi_uplink=7],2) → R4:(3,[ngksi_uplink=7],3) → R5:(4,[ngksi_uplink=7],4) → R6:(5,[ngksi_uplink=7],5) → R7:(6,[ngksi_uplink=7],6) → R8:(0,[ngksi_uplink=7],0) → R9:(1,[ngksi_uplink=7],1) → R10:(2,[ngksi_uplink=7],2)
- `E0019:S002:L2`；无信号：R3:(2,[ngksi_uplink=1],2) → R4:(3,[ngksi_uplink=2],3) → R5:(4,[ngksi_uplink=3],4) → R6:(5,[ngksi_uplink=4],5) → R7:(6,[ngksi_uplink=5],6) → R8:(0,[ngksi_uplink=6],0) → R9:(1,[ngksi_uplink=0],1) → R10:(2,[ngksi_uplink=1],2)
- `E0019:S009:L12`；无信号：R3:(2,[ngksi_uplink=1],2) → R4:(3,[ngksi_uplink=2],3) → R5:(4,[ngksi_uplink=3],4) → R6:(5,[ngksi_uplink=4],5) → R7:(6,[ngksi_uplink=5],6) → R8:(0,[ngksi_uplink=6],0) → R9:(1,[ngksi_uplink=0],1) → R10:(2,[ngksi_uplink=1],2)
- `E0019:S017:L16`；无信号：R3:(1,[ngksi_uplink=7],1) → R4:(1,[ngksi_uplink=7],1) → R5:(1,[ngksi_uplink=7],1) → R6:(1,[ngksi_uplink=7],1) → R7:(1,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],1) → R10:(1,[ngksi_uplink=7],1)
- `E0019:S017:L17`；无信号：R3:(2,[ngksi_uplink=1],2) → R4:(3,[ngksi_uplink=2],3) → R5:(4,[ngksi_uplink=3],4) → R6:(5,[ngksi_uplink=4],5) → R7:(6,[ngksi_uplink=5],6) → R8:(0,[ngksi_uplink=6],0) → R9:(1,[ngksi_uplink=0],1) → R10:(2,[ngksi_uplink=1],2)
- `E0019:S018:L18`；无信号：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)
- `E0019:S036:L21`；无信号：R3:(4,[ngksi_uplink=7],4) → R4:(6,[ngksi_uplink=7],6) → R5:(1,[ngksi_uplink=7],1) → R6:(3,[ngksi_uplink=7],3) → R7:(5,[ngksi_uplink=7],5) → R8:(0,[ngksi_uplink=7],0) → R9:(2,[ngksi_uplink=7],2) → R10:(4,[ngksi_uplink=7],4)
- `E0019:S036:L22`；无信号：R3:(4,[ngksi_uplink=3],4)[carried_from_R2] → R4:(6,[ngksi_uplink=5],6) → R5:(1,[ngksi_uplink=0],1) → R6:(3,[ngksi_uplink=2],3) → R7:(5,[ngksi_uplink=4],5) → R8:(0,[ngksi_uplink=6],0) → R9:(2,[ngksi_uplink=1],2) → R10:(4,[ngksi_uplink=3],4)
- `E0019:S036:L23`；无信号：R3:(4,[ngksi_uplink=7],4) → R4:(6,[ngksi_uplink=7],6) → R5:(1,[ngksi_uplink=7],1) → R6:(3,[ngksi_uplink=7],3) → R7:(5,[ngksi_uplink=7],5) → R8:(0,[ngksi_uplink=7],0) → R9:(2,[ngksi_uplink=7],2) → R10:(4,[ngksi_uplink=7],4)
- `E0019:S036:L24`；无信号：R3:(4,[ngksi_uplink=3],4)[carried_from_R2] → R4:(6,[ngksi_uplink=5],6) → R5:(1,[ngksi_uplink=0],1) → R6:(3,[ngksi_uplink=2],3) → R7:(5,[ngksi_uplink=4],5) → R8:(0,[ngksi_uplink=6],0) → R9:(2,[ngksi_uplink=1],2) → R10:(4,[ngksi_uplink=3],4)
- `E0019:S037:L25`；无信号：R3:(2,[ngksi_uplink=7],2) → R4:(2,[ngksi_uplink=7],2) → R5:(2,[ngksi_uplink=7],2) → R6:(2,[ngksi_uplink=7],2) → R7:(2,[ngksi_uplink=7],2) → R8:(2,[ngksi_uplink=7],2) → R9:(2,[ngksi_uplink=7],2) → R10:(2,[ngksi_uplink=7],2)
- `E0019:S037:L26`；无信号：R3:(2,[ngksi_uplink=1],2)[carried_from_R2] → R4:(2,[ngksi_uplink=1],2) → R5:(2,[ngksi_uplink=1],2) → R6:(2,[ngksi_uplink=1],2) → R7:(2,[ngksi_uplink=1],2) → R8:(2,[ngksi_uplink=1],2) → R9:(2,[ngksi_uplink=1],2) → R10:(2,[ngksi_uplink=1],2)
- `E0019:S037:L27`；无信号：R3:(2,[ngksi_uplink=7],2) → R4:(2,[ngksi_uplink=7],2) → R5:(2,[ngksi_uplink=7],2) → R6:(2,[ngksi_uplink=7],2) → R7:(2,[ngksi_uplink=7],2) → R8:(2,[ngksi_uplink=7],2) → R9:(2,[ngksi_uplink=7],2) → R10:(2,[ngksi_uplink=7],2)
- `E0019:S037:L28`；无信号：R3:(2,[ngksi_uplink=1],2)[carried_from_R2] → R4:(2,[ngksi_uplink=1],2) → R5:(2,[ngksi_uplink=1],2) → R6:(2,[ngksi_uplink=1],2) → R7:(2,[ngksi_uplink=1],2) → R8:(2,[ngksi_uplink=1],2) → R9:(2,[ngksi_uplink=1],2) → R10:(2,[ngksi_uplink=1],2)
- `E0019:S039:L29`；无信号：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)

</details>

### E0037: s3→s1

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0037:S002:L1`；{isInitMsg=0}：R3:(2,[ngksi_uplink=7],3) → R4:(3,[ngksi_uplink=7],4) → R5:(4,[ngksi_uplink=7],5) → R6:(5,[ngksi_uplink=7],6) → R7:(6,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],2) → R10:(2,[ngksi_uplink=7],3)

</details>

### E0038: s3→s1

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - `r' = ite(r_i < 6, r_i + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0038:S002:L2`；{isInitMsg=0}：R3:(2,[ngksi_uplink=2],3) → R4:(3,[ngksi_uplink=3],4) → R5:(4,[ngksi_uplink=4],5) → R6:(5,[ngksi_uplink=5],6) → R7:(6,[ngksi_uplink=6],0) → R8:(0,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=1],2) → R10:(2,[ngksi_uplink=2],3)

</details>

### E0050: s4→s1

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`hypothetical_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：2 条；R3–R10 样本：16 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - `r' = ite(r_i < 6, r_i + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0050:F002:L31`；{isInitMsg=0}：R3:(2,[ngksi_uplink=2],3) → R4:(3,[ngksi_uplink=3],4) → R5:(4,[ngksi_uplink=4],5) → R6:(5,[ngksi_uplink=5],6) → R7:(6,[ngksi_uplink=6],0) → R8:(0,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=1],2) → R10:(2,[ngksi_uplink=2],3)
- `E0050:S009:L12`；{isInitMsg=0}：R3:(2,[ngksi_uplink=2],3) → R4:(3,[ngksi_uplink=3],4) → R5:(4,[ngksi_uplink=4],5) → R6:(5,[ngksi_uplink=5],6) → R7:(6,[ngksi_uplink=6],0) → R8:(0,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=1],2) → R10:(2,[ngksi_uplink=2],3)

</details>

### E0073: s6→s1

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`hypothetical_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：3 条；R3–R10 样本：24 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `functional_subset`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
  - 铅垂成分 x=0，y=`[0, 1]`，强度 `weak`；它是结构证据，不是更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0073:S008:L10`；{isInitMsg=0}：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)
- `E0073:S036:L21`；{isInitMsg=0}：R3:(5,[ngksi_uplink=7],6) → R4:(0,[ngksi_uplink=7],1) → R5:(2,[ngksi_uplink=7],3) → R6:(4,[ngksi_uplink=7],5) → R7:(6,[ngksi_uplink=7],0) → R8:(1,[ngksi_uplink=7],2) → R9:(3,[ngksi_uplink=7],4) → R10:(5,[ngksi_uplink=7],6)
- `E0073:S036:L23`；{isInitMsg=0}：R3:(5,[ngksi_uplink=7],6) → R4:(0,[ngksi_uplink=7],1) → R5:(2,[ngksi_uplink=7],3) → R6:(4,[ngksi_uplink=7],5) → R7:(6,[ngksi_uplink=7],0) → R8:(1,[ngksi_uplink=7],2) → R9:(3,[ngksi_uplink=7],4) → R10:(5,[ngksi_uplink=7],6)

</details>

### E0085: s7→s8

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`hypothetical_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 1}]`；不参与本阶段拆分或拟合。
- 循环轨迹：8 条；R3–R10 样本：64 个。
- r_before–r_after：
  - `r' = 0`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0085:S010:L13`；{isInitMsg=1}：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)
- `E0085:S017:L16`；{isInitMsg=1}：R3:(1,[ngksi_uplink=7],0) → R4:(1,[ngksi_uplink=7],0) → R5:(1,[ngksi_uplink=7],0) → R6:(1,[ngksi_uplink=7],0) → R7:(1,[ngksi_uplink=7],0) → R8:(1,[ngksi_uplink=7],0) → R9:(1,[ngksi_uplink=7],0) → R10:(1,[ngksi_uplink=7],0)
- `E0085:S017:L17`；{isInitMsg=1}：R3:(2,[ngksi_uplink=7],0) → R4:(3,[ngksi_uplink=7],0) → R5:(4,[ngksi_uplink=7],0) → R6:(5,[ngksi_uplink=7],0) → R7:(6,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(1,[ngksi_uplink=7],0) → R10:(2,[ngksi_uplink=7],0)
- `E0085:S037:L25`；{isInitMsg=1}：R3:(2,[ngksi_uplink=7],0) → R4:(2,[ngksi_uplink=7],0) → R5:(2,[ngksi_uplink=7],0) → R6:(2,[ngksi_uplink=7],0) → R7:(2,[ngksi_uplink=7],0) → R8:(2,[ngksi_uplink=7],0) → R9:(2,[ngksi_uplink=7],0) → R10:(2,[ngksi_uplink=7],0)
- `E0085:S037:L26`；{isInitMsg=1}：R3:(2,[ngksi_uplink=7],0) → R4:(2,[ngksi_uplink=7],0) → R5:(2,[ngksi_uplink=7],0) → R6:(2,[ngksi_uplink=7],0) → R7:(2,[ngksi_uplink=7],0) → R8:(2,[ngksi_uplink=7],0) → R9:(2,[ngksi_uplink=7],0) → R10:(2,[ngksi_uplink=7],0)
- `E0085:S037:L27`；{isInitMsg=1}：R3:(2,[ngksi_uplink=7],0) → R4:(2,[ngksi_uplink=7],0) → R5:(2,[ngksi_uplink=7],0) → R6:(2,[ngksi_uplink=7],0) → R7:(2,[ngksi_uplink=7],0) → R8:(2,[ngksi_uplink=7],0) → R9:(2,[ngksi_uplink=7],0) → R10:(2,[ngksi_uplink=7],0)
- `E0085:S037:L28`；{isInitMsg=1}：R3:(2,[ngksi_uplink=7],0) → R4:(2,[ngksi_uplink=7],0) → R5:(2,[ngksi_uplink=7],0) → R6:(2,[ngksi_uplink=7],0) → R7:(2,[ngksi_uplink=7],0) → R8:(2,[ngksi_uplink=7],0) → R9:(2,[ngksi_uplink=7],0) → R10:(2,[ngksi_uplink=7],0)
- `E0085:S039:L29`；{isInitMsg=1}：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)

</details>

### E0097: s8→s1

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0097:S017:L16`；{isInitMsg=0}：R3:(0,[ngksi_uplink=7],1) → R4:(0,[ngksi_uplink=7],1) → R5:(0,[ngksi_uplink=7],1) → R6:(0,[ngksi_uplink=7],1) → R7:(0,[ngksi_uplink=7],1) → R8:(0,[ngksi_uplink=7],1) → R9:(0,[ngksi_uplink=7],1) → R10:(0,[ngksi_uplink=7],1)

</details>

### E0098: s8→s1

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=0，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。
- r_i–r_after：
  - `r' = ite(r_i < 6, r_i + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0098:S017:L17`；{isInitMsg=0}：R3:(0,[ngksi_uplink=2],3) → R4:(0,[ngksi_uplink=3],4) → R5:(0,[ngksi_uplink=4],5) → R6:(0,[ngksi_uplink=5],6) → R7:(0,[ngksi_uplink=6],0) → R8:(0,[ngksi_uplink=0],1) → R9:(0,[ngksi_uplink=1],2) → R10:(0,[ngksi_uplink=2],3)

</details>

### E0103: s8→s9

- input/output：`authenticationResponse/securityModeCommand`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{}]`；不参与本阶段拆分或拟合。
- 循环轨迹：6 条；R3–R10 样本：48 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0103:S010:L13`；无信号：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)
- `E0103:S037:L25`；无信号：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)
- `E0103:S037:L26`；无信号：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)
- `E0103:S037:L27`；无信号：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)
- `E0103:S037:L28`；无信号：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)
- `E0103:S039:L29`；无信号：R3:(0,[ngksi_uplink=7],0) → R4:(0,[ngksi_uplink=7],0) → R5:(0,[ngksi_uplink=7],0) → R6:(0,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],0) → R9:(0,[ngksi_uplink=7],0) → R10:(0,[ngksi_uplink=7],0)

</details>

### E0109: s9→s10

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：2 条；R3–R10 样本：16 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0109:S037:L25`；{isInitMsg=0}：R3:(0,[ngksi_uplink=7],1) → R4:(0,[ngksi_uplink=7],1) → R5:(0,[ngksi_uplink=7],1) → R6:(0,[ngksi_uplink=7],1) → R7:(0,[ngksi_uplink=7],1) → R8:(0,[ngksi_uplink=7],1) → R9:(0,[ngksi_uplink=7],1) → R10:(0,[ngksi_uplink=7],1)
- `E0109:S037:L26`；{isInitMsg=0}：R3:(0,[ngksi_uplink=7],1) → R4:(0,[ngksi_uplink=7],1) → R5:(0,[ngksi_uplink=7],1) → R6:(0,[ngksi_uplink=7],1) → R7:(0,[ngksi_uplink=7],1) → R8:(0,[ngksi_uplink=7],1) → R9:(0,[ngksi_uplink=7],1) → R10:(0,[ngksi_uplink=7],1)

</details>

### E0110: s9→s10

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：2 条；R3–R10 样本：16 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0110:S037:L27`；{isInitMsg=0}：R3:(0,[ngksi_uplink=0],1) → R4:(0,[ngksi_uplink=0],1) → R5:(0,[ngksi_uplink=0],1) → R6:(0,[ngksi_uplink=0],1) → R7:(0,[ngksi_uplink=0],1) → R8:(0,[ngksi_uplink=0],1) → R9:(0,[ngksi_uplink=0],1) → R10:(0,[ngksi_uplink=0],1)
- `E0110:S037:L28`；{isInitMsg=0}：R3:(0,[ngksi_uplink=0],1) → R4:(0,[ngksi_uplink=0],1) → R5:(0,[ngksi_uplink=0],1) → R6:(0,[ngksi_uplink=0],1) → R7:(0,[ngksi_uplink=0],1) → R8:(0,[ngksi_uplink=0],1) → R9:(0,[ngksi_uplink=0],1) → R10:(0,[ngksi_uplink=0],1)

</details>

### E0121: s10→s10

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0121:L003:L34`；{isInitMsg=0}：R3:(3,[ngksi_uplink=7],4) → R4:(4,[ngksi_uplink=7],5) → R5:(5,[ngksi_uplink=7],6) → R6:(6,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],2) → R9:(2,[ngksi_uplink=7],3) → R10:(3,[ngksi_uplink=7],4)

</details>

### E0122: s10→s10

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0122:L004:L35`；{isInitMsg=0}：R3:(1,[ngksi_uplink=0],1) → R4:(1,[ngksi_uplink=0],1) → R5:(1,[ngksi_uplink=0],1) → R6:(1,[ngksi_uplink=0],1) → R7:(1,[ngksi_uplink=0],1) → R8:(1,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=0],1) → R10:(1,[ngksi_uplink=0],1)

</details>

### E0127: s10→s17

- input/output：`authenticationResponse/securityModeCommand`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{}]`；不参与本阶段拆分或拟合。
- 循环轨迹：7 条；R3–R10 样本：56 个。
- r_before–r_after：
  - `r' = r`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - `r' = ite(r_i < 6, r_i + 1, 0)`；作用域 `functional_subset`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0127:S004:L4`；无信号：R3:(3,[ngksi_uplink=7],3) → R4:(4,[ngksi_uplink=7],4) → R5:(5,[ngksi_uplink=7],5) → R6:(6,[ngksi_uplink=7],6) → R7:(0,[ngksi_uplink=7],0) → R8:(1,[ngksi_uplink=7],1) → R9:(2,[ngksi_uplink=7],2) → R10:(3,[ngksi_uplink=7],3)
- `E0127:S004:L5`；无信号：R3:(3,[ngksi_uplink=2],3) → R4:(4,[ngksi_uplink=3],4) → R5:(5,[ngksi_uplink=4],5) → R6:(6,[ngksi_uplink=5],6) → R7:(0,[ngksi_uplink=6],0) → R8:(1,[ngksi_uplink=0],1) → R9:(2,[ngksi_uplink=1],2) → R10:(3,[ngksi_uplink=2],3)
- `E0127:S037:L25`；无信号：R3:(1,[ngksi_uplink=7],1) → R4:(1,[ngksi_uplink=7],1) → R5:(1,[ngksi_uplink=7],1) → R6:(1,[ngksi_uplink=7],1) → R7:(1,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],1) → R10:(1,[ngksi_uplink=7],1)
- `E0127:S037:L26`；无信号：R3:(1,[ngksi_uplink=7],1) → R4:(1,[ngksi_uplink=7],1) → R5:(1,[ngksi_uplink=7],1) → R6:(1,[ngksi_uplink=7],1) → R7:(1,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],1) → R10:(1,[ngksi_uplink=7],1)
- `E0127:S037:L27`；无信号：R3:(1,[ngksi_uplink=0],1) → R4:(1,[ngksi_uplink=0],1) → R5:(1,[ngksi_uplink=0],1) → R6:(1,[ngksi_uplink=0],1) → R7:(1,[ngksi_uplink=0],1) → R8:(1,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=0],1) → R10:(1,[ngksi_uplink=0],1)
- `E0127:S037:L28`；无信号：R3:(1,[ngksi_uplink=0],1) → R4:(1,[ngksi_uplink=0],1) → R5:(1,[ngksi_uplink=0],1) → R6:(1,[ngksi_uplink=0],1) → R7:(1,[ngksi_uplink=0],1) → R8:(1,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=0],1) → R10:(1,[ngksi_uplink=0],1)
- `E0127:S039:L29`；无信号：R3:(1,[ngksi_uplink=7],1) → R4:(1,[ngksi_uplink=7],1) → R5:(1,[ngksi_uplink=7],1) → R6:(1,[ngksi_uplink=7],1) → R7:(1,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],1) → R10:(1,[ngksi_uplink=7],1)

</details>

### E0133: s11→s10

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`hypothetical_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 1}]`；不参与本阶段拆分或拟合。
- 循环轨迹：3 条；R3–R10 样本：24 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0133:F001:L30`；{isInitMsg=1}：R3:(3,[ngksi_uplink=7],4) → R4:(4,[ngksi_uplink=7],5) → R5:(5,[ngksi_uplink=7],6) → R6:(6,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],2) → R9:(2,[ngksi_uplink=7],3) → R10:(3,[ngksi_uplink=7],4)
- `E0133:S003:L3`；{isInitMsg=1}：R3:(3,[ngksi_uplink=7],4) → R4:(4,[ngksi_uplink=7],5) → R5:(5,[ngksi_uplink=7],6) → R6:(6,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],2) → R9:(2,[ngksi_uplink=7],3) → R10:(3,[ngksi_uplink=7],4)
- `E0133:S039:L29`；{isInitMsg=1}：R3:(0,[ngksi_uplink=7],1) → R4:(0,[ngksi_uplink=7],1) → R5:(0,[ngksi_uplink=7],1) → R6:(0,[ngksi_uplink=7],1) → R7:(0,[ngksi_uplink=7],1) → R8:(0,[ngksi_uplink=7],1) → R9:(0,[ngksi_uplink=7],1) → R10:(0,[ngksi_uplink=7],1)

</details>

### E0145: s12→s13

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`hypothetical_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 1}]`；不参与本阶段拆分或拟合。
- 循环轨迹：5 条；R3–R10 样本：40 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0145:S005:L6`；{isInitMsg=1}：R3:(2,[ngksi_uplink=7],3) → R4:(3,[ngksi_uplink=7],4) → R5:(4,[ngksi_uplink=7],5) → R6:(5,[ngksi_uplink=7],6) → R7:(6,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],2) → R10:(2,[ngksi_uplink=7],3)
- `E0145:S012:L14`；{isInitMsg=1}：R3:(2,[ngksi_uplink=7],3) → R4:(3,[ngksi_uplink=7],4) → R5:(4,[ngksi_uplink=7],5) → R6:(5,[ngksi_uplink=7],6) → R7:(6,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],2) → R10:(2,[ngksi_uplink=7],3)
- `E0145:S022:L19`；{isInitMsg=1}：R3:(2,[ngksi_uplink=7],3) → R4:(3,[ngksi_uplink=7],4) → R5:(4,[ngksi_uplink=7],5) → R6:(5,[ngksi_uplink=7],6) → R7:(6,[ngksi_uplink=7],0) → R8:(0,[ngksi_uplink=7],1) → R9:(1,[ngksi_uplink=7],2) → R10:(2,[ngksi_uplink=7],3)
- `E0145:S036:L21`；{isInitMsg=1}：R3:(4,[ngksi_uplink=7],5) → R4:(6,[ngksi_uplink=7],0) → R5:(1,[ngksi_uplink=7],2) → R6:(3,[ngksi_uplink=7],4) → R7:(5,[ngksi_uplink=7],6) → R8:(0,[ngksi_uplink=7],1) → R9:(2,[ngksi_uplink=7],3) → R10:(4,[ngksi_uplink=7],5)
- `E0145:S036:L22`；{isInitMsg=1}：R3:(4,[ngksi_uplink=7],5) → R4:(6,[ngksi_uplink=7],0) → R5:(1,[ngksi_uplink=7],2) → R6:(3,[ngksi_uplink=7],4) → R7:(5,[ngksi_uplink=7],6) → R8:(0,[ngksi_uplink=7],1) → R9:(2,[ngksi_uplink=7],3) → R10:(4,[ngksi_uplink=7],5)

</details>

### E0146: s12→s13

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`hypothetical_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 1}]`；不参与本阶段拆分或拟合。
- 循环轨迹：5 条；R3–R10 样本：40 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `functional_subset`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
  - 铅垂成分 x=1，y=`[1, 2]`，强度 `weak`；它是结构证据，不是更新公式。
- r_i–r_after：
  - `r' = ite(r_i < 6, r_i + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0146:S005:L7`；{isInitMsg=1}：R3:(1,[ngksi_uplink=0],1) → R4:(1,[ngksi_uplink=0],1) → R5:(1,[ngksi_uplink=0],1) → R6:(1,[ngksi_uplink=0],1) → R7:(1,[ngksi_uplink=0],1) → R8:(1,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=0],1) → R10:(1,[ngksi_uplink=0],1)
- `E0146:S012:L15`；{isInitMsg=1}：R3:(2,[ngksi_uplink=2],3) → R4:(3,[ngksi_uplink=3],4) → R5:(4,[ngksi_uplink=4],5) → R6:(5,[ngksi_uplink=5],6) → R7:(6,[ngksi_uplink=6],0) → R8:(0,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=1],2) → R10:(2,[ngksi_uplink=2],3)
- `E0146:S022:L20`；{isInitMsg=1}：R3:(2,[ngksi_uplink=2],3) → R4:(3,[ngksi_uplink=3],4) → R5:(4,[ngksi_uplink=4],5) → R6:(5,[ngksi_uplink=5],6) → R7:(6,[ngksi_uplink=6],0) → R8:(0,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=1],2) → R10:(2,[ngksi_uplink=2],3)
- `E0146:S036:L23`；{isInitMsg=1}：R3:(4,[ngksi_uplink=4],5) → R4:(6,[ngksi_uplink=6],0) → R5:(1,[ngksi_uplink=1],2) → R6:(3,[ngksi_uplink=3],4) → R7:(5,[ngksi_uplink=5],6) → R8:(0,[ngksi_uplink=0],1) → R9:(2,[ngksi_uplink=2],3) → R10:(4,[ngksi_uplink=4],5)
- `E0146:S036:L24`；{isInitMsg=1}：R3:(4,[ngksi_uplink=4],5) → R4:(6,[ngksi_uplink=6],0) → R5:(1,[ngksi_uplink=1],2) → R6:(3,[ngksi_uplink=3],4) → R7:(5,[ngksi_uplink=5],6) → R8:(0,[ngksi_uplink=0],1) → R9:(2,[ngksi_uplink=2],3) → R10:(4,[ngksi_uplink=4],5)

</details>

### E0157: s13→s13

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0157:L005:L36`；{isInitMsg=0}：R3:(3,[ngksi_uplink=7],4) → R4:(4,[ngksi_uplink=7],5) → R5:(5,[ngksi_uplink=7],6) → R6:(6,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],2) → R9:(2,[ngksi_uplink=7],3) → R10:(3,[ngksi_uplink=7],4)

</details>

### E0158: s13→s13

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0158:L006:L37`；{isInitMsg=0}：R3:(1,[ngksi_uplink=0],1) → R4:(1,[ngksi_uplink=0],1) → R5:(1,[ngksi_uplink=0],1) → R6:(1,[ngksi_uplink=0],1) → R7:(1,[ngksi_uplink=0],1) → R8:(1,[ngksi_uplink=0],1) → R9:(1,[ngksi_uplink=0],1) → R10:(1,[ngksi_uplink=0],1)

</details>

### E0163: s13→s14

- input/output：`authenticationResponse/securityModeCommand`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{}]`；不参与本阶段拆分或拟合。
- 循环轨迹：10 条；R3–R10 样本：80 个。
- r_before–r_after：
  - `r' = r`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - `r' = ite(r_i < 6, r_i + 1, 0)`；作用域 `functional_subset`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0163:S006:L8`；无信号：R3:(3,[ngksi_uplink=7],3) → R4:(4,[ngksi_uplink=7],4) → R5:(5,[ngksi_uplink=7],5) → R6:(6,[ngksi_uplink=7],6) → R7:(0,[ngksi_uplink=7],0) → R8:(1,[ngksi_uplink=7],1) → R9:(2,[ngksi_uplink=7],2) → R10:(3,[ngksi_uplink=7],3)
- `E0163:S006:L9`；无信号：R3:(3,[ngksi_uplink=2],3) → R4:(4,[ngksi_uplink=3],4) → R5:(5,[ngksi_uplink=4],5) → R6:(6,[ngksi_uplink=5],6) → R7:(0,[ngksi_uplink=6],0) → R8:(1,[ngksi_uplink=0],1) → R9:(2,[ngksi_uplink=1],2) → R10:(3,[ngksi_uplink=2],3)
- `E0163:S012:L14`；无信号：R3:(3,[ngksi_uplink=7],3) → R4:(4,[ngksi_uplink=7],4) → R5:(5,[ngksi_uplink=7],5) → R6:(6,[ngksi_uplink=7],6) → R7:(0,[ngksi_uplink=7],0) → R8:(1,[ngksi_uplink=7],1) → R9:(2,[ngksi_uplink=7],2) → R10:(3,[ngksi_uplink=7],3)
- `E0163:S012:L15`；无信号：R3:(3,[ngksi_uplink=2],3) → R4:(4,[ngksi_uplink=3],4) → R5:(5,[ngksi_uplink=4],5) → R6:(6,[ngksi_uplink=5],6) → R7:(0,[ngksi_uplink=6],0) → R8:(1,[ngksi_uplink=0],1) → R9:(2,[ngksi_uplink=1],2) → R10:(3,[ngksi_uplink=2],3)
- `E0163:S022:L19`；无信号：R3:(3,[ngksi_uplink=7],3) → R4:(4,[ngksi_uplink=7],4) → R5:(5,[ngksi_uplink=7],5) → R6:(6,[ngksi_uplink=7],6) → R7:(0,[ngksi_uplink=7],0) → R8:(1,[ngksi_uplink=7],1) → R9:(2,[ngksi_uplink=7],2) → R10:(3,[ngksi_uplink=7],3)
- `E0163:S022:L20`；无信号：R3:(3,[ngksi_uplink=2],3) → R4:(4,[ngksi_uplink=3],4) → R5:(5,[ngksi_uplink=4],5) → R6:(6,[ngksi_uplink=5],6) → R7:(0,[ngksi_uplink=6],0) → R8:(1,[ngksi_uplink=0],1) → R9:(2,[ngksi_uplink=1],2) → R10:(3,[ngksi_uplink=2],3)
- `E0163:S036:L21`；无信号：R3:(5,[ngksi_uplink=7],5) → R4:(0,[ngksi_uplink=7],0) → R5:(2,[ngksi_uplink=7],2) → R6:(4,[ngksi_uplink=7],4) → R7:(6,[ngksi_uplink=7],6) → R8:(1,[ngksi_uplink=7],1) → R9:(3,[ngksi_uplink=7],3) → R10:(5,[ngksi_uplink=7],5)
- `E0163:S036:L22`；无信号：R3:(5,[ngksi_uplink=7],5) → R4:(0,[ngksi_uplink=7],0) → R5:(2,[ngksi_uplink=7],2) → R6:(4,[ngksi_uplink=7],4) → R7:(6,[ngksi_uplink=7],6) → R8:(1,[ngksi_uplink=7],1) → R9:(3,[ngksi_uplink=7],3) → R10:(5,[ngksi_uplink=7],5)
- `E0163:S036:L23`；无信号：R3:(5,[ngksi_uplink=4],5) → R4:(0,[ngksi_uplink=6],0) → R5:(2,[ngksi_uplink=1],2) → R6:(4,[ngksi_uplink=3],4) → R7:(6,[ngksi_uplink=5],6) → R8:(1,[ngksi_uplink=0],1) → R9:(3,[ngksi_uplink=2],3) → R10:(5,[ngksi_uplink=4],5)
- `E0163:S036:L24`；无信号：R3:(5,[ngksi_uplink=4],5) → R4:(0,[ngksi_uplink=6],0) → R5:(2,[ngksi_uplink=1],2) → R6:(4,[ngksi_uplink=3],4) → R7:(6,[ngksi_uplink=5],6) → R8:(1,[ngksi_uplink=0],1) → R9:(3,[ngksi_uplink=2],3) → R10:(5,[ngksi_uplink=4],5)

</details>

### E0169: s14→s13

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0169:S006:L8`；{isInitMsg=0}：R3:(3,[ngksi_uplink=7],4) → R4:(4,[ngksi_uplink=7],5) → R5:(5,[ngksi_uplink=7],6) → R6:(6,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],2) → R9:(2,[ngksi_uplink=7],3) → R10:(3,[ngksi_uplink=7],4)

</details>

### E0170: s14→s13

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - `r' = ite(r_i < 6, r_i + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0170:S006:L9`；{isInitMsg=0}：R3:(3,[ngksi_uplink=3],4) → R4:(4,[ngksi_uplink=4],5) → R5:(5,[ngksi_uplink=5],6) → R6:(6,[ngksi_uplink=6],0) → R7:(0,[ngksi_uplink=0],1) → R8:(1,[ngksi_uplink=1],2) → R9:(2,[ngksi_uplink=2],3) → R10:(3,[ngksi_uplink=3],4)

</details>

### E0181: s15→s1

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`hypothetical_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 1}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0181:S039:L29`；{isInitMsg=1}：R3:(1,[ngksi_uplink=7],0) → R4:(1,[ngksi_uplink=7],0) → R5:(1,[ngksi_uplink=7],0) → R6:(1,[ngksi_uplink=7],0) → R7:(1,[ngksi_uplink=7],0) → R8:(1,[ngksi_uplink=7],0) → R9:(1,[ngksi_uplink=7],0) → R10:(1,[ngksi_uplink=7],0)

</details>

### E0193: s16→s1

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`hypothetical_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：2 条；R3–R10 样本：16 个。
- r_before–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0193:S037:L25`；{isInitMsg=0}：R3:(1,[ngksi_uplink=7],2) → R4:(1,[ngksi_uplink=7],2) → R5:(1,[ngksi_uplink=7],2) → R6:(1,[ngksi_uplink=7],2) → R7:(1,[ngksi_uplink=7],2) → R8:(1,[ngksi_uplink=7],2) → R9:(1,[ngksi_uplink=7],2) → R10:(1,[ngksi_uplink=7],2)
- `E0193:S037:L27`；{isInitMsg=0}：R3:(1,[ngksi_uplink=7],2) → R4:(1,[ngksi_uplink=7],2) → R5:(1,[ngksi_uplink=7],2) → R6:(1,[ngksi_uplink=7],2) → R7:(1,[ngksi_uplink=7],2) → R8:(1,[ngksi_uplink=7],2) → R9:(1,[ngksi_uplink=7],2) → R10:(1,[ngksi_uplink=7],2)

</details>

### E0205: s17→s10

- input/output：`registrationRequest/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - 仅含静态点或纯铅垂退化轨迹；不生成更新公式。
  - 铅垂成分 x=7，y=`[0, 1, 2, 3, 4, 5, 6]`，强度 `core`；它是结构证据，不是更新公式。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0205:S004:L4`；{isInitMsg=0}：R3:(3,[ngksi_uplink=7],4) → R4:(4,[ngksi_uplink=7],5) → R5:(5,[ngksi_uplink=7],6) → R6:(6,[ngksi_uplink=7],0) → R7:(0,[ngksi_uplink=7],1) → R8:(1,[ngksi_uplink=7],2) → R9:(2,[ngksi_uplink=7],3) → R10:(3,[ngksi_uplink=7],4)

</details>

### E0206: s17→s10

- input/output：`registrationRequestGUTI/authenticationRequest`；候选等级仅作审计：`relatively_stable_candidate`。
- 观察到的信号上下文：`[{'isInitMsg': 0}]`；不参与本阶段拆分或拟合。
- 循环轨迹：1 条；R3–R10 样本：8 个。
- r_before–r_after：
  - `r' = ite(r < 6, r + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。
- r_i–r_after：
  - `r' = ite(r_i < 6, r_i + 1, 0)`；作用域 `full_projection`；证据 `core/observationally_exact`；覆盖 x=`[0, 1, 2, 3, 4, 5, 6]`，缺口=`[]`；方向 `forward_majority`。

<details>
<summary>展开全部循环的具体轨迹</summary>

- `E0206:S004:L5`；{isInitMsg=0}：R3:(3,[ngksi_uplink=3],4) → R4:(4,[ngksi_uplink=4],5) → R5:(5,[ngksi_uplink=5],6) → R6:(6,[ngksi_uplink=6],0) → R7:(0,[ngksi_uplink=0],1) → R8:(1,[ngksi_uplink=1],2) → R9:(2,[ngksi_uplink=2],3) → R10:(3,[ngksi_uplink=3],4)

</details>

## 候选组反向索引

- `B-before-after-371581a68c` / `authenticationResponse/securityModeCommand` / r_before–r_after / `r' = r`
  - 拥有者：`['E0019', 'E0127', 'E0163']`。
  - 核心拥有者：`['E0019', 'E0127', 'E0163']`。
  - 相容弱证据：`['E0103']`；仅部分分支相容：`[]`。
- `B-input-after-13070c55f1` / `authenticationResponse/securityModeCommand` / r_i–r_after / `r' = ite(r_i < 6, r_i + 1, 0)`
  - 拥有者：`['E0019', 'E0127', 'E0163']`。
  - 核心拥有者：`['E0019', 'E0127', 'E0163']`。
  - 相容弱证据：`[]`；仅部分分支相容：`['E0103']`。
- `B-before-after-1f61d49aba` / `registrationRequest/authenticationRequest` / r_before–r_after / `r' = ite(r < 6, r + 1, 0)`
  - 拥有者：`['E0013', 'E0037', 'E0073', 'E0121', 'E0133', 'E0145', 'E0157', 'E0169', 'E0205']`。
  - 核心拥有者：`['E0013', 'E0037', 'E0073', 'E0121', 'E0133', 'E0145', 'E0157', 'E0169', 'E0205']`。
  - 相容弱证据：`[]`；仅部分分支相容：`['E0097', 'E0109', 'E0193']`。
- `B-before-after-cf646097a2` / `registrationRequest/authenticationRequest` / r_before–r_after / `r' = 0`
  - 拥有者：`['E0085']`。
  - 核心拥有者：`['E0085']`。
  - 相容弱证据：`['E0001', 'E0181']`；仅部分分支相容：`[]`。
- `B-before-after-b62725f500` / `registrationRequestGUTI/authenticationRequest` / r_before–r_after / `r' = ite(r < 6, r + 1, 0)`
  - 拥有者：`['E0014', 'E0038', 'E0050', 'E0146', 'E0170', 'E0206']`。
  - 核心拥有者：`['E0014', 'E0038', 'E0050', 'E0146', 'E0170', 'E0206']`。
  - 相容弱证据：`[]`；仅部分分支相容：`['E0098', 'E0110']`。
- `B-input-after-6f2677e834` / `registrationRequestGUTI/authenticationRequest` / r_i–r_after / `r' = ite(r_i < 6, r_i + 1, 0)`
  - 拥有者：`['E0038', 'E0050', 'E0098', 'E0146', 'E0170', 'E0206']`。
  - 核心拥有者：`['E0038', 'E0050', 'E0098', 'E0146', 'E0170', 'E0206']`。
  - 相容弱证据：`[]`；仅部分分支相容：`['E0014', 'E0110', 'E0122', 'E0158']`。

## 解释边界

- 静态点和纯铅垂轨迹不产生公式、不进入拟合门槛或方向计票；与既有公式相符时只作相容证据，其余点保持未解决。
- 常数候选必须由同一条轨迹内至少两个不同 x 构成的真实水平线支持；多个静态点不能拼成水平线。
- 有缺口的候选只说明已观察 x 上精确成立，不把未观察 x 当作验证结果。
- 第一阶段候选类型和信号上下文只作审计，不参与边级候选发现或候选组划分。
- 稳定性推断聚合只联合相对稳定推断的源边；优先选择完整简单公式，仅在唯一输入寄存器值形成铅垂成分时构造并逐点验证跨投影模型树。
- 旧稳定聚合中的 `s` 只作为公式适用条件。新稳定推断比较不同信号分支：公式相同即合并，只有不同且均精确时才生成信号根节点。
- 前序最简阶段独立于旧迁移检验与旧前序反推，只把旧状态保留为审计字段；其页面入口暂时禁用，但 JSON 审计数据继续保留。
- 前序不变推断是带稳定轨迹包含与主要方向前提的可反驳假设；反向集合前像只更新证据事件的伪 `r_after`，不产生前序边公式。
- 伪下行只进行一轮观察区域重划；保持边仅连续延伸此前真实 KSI 下行，遇到非延伸假设性边即中断，不能自行建立观察锚点。伪边界不构成独立稳定证据，新增长度1区域本阶段不重新拟合公式。

## 排版检查

报告不用宽表格；长公式、EID 和循环轨迹放在可折叠段落中，公式与路径允许自然换行，窄屏不会由固定列宽造成横向溢出。
