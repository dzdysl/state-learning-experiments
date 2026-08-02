# hypothesis_13 全量二分类回溯检验报告

## 1. 数据来源与判定规则

- 原始 DOT：`D:\state-learning-lab\projects\state-learning-experiments\experiments\open5gs\ueransim-smc-context-pdu-selection\open5gs266-smc-context-h13-interrupted-20260730\evidence\hypotheses\hypothesis_13.dot`
- SHA-256：`465fc8f4e5cba3715ec8745cbc81f9d24f5876fdb37d455f858f02f01815523a`
- 入口策略：`strict`；默认入口必须恰有一个 signature 差异位置。
- 节点按 `(轮次, 父类, 无序子类对)` 去重；从最后有效轮向第1轮扫描。
- 多位置对子不独立启动；仅在回溯需要时作为中间节点，同一上游对子合并信令，不同上游对子分别分支。

| 位置 | 输入 | 简写 |
|---:|---|---|
| 1 | `registrationRequest` | `RR` |
| 2 | `registrationRequestGUTI` | `RRG` |
| 3 | `registrationComplete` | `RC` |
| 4 | `deregistrationRequest` | `DR` |
| 5 | `serviceRequest` | `SR` |
| 6 | `securityModeReject` | `SMR` |
| 7 | `authenticationResponse` | `AR` |
| 8 | `authenticationFailure` | `AF` |
| 9 | `deregistrationAccept` | `DA` |
| 10 | `securityModeComplete` | `SMC` |
| 11 | `identityResponse` | `IR` |
| 12 | `configurationUpdateComplete` | `CUC` |

## 2. 重算统计

| 轮次 | 拆分父类 | 候选对子 | 严格入口对子 | 非严格对子 |
|---:|---:|---:|---:|---:|
| 4 | 1 | 1 | 1 | 0 |
| 3 | 3 | 3 | 2 | 1 |
| 2 | 2 | 4 | 3 | 1 |
| 1 | 2 | 4 | 2 | 2 |
| 合计 | — | 12 | 8 | 4 |

- 状态数：17；拆分父类数：`[2, 2, 3, 1, 0]`。
- 独立入口：4；回溯节点：8；已覆盖节点：4。
- 轮间关系：6；终点关系：2；初始关键差异：2。

## 3. 独立入口与去重路径

| 序号 | 入口 | 轮次与父类 | 子类对 | 去重路径 |
|---:|---|---|---|---|
| 1 | `B01` | 第4轮 `NG1` | `NG1/NG2` | B01 --DR→ B02；B02 --RR→ B05；B05 --AR→ B07；B07 --SMC→ K1 |
| 2 | `B03` | 第3轮 `D2` | `D3/D4` | B03 --RR→ B05；B05 --AR→ B07；B07 --SMC→ K1 |
| 3 | `B04` | 第2轮 `A1` | `A1/A2` | B04 --DR→ B08；B08 --RRG→ K2 |
| 4 | `B06` | 第2轮 `N1` | `N1/N3` | B06 --DR→ B08；B08 --RRG→ K2 |

### 固定 A/B 具体状态轨迹

#### `B01`

- 路径 1 / 输入变体 1：`deregistrationRequest registrationRequest authenticationResponse securityModeComplete` → `stage0:A/S`
  - branch A（入口子类 `NG1`，起点 `s6`）：`s6 → s0 → s1 → s3 → s4`
  - branch B（入口子类 `NG2`，起点 `s16`）：`s16 → s11 → s10 → s9 → s9`

#### `B03`

- 路径 1 / 输入变体 1：`registrationRequest authenticationResponse securityModeComplete` → `stage0:A/S`
  - branch A（入口子类 `D3`，起点 `s7`）：`s7 → s8 → s9 → s9`
  - branch B（入口子类 `D4`，起点 `s15`）：`s15 → s1 → s3 → s4`

#### `B04`

- 路径 1 / 输入变体 1：`deregistrationRequest registrationRequestGUTI` → `stage0:N/NG`
  - branch A（入口子类 `A1`，起点 `s3`）：`s3 → s0 → s6`
  - branch B（入口子类 `A2`，起点 `s14`）：`s14 → s12 → s13`

#### `B06`

- 路径 1 / 输入变体 1：`deregistrationRequest registrationRequestGUTI` → `stage0:N/NG`
  - branch A（入口子类 `N1`，起点 `s1`）：`s1 → s0 → s6`
  - branch B（入口子类 `N3`，起点 `s13`）：`s13 → s12 → s13`


