# hypothesis_23 迭代细化报告

## 1. 数据与 signature

- DOT：`D:\state-learning-lab\projects\state-learning-experiments\experiments\open5gs\baseline-mdf-servreq\2026-07-17\evidence\hypotheses\hypothesis_23.dot`
- SHA-256：`e0ff218edf17bcbc8af9cf5a4a07d530c3ed62ad0ffab45e150e9d475109d6e2`
- 状态数：28
- 输入顺序：`registrationRequest`, `registrationRequestGUTI`, `registrationComplete`, `deregistrationRequest`, `serviceRequest`, `securityModeReject`, `authenticationResponse`, `authenticationFailure`, `deregistrationAccept`, `securityModeComplete`, `identityResponse`, `configurationUpdateComplete`
- signature：依次把每条输入转移的目标状态替换为上一轮类别标签；输出动作不进入 signature。

## 2. 初始分类

- `A` = {s3, s9, s12, s20, s22, s27}
- `D` = {s0, s10, s13, s15, s16, s23, s26}
- `N` = {s1, s4, s7, s11, s14, s19, s21, s24, s25}
- `NG` = {s8, s17, s18}
- `S` = {s5}
- `R` = {s6}
- `X` = {s2}

## 3. 逐轮细化

### 第 1 轮

- 类别数：16
- 拆分父类数：4

#### 父类 `A`：{s3, s9, s12, s20, s22, s27}

- `A1` = {s3, s20}
  - signature：`(N, N, A, D, A, D, A, A, A, S, A, A)`
  - 与基准 signature 相同。
- `A2` = {s9}
  - signature：`(A, N, A, D, D, D, A, A, A, S, A, A)`
  - 与基准差异：registrationRequest：N→A；serviceRequest：A→D
- `A3` = {s12, s27}
  - signature：`(N, N, A, D, A, D, A, A, A, A, A, A)`
  - 与基准差异：securityModeComplete：S→A
- `A4` = {s22}
  - signature：`(N, N, A, D, A, D, A, A, A, X, A, A)`
  - 与基准差异：securityModeComplete：S→X

#### 父类 `D`：{s0, s10, s13, s15, s16, s23, s26}

- `D1` = {s0, s15, s26}
  - signature：`(N, NG, D, D, D, X, X, X, X, D, N, D)`
  - 与基准 signature 相同。
- `D2` = {s10, s23}
  - signature：`(N, NG, X, D, D, X, X, X, X, X, N, X)`
  - 与基准差异：registrationComplete：D→X；securityModeComplete：D→X；configurationUpdateComplete：D→X
- `D3` = {s13}
  - signature：`(D, NG, X, D, D, X, X, X, X, X, D, X)`
  - 与基准差异：registrationRequest：N→D；registrationComplete：D→X；securityModeComplete：D→X；identityResponse：N→D；configurationUpdateComplete：D→X
- `D4` = {s16}
  - signature：`(N, N, D, D, D, X, X, X, X, D, N, D)`
  - 与基准差异：registrationRequestGUTI：NG→N

#### 父类 `N`：{s1, s4, s7, s11, s14, s19, s21, s24, s25}

- `N1` = {s1, s11, s14, s19, s21, s24}
  - signature：`(N, N, N, D, N, N, A, D, N, N, N, N)`
  - 与基准 signature 相同。
- `N2` = {s4, s25}
  - signature：`(N, N, N, D, N, N, D, D, N, N, N, N)`
  - 与基准差异：authenticationResponse：A→D
- `N3` = {s7}
  - signature：`(N, N, N, D, D, N, A, D, N, N, N, N)`
  - 与基准差异：serviceRequest：N→D

#### 父类 `NG`：{s8, s17, s18}

- `NG1` = {s8, s18}
  - signature：`(N, NG, NG, D, NG, NG, NG, NG, NG, NG, N, NG)`
  - 与基准 signature 相同。
- `NG2` = {s17}
  - signature：`(NG, NG, NG, D, D, NG, NG, NG, NG, NG, NG, NG)`
  - 与基准差异：registrationRequest：N→NG；serviceRequest：NG→D；identityResponse：N→NG

### 第 2 轮

- 类别数：20
- 拆分父类数：2

#### 父类 `A1`：{s3, s20}

- `A1` = {s3}
  - signature：`(N1, N1, A1, D1, A1, D2, A1, A1, A1, S1, A1, A1)`
  - 与基准 signature 相同。
- `A2` = {s20}
  - signature：`(N1, N1, A1, D4, A1, D2, A1, A1, A1, S1, A1, A1)`
  - 与基准差异：deregistrationRequest：D1→D4

#### 父类 `N1`：{s1, s11, s14, s19, s21, s24}

- `N1` = {s1, s24}
  - signature：`(N1, N1, N1, D1, N1, N1, A1, D1, N1, N1, N1, N1)`
  - 与基准 signature 相同。
