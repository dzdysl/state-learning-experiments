# 观察

## 输入与完整轨迹

- `inputs/base-cycle-repeat10.seq` 有 37 行、35,826 bytes，
  SHA-256 为 `4b1f05b6e4a9030de0c0b0170124dc8ac635e03de7f872b6f4f3d1c987df9e2a`。
  它与 H14 父 follow-up 的基础输入同哈希；运行器 console 第 5 行也指向同名文件。
- `evidence/statelearner_trace.jsonl` 有 1,656 个有效 JSONL 记录，按
  `session_id` 分为 37 组。每组的完整 `abstract_io.input` 字唯一匹配输入文件的
  一行，覆盖行号 1–37，无重号、缺号或前缀匹配歧义。
- 对 H14 原始 DOT（SHA-256
  `19fe51296d78d000d2ab7e9106d4555b0064ca7f5e7b78c608c39f05db5ca8d0`）排除
  `s2` 后，从 `s0` 按完整 trace 的每个输入模拟。37 组、1,656 步均找到确定性
  转移，且每步 `abstract_io.output` 与 DOT 输出相同；详见
  [model-check.json](model-check.json)。

## `cleaned` 文件的边界

- console 第 9674–9676 行分别记录完整 trace、cleaned trace 及其 498 行数。
  完整 trace 为 1,656 行，因而 cleaned 文件省略了 1,158 个完整轨迹观察。
- 本记录只把完整 `statelearner_trace.jsonl` 作为直接 I/O 证据。cleaned 文件留在
  外部快照供审计，但不是覆盖、完成度、模型一致性或寄存器推断的输入。

## 相对稳定推断迁移检验

- 假设性边先按 `cycle_id` 构建局部最简候选并求精确树交集；仅当多个循环分区的交集
  为空时，才触发 `combined_sample_fit`。局部优先候选交集为空但联合样本仍存在精确公式，
  与联合样本本身无法由同一公式覆盖，是两个不同结果。
- `{isInitMsg=0}`、`registrationRequest/authenticationRequest` 的 8 条相对稳定具体边
  共有 72 个单边区域样本；相对稳定推断的已观察 `s=0` 分支为
  `r'=ite(r<6,r+1,0)`，`s=1` 分支为未观察。
- `{isInitMsg=0}`、`registrationRequestGUTI/authenticationRequest` 的 8 条相对稳定具体边
  也有 72 个单边区域样本；相对稳定推断的已观察 `s=0` 分支为
  `r'=ite(r_i=7,ite(r<6,r+1,0),ite(r_i<6,r_i+1,0))`，`s=1` 分支为未观察。
- `authenticationResponse/securityModeCommand` 的 4 条相对稳定具体边共有 312 个单边
  区域样本；该 I/O 不适用已配置信号，相对稳定推断为 `r'=r`；当前 52 条边结果中没有同一 I/O 的
  假设性目标，故记录 `target_partition_count=0`。
- 第三个相对稳定推断生成后，其中的 GUTI 模型树含
  `derived_value_guard: r_i[ngksi_uplink] == 7`。工具据此动态记录后续派生值分裂偏好 `T=7`；
  来源边为 `E0014/E0038/E0098/E0110/E0122/E0158/E0170/E0206`。该偏好没有来自配置，
  不影响已生成候选；后续若精确常数 `r'=7` 覆盖全部样本，则排在最高优先级，含同一
  `r_i==7` 分裂的候选其次，其他精确候选继续保留。
- 共 22 个假设性循环—边分区：4 个迁移成功、17 个没有相同 `{s}/input/output` 的相对稳定推断、
  1 个迁移失败。4 个成功分区为 `E0050/F002`、`E0050/S009`、`E0073/S036`、`E0193/S037`。
  17 个无匹配分区均为 `{isInitMsg=1}`，而相同 I/O 的相对稳定推断只绑定 `s=0`；
  因此不借用其他信号分支，也不执行反推。
- `E0073: s6 → s1, registrationRequest/authenticationRequest` 在 `S008` 输入行 10
  （`V01`）的 R3 首例为终止信号 `isInitMsg=0`、终止上行 `r_i=7`、前一下行
  `r_before=0`，映射观察区域为
  `(0,{isInitMsg=0},[ngksi_uplink=7],0)`，相对稳定推断预测 `r_after=1`、
  当前下行观察为 0，8/8 个样本不匹配；同一
  具体边在 `S036` 输入行 21/23（`V01/V03`）的终止信号同为 0、终止上行同为 7，
  16 个样本全部满足参考公式。
- S008 的三边区域为
  `E0016: s1→s0, deregistrationRequest/null_action`、
  `E0002: s0→s6, registrationRequestGUTI/identityRequest`、
  `E0073: s6→s1, registrationRequest/authenticationRequest`。反推保持更早的 E0016 为
  `r'=r`，只归因最近无 KSI 下行的 E0002。当前 52 条边匹配轨迹的上下行 KSI 并集为
  `{0,1,2,3,4,5,6,7}`。
- E0002 在该分区的完整输入为 `isInitMsg=1`、
  `registrationRequestGUTI KSI=7`、进入边前 `r=0`。8 个样本的允许输出均为 `{6,7}`，
  得到六个精确简单候选：`r'=7`、`r'=6`、`r'=r+6`、`r'=r+7`、`r'=r_i-1`、
  `r'=r_i`。动态 `T=7` 使精确常数赋值 `r'=7` 排在首位；其他精确候选全部保留。
- 反推结果仍是 `hypothetical_candidate`，依赖相对稳定推断迁移、E0016 保持、最近前序边归因和
  全局观测值域假设。它不能确认 E0002 或 E0073 的真实实现更新。

## 运行时异常

- console 第 9677–9687 行报告 `Primary total: 37`、`Primary kept: 37`、
  `Failed primary ids: []` 和 `Final kept sequences: 37`。所以 37 条逻辑序列均
  已运行并产生完整 trace。
- `run-manifest.json` 列出 80 个 `core-session`，均为 `failed` 且退出码为 134。
  其中 43 个会话日志包含 `socket bind(2) ... Address already in use`；剩余 37 个
  会话对应完整 I/O trace。console 共记录 80 次 `scripts/start_core.sh` 运行时哈希
  不匹配和 80 次 launcher status 134。
- 完整 I/O trace 没有 H14 输出不一致；因此上述 134 与端口占用是运行环境/收尾
  事实，而不是本次已观察输入上的模型反例。

## 参考源码定位

- 运行包没有冻结实际 Open5GS 二进制版本或哈希。`open5gs_old`（v2.6.6）只能作为
  参考快照：`src/amf/nas-path.c:486–491` 在 NG context 已移除时返回
  `OGS_NOTFOUND`，而 `src/amf/gmm-sm.c:1527–1530` 在认证计时器重传发送后期望
  `OGS_OK`。这与部分会话中先出现 `NG context has already been removed`、再出现
  `gmm_state_authentication: Expectation 'r == OGS_OK' failed` 的日志顺序相符。