## 4. 去重后的回溯节点：完整 signature、代表转移与全部成员转移变体

### 第 4 轮

#### `B01`：父类 `NG1` 的 `NG1/NG2`（独立入口）

- 子类 `NG1` = {s6}
  - signature：`(N1, NG1, NG1, D1, NG1, NG1, NG1, NG1, NG1, NG1, N1, NG1)`
- 子类 `NG2` = {s16}
  - signature：`(N1, NG1, NG1, D2, NG1, NG1, NG1, NG1, NG1, NG1, N1, NG1)`

| 位置 | 输入 | 简写 | 上游类别对 | `NG1` 代表转移 | `NG2` 代表转移 |
|---:|---|---|---|---|---|
| 4 | `deregistrationRequest` | `DR` | `D1/D2` | `s6 --deregistrationRequest / null_action→ s0` | `s16 --deregistrationRequest / null_action→ s11` |
|  | 全部成员转移变体 |  |  | `s6 --deregistrationRequest / null_action→ s0` | `s16 --deregistrationRequest / null_action→ s11` |

- 回溯关系：`B01 --DR→ B02`。
- 全部成员状态交叉对在模型内行为等价：`False`。
  - `s6/s16`：最短区分序列 `deregistrationRequest registrationRequest authenticationResponse securityModeComplete` → `s3:registrationAccept/s9:null_action`。

### 第 3 轮

#### `B02`：父类 `D1` 的 `D1/D2`（已由较晚路径覆盖）

- 子类 `D1` = {s0}
  - signature：`(N1, NG1, D1, D1, D1, X, X, X, X, D1, D1, D1)`
- 子类 `D2` = {s11}
  - signature：`(N2, NG1, D1, D1, D1, X, X, X, X, D1, D1, D1)`

| 位置 | 输入 | 简写 | 上游类别对 | `D1` 代表转移 | `D2` 代表转移 |
|---:|---|---|---|---|---|
| 1 | `registrationRequest` | `RR` | `N1/N2` | `s0 --registrationRequest / authenticationRequest→ s1` | `s11 --registrationRequest / authenticationRequest→ s10` |
|  | 全部成员转移变体 |  |  | `s0 --registrationRequest / authenticationRequest→ s1` | `s11 --registrationRequest / authenticationRequest→ s10` |

- 回溯关系：`B02 --RR→ B05`。
- 全部成员状态交叉对在模型内行为等价：`False`。
  - `s0/s11`：最短区分序列 `registrationRequest authenticationResponse securityModeComplete` → `s3:registrationAccept/s9:null_action`。

#### `B03`：父类 `D2` 的 `D3/D4`（独立入口）

- 子类 `D3` = {s7}
  - signature：`(N2, NG1, X, D1, D2, X, X, X, X, X, D2, X)`
- 子类 `D4` = {s15}
  - signature：`(N1, NG1, X, D1, D2, X, X, X, X, X, D2, X)`

| 位置 | 输入 | 简写 | 上游类别对 | `D3` 代表转移 | `D4` 代表转移 |
|---:|---|---|---|---|---|
| 1 | `registrationRequest` | `RR` | `N1/N2` | `s7 --registrationRequest / authenticationRequest→ s8` | `s15 --registrationRequest / authenticationRequest→ s1` |
|  | 全部成员转移变体 |  |  | `s7 --registrationRequest / authenticationRequest→ s8` | `s15 --registrationRequest / authenticationRequest→ s1` |

- 回溯关系：`B03 --RR→ B05`。
- 全部成员状态交叉对在模型内行为等价：`False`。
  - `s7/s15`：最短区分序列 `registrationRequest authenticationResponse securityModeComplete` → `s9:null_action/s3:registrationAccept`。

### 第 2 轮

#### `B04`：父类 `A1` 的 `A1/A2`（独立入口）

- 子类 `A1` = {s3}
  - signature：`(N1, N1, A1, D1, A1, D2, A1, A1, A1, S1, A1, A1)`
- 子类 `A2` = {s14}
  - signature：`(N1, N1, A1, D3, A1, D2, A1, A1, A1, S1, A1, A1)`

| 位置 | 输入 | 简写 | 上游类别对 | `A1` 代表转移 | `A2` 代表转移 |
|---:|---|---|---|---|---|
| 4 | `deregistrationRequest` | `DR` | `D1/D3` | `s3 --deregistrationRequest / null_action→ s0` | `s14 --deregistrationRequest / null_action→ s12` |
|  | 全部成员转移变体 |  |  | `s3 --deregistrationRequest / null_action→ s0` | `s14 --deregistrationRequest / null_action→ s12` |