- `N2` = {s11, s14}
  - signature：`(N1, N1, N1, D1, N1, N1, A3, D1, N1, N1, N1, N1)`
  - 与基准差异：authenticationResponse：A1→A3
- `N3` = {s19}
  - signature：`(N1, N1, N1, D4, N1, N1, A1, D1, N1, N1, N1, N1)`
  - 与基准差异：deregistrationRequest：D1→D4
- `N4` = {s21}
  - signature：`(N1, N1, N1, D4, N1, N1, A4, D1, N1, N1, N1, N1)`
  - 与基准差异：deregistrationRequest：D1→D4；authenticationResponse：A1→A4

### 第 3 轮

- 类别数：25
- 拆分父类数：5

#### 父类 `D1`：{s0, s15, s26}

- `D1` = {s0, s26}
  - signature：`(N1, NG1, D1, D1, D1, X, X, X, X, D1, N5, D1)`
  - 与基准 signature 相同。
- `D2` = {s15}
  - signature：`(N2, NG1, D1, D1, D1, X, X, X, X, D1, N5, D1)`
  - 与基准差异：registrationRequest：N1→N2

#### 父类 `D2`：{s10, s23}

- `D3` = {s10}
  - signature：`(N2, NG1, X, D1, D2, X, X, X, X, X, N5, X)`
  - 与基准 signature 相同。
- `D4` = {s23}
  - signature：`(N1, NG1, X, D1, D2, X, X, X, X, X, N5, X)`
  - 与基准差异：registrationRequest：N2→N1

#### 父类 `N1`：{s1, s24}

- `N1` = {s1}
  - signature：`(N1, N1, N1, D1, N1, N1, A1, D1, N1, N1, N1, N1)`
  - 与基准 signature 相同。
- `N2` = {s24}
  - signature：`(N2, N1, N1, D1, N1, N1, A1, D1, N1, N1, N1, N1)`
  - 与基准差异：registrationRequest：N1→N2

#### 父类 `N2`：{s11, s14}

- `N3` = {s11}
  - signature：`(N1, N1, N2, D1, N2, N2, A4, D1, N2, N2, N2, N2)`
  - 与基准 signature 相同。
- `N4` = {s14}
  - signature：`(N2, N2, N2, D1, N2, N2, A4, D1, N2, N2, N2, N2)`
  - 与基准差异：registrationRequest：N1→N2；registrationRequestGUTI：N1→N2

#### 父类 `N5`：{s4, s25}

- `N7` = {s4}
  - signature：`(N1, N1, N5, D1, N5, N5, D1, D1, N5, N5, N5, N5)`
  - 与基准 signature 相同。
- `N8` = {s25}
  - signature：`(N2, N1, N5, D1, N5, N5, D1, D1, N5, N5, N5, N5)`
  - 与基准差异：registrationRequest：N1→N2

### 第 4 轮

- 类别数：27
- 拆分父类数：2

#### 父类 `A4`：{s12, s27}

- `A4` = {s12}
  - signature：`(N4, N4, A4, D2, A4, D3, A4, A4, A4, A4, A4, A4)`
  - 与基准 signature 相同。
- `A5` = {s27}
  - signature：`(N4, N4, A4, D2, A4, D4, A4, A4, A4, A4, A4, A4)`
  - 与基准差异：securityModeReject：D3→D4

#### 父类 `NG1`：{s8, s18}

- `NG1` = {s8}
  - signature：`(N1, NG1, NG1, D1, NG1, NG1, NG1, NG1, NG1, NG1, N1, NG1)`
  - 与基准 signature 相同。
- `NG2` = {s18}
  - signature：`(N1, NG1, NG1, D2, NG1, NG1, NG1, NG1, NG1, NG1, N1, NG1)`
  - 与基准差异：deregistrationRequest：D1→D2

### 第 5 轮

本轮没有产生新的状态集合划分，判定收敛。

## 4. 最终分类

- `A1` = {s3}
- `A2` = {s20}
- `A3` = {s9}
- `A4` = {s12}
- `A5` = {s27}
- `A6` = {s22}
- `D1` = {s0, s26}
- `D2` = {s15}
- `D3` = {s10}
- `D4` = {s23}
- `D5` = {s13}
- `D6` = {s16}
- `N1` = {s1}
- `N2` = {s24}
- `N3` = {s11}
- `N4` = {s14}
- `N5` = {s19}
- `N6` = {s21}
- `N7` = {s4}
- `N8` = {s25}
- `N9` = {s7}
- `NG1` = {s8}
- `NG2` = {s18}
- `NG3` = {s17}
- `S1` = {s5}
- `R1` = {s6}
- `X` = {s2}

## 5. 汇总

- 最后有效细化轮：第 4 轮。
- 收敛确认轮：第 5 轮。
- 最终类别数：27。
