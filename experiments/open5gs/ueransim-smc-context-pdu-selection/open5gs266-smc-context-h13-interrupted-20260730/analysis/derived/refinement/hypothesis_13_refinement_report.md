# hypothesis_13 迭代细化报告

## 1. 数据与 signature

- DOT：`D:\state-learning-lab\projects\state-learning-experiments\experiments\open5gs\ueransim-smc-context-pdu-selection\open5gs266-smc-context-h13-interrupted-20260730\evidence\hypotheses\hypothesis_13.dot`
- SHA-256：`465fc8f4e5cba3715ec8745cbc81f9d24f5876fdb37d455f858f02f01815523a`
- 状态数：17
- 输入顺序：`registrationRequest`, `registrationRequestGUTI`, `registrationComplete`, `deregistrationRequest`, `serviceRequest`, `securityModeReject`, `authenticationResponse`, `authenticationFailure`, `deregistrationAccept`, `securityModeComplete`, `identityResponse`, `configurationUpdateComplete`
- signature：依次把每条输入转移的目标状态替换为上一轮类别标签；输出动作不进入 signature。

## 2. 初始分类

- `A` = {s3, s9, s14}
- `D` = {s0, s7, s11, s12, s15}
- `N` = {s1, s8, s10, s13}
- `NG` = {s6, s16}
- `S` = {s4}
- `R` = {s5}
- `X` = {s2}

## 3. 逐轮细化

### 第 1 轮

- 类别数：10
- 拆分父类数：2

#### 父类 `A`：{s3, s9, s14}

- `A1` = {s3, s14}
  - signature：`(N, N, A, D, A, D, A, A, A, S, A, A)`
  - 与基准 signature 相同。
- `A2` = {s9}
  - signature：`(N, N, A, D, A, D, A, A, A, A, A, A)`
  - 与基准差异：securityModeComplete：S→A

#### 父类 `D`：{s0, s7, s11, s12, s15}

- `D1` = {s0, s11}
  - signature：`(N, NG, D, D, D, X, X, X, X, D, D, D)`
  - 与基准 signature 相同。
- `D2` = {s7, s15}
  - signature：`(N, NG, X, D, D, X, X, X, X, X, D, X)`
  - 与基准差异：registrationComplete：D→X；securityModeComplete：D→X；configurationUpdateComplete：D→X
- `D3` = {s12}
  - signature：`(N, N, D, D, D, X, X, X, X, D, D, D)`
  - 与基准差异：registrationRequestGUTI：NG→N

### 第 2 轮

- 类别数：13
- 拆分父类数：2

#### 父类 `A1`：{s3, s14}

- `A1` = {s3}
  - signature：`(N1, N1, A1, D1, A1, D2, A1, A1, A1, S1, A1, A1)`
  - 与基准 signature 相同。
- `A2` = {s14}
  - signature：`(N1, N1, A1, D3, A1, D2, A1, A1, A1, S1, A1, A1)`
  - 与基准差异：deregistrationRequest：D1→D3

#### 父类 `N1`：{s1, s8, s10, s13}

- `N1` = {s1}
  - signature：`(N1, N1, N1, D1, N1, N1, A1, D1, N1, N1, N1, N1)`
  - 与基准 signature 相同。
- `N2` = {s8, s10}
  - signature：`(N1, N1, N1, D1, N1, N1, A2, D1, N1, N1, N1, N1)`
  - 与基准差异：authenticationResponse：A1→A2
- `N3` = {s13}
  - signature：`(N1, N1, N1, D3, N1, N1, A1, D1, N1, N1, N1, N1)`
  - 与基准差异：deregistrationRequest：D1→D3

### 第 3 轮

- 类别数：16
- 拆分父类数：3

#### 父类 `D1`：{s0, s11}

- `D1` = {s0}
  - signature：`(N1, NG1, D1, D1, D1, X, X, X, X, D1, D1, D1)`
  - 与基准 signature 相同。
- `D2` = {s11}
  - signature：`(N2, NG1, D1, D1, D1, X, X, X, X, D1, D1, D1)`
  - 与基准差异：registrationRequest：N1→N2

#### 父类 `D2`：{s7, s15}

- `D3` = {s7}
  - signature：`(N2, NG1, X, D1, D2, X, X, X, X, X, D2, X)`
  - 与基准 signature 相同。
- `D4` = {s15}
  - signature：`(N1, NG1, X, D1, D2, X, X, X, X, X, D2, X)`
  - 与基准差异：registrationRequest：N2→N1

#### 父类 `N2`：{s8, s10}

- `N2` = {s8}
  - signature：`(N1, N1, N2, D1, N2, N2, A3, D1, N2, N2, N2, N2)`
  - 与基准 signature 相同。
- `N3` = {s10}
  - signature：`(N2, N2, N2, D1, N2, N2, A3, D1, N2, N2, N2, N2)`
  - 与基准差异：registrationRequest：N1→N2；registrationRequestGUTI：N1→N2

### 第 4 轮

- 类别数：17
- 拆分父类数：1

#### 父类 `NG1`：{s6, s16}

- `NG1` = {s6}
  - signature：`(N1, NG1, NG1, D1, NG1, NG1, NG1, NG1, NG1, NG1, N1, NG1)`
  - 与基准 signature 相同。
- `NG2` = {s16}
  - signature：`(N1, NG1, NG1, D2, NG1, NG1, NG1, NG1, NG1, NG1, N1, NG1)`
  - 与基准差异：deregistrationRequest：D1→D2

### 第 5 轮

本轮没有产生新的状态集合划分，判定收敛。

## 4. 最终分类

- `A1` = {s3}
- `A2` = {s14}
- `A3` = {s9}
- `D1` = {s0}
- `D2` = {s11}
- `D3` = {s7}
- `D4` = {s15}
- `D5` = {s12}
- `N1` = {s1}
- `N2` = {s8}
- `N3` = {s10}
- `N4` = {s13}
- `NG1` = {s6}
- `NG2` = {s16}
- `S1` = {s4}
- `R1` = {s5}
- `X` = {s2}

## 5. 汇总

- 最后有效细化轮：第 4 轮。
- 收敛确认轮：第 5 轮。
- 最终类别数：17。