- 回溯关系：`B04 --DR→ B08`。
- 全部成员状态交叉对在模型内行为等价：`False`。
  - `s3/s14`：最短区分序列 `deregistrationRequest registrationRequestGUTI` → `s0:identityRequest/s12:authenticationRequest`。

#### `B05`：父类 `N1` 的 `N1/N2`（已由较晚路径覆盖）

- 子类 `N1` = {s1}
  - signature：`(N1, N1, N1, D1, N1, N1, A1, D1, N1, N1, N1, N1)`
- 子类 `N2` = {s8, s10}
  - signature：`(N1, N1, N1, D1, N1, N1, A2, D1, N1, N1, N1, N1)`

| 位置 | 输入 | 简写 | 上游类别对 | `N1` 代表转移 | `N2` 代表转移 |
|---:|---|---|---|---|---|
| 7 | `authenticationResponse` | `AR` | `A1/A2` | `s1 --authenticationResponse / securityModeCommand→ s3` | `s8 --authenticationResponse / securityModeCommand→ s9` |
|  | 全部成员转移变体 |  |  | `s1 --authenticationResponse / securityModeCommand→ s3` | `s8 --authenticationResponse / securityModeCommand→ s9；s10 --authenticationResponse / securityModeCommand→ s9` |

- 回溯关系：`B05 --AR→ B07`。
- 全部成员状态交叉对在模型内行为等价：`False`。
  - `s1/s8`：最短区分序列 `authenticationResponse securityModeComplete` → `s3:registrationAccept/s9:null_action`。
  - `s1/s10`：最短区分序列 `authenticationResponse securityModeComplete` → `s3:registrationAccept/s9:null_action`。

#### `B06`：父类 `N1` 的 `N1/N3`（独立入口）

- 子类 `N1` = {s1}
  - signature：`(N1, N1, N1, D1, N1, N1, A1, D1, N1, N1, N1, N1)`
- 子类 `N3` = {s13}
  - signature：`(N1, N1, N1, D3, N1, N1, A1, D1, N1, N1, N1, N1)`

| 位置 | 输入 | 简写 | 上游类别对 | `N1` 代表转移 | `N3` 代表转移 |
|---:|---|---|---|---|---|
| 4 | `deregistrationRequest` | `DR` | `D1/D3` | `s1 --deregistrationRequest / null_action→ s0` | `s13 --deregistrationRequest / null_action→ s12` |
|  | 全部成员转移变体 |  |  | `s1 --deregistrationRequest / null_action→ s0` | `s13 --deregistrationRequest / null_action→ s12` |

- 回溯关系：`B06 --DR→ B08`。
- 全部成员状态交叉对在模型内行为等价：`False`。
  - `s1/s13`：最短区分序列 `deregistrationRequest registrationRequestGUTI` → `s0:identityRequest/s12:authenticationRequest`。

### 第 1 轮

#### `B07`：父类 `A` 的 `A1/A2`（已由较晚路径覆盖）

- 子类 `A1` = {s3, s14}
  - signature：`(N, N, A, D, A, D, A, A, A, S, A, A)`
- 子类 `A2` = {s9}
  - signature：`(N, N, A, D, A, D, A, A, A, A, A, A)`

| 位置 | 输入 | 简写 | 上游类别对 | `A1` 代表转移 | `A2` 代表转移 |
|---:|---|---|---|---|---|
| 10 | `securityModeComplete` | `SMC` | `A/S` | `s3 --securityModeComplete / registrationAccept→ s4` | `s9 --securityModeComplete / null_action→ s9` |
|  | 全部成员转移变体 |  |  | `s3 --securityModeComplete / registrationAccept→ s4；s14 --securityModeComplete / registrationAccept→ s4` | `s9 --securityModeComplete / null_action→ s9` |

- 回溯关系：`B07 --SMC→ K1`。
- 全部成员状态交叉对在模型内行为等价：`False`。
  - `s3/s9`：最短区分序列 `securityModeComplete` → `s3:registrationAccept/s9:null_action`。
  - `s14/s9`：最短区分序列 `securityModeComplete` → `s14:registrationAccept/s9:null_action`。

#### `B08`：父类 `D` 的 `D1/D3`（已由较晚路径覆盖）

- 子类 `D1` = {s0, s11}
  - signature：`(N, NG, D, D, D, X, X, X, X, D, D, D)`
- 子类 `D3` = {s12}
  - signature：`(N, N, D, D, D, X, X, X, X, D, D, D)`

| 位置 | 输入 | 简写 | 上游类别对 | `D1` 代表转移 | `D3` 代表转移 |
|---:|---|---|---|---|---|
| 2 | `registrationRequestGUTI` | `RRG` | `N/NG` | `s0 --registrationRequestGUTI / identityRequest→ s6` | `s12 --registrationRequestGUTI / authenticationRequest→ s13` |
|  | 全部成员转移变体 |  |  | `s0 --registrationRequestGUTI / identityRequest→ s6；s11 --registrationRequestGUTI / identityRequest→ s16` | `s12 --registrationRequestGUTI / authenticationRequest→ s13` |

- 回溯关系：`B08 --RRG→ K2`。
- 全部成员状态交叉对在模型内行为等价：`False`。
  - `s0/s12`：最短区分序列 `registrationRequestGUTI` → `s0:identityRequest/s12:authenticationRequest`。
  - `s11/s12`：最短区分序列 `registrationRequestGUTI` → `s11:identityRequest/s12:authenticationRequest`。

## 5. 初始关键分裂与可观察输出

### `K1`：初始类别 `A/S`

- 来源 `B07` 的 `A1/A2`，输入 `securityModeComplete`（`SMC`）：
  - 状态 `s3`：`s3 --securityModeComplete / registrationAccept→ s4`
  - 状态 `s9`：`s9 --securityModeComplete / null_action→ s9`
  - 直接可观察：`s3:registrationAccept/s9:null_action`。
  - 状态 `s14`：`s14 --securityModeComplete / registrationAccept→ s4`
  - 状态 `s9`：`s9 --securityModeComplete / null_action→ s9`
  - 直接可观察：`s14:registrationAccept/s9:null_action`。

### `K2`：初始类别 `N/NG`

- 来源 `B08` 的 `D1/D3`，输入 `registrationRequestGUTI`（`RRG`）：
  - 状态 `s0`：`s0 --registrationRequestGUTI / identityRequest→ s6`
  - 状态 `s12`：`s12 --registrationRequestGUTI / authenticationRequest→ s13`
  - 直接可观察：`s0:identityRequest/s12:authenticationRequest`。
  - 状态 `s11`：`s11 --registrationRequestGUTI / identityRequest→ s16`
  - 状态 `s12`：`s12 --registrationRequestGUTI / authenticationRequest→ s13`
  - 直接可观察：`s11:identityRequest/s12:authenticationRequest`。

## 6. 不符合严格入口条件的 4 个对子

| 轮次 | 父类 | 子类对 | 分类 | 差异数 | 排除原因 | 差异输入与目标类别 | 是否作为中间节点 |
|---:|---|---|---|---:|---|---|---|
| 3 | `N2` | `N2/N3` | `convergent_unique` | 2 | 2 个 signature 位置不同；虽汇聚到同一上游类别对，但不满足 strict 的单位置条件 | `RR(N1→N2)；RRG(N1→N2)` | 否 |
| 2 | `N1` | `N2/N3` | `branching` | 2 | 2 个 signature 位置不同，且指向 2 个不同上游类别对 | `DR(D1→D3)；AR(A1→A2)` | 否 |
| 1 | `D` | `D1/D2` | `convergent_unique` | 3 | 3 个 signature 位置不同；虽汇聚到同一上游类别对，但不满足 strict 的单位置条件 | `RC(D→X)；SMC(D→X)；CUC(D→X)` | 否 |
| 1 | `D` | `D2/D3` | `branching` | 4 | 4 个 signature 位置不同，且指向 2 个不同上游类别对 | `RRG(N→NG)；RC(D→X)；SMC(D→X)；CUC(D→X)` | 否 |

## 7. 一致性结论

- 最后有效细化轮：第 4 轮；第 5 轮确认收敛。
- 所有轮间关系严格指向上一轮，无环；公共尾链仅展开一次。
- 所有代表转移和成员变体均来自原始 DOT；初始终点均落实到直接或最短后缀可观察输出差异。

## 8. 全量回溯流程图

![hypothesis_13 全量二分类回溯流程图](hypothesis_13_all_binary_backtrace_flowchart.svg)

可编辑 DOT：`hypothesis_13_all_binary_backtrace_flowchart.dot`；PDF：`hypothesis_13_all_binary_backtrace_flowchart.pdf`；审计 JSON：`hypothesis_13_all_binary_backtrace.json`。
