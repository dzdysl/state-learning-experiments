# 观察

## 输入与完整轨迹

- `inputs/hypothesis_14_base_cycle_cover_repeat10.seq` 有 37 行、35,826 bytes，
  SHA-256 为 `4b1f05b6e4a9030de0c0b0170124dc8ac635e03de7f872b6f4f3d1c987df9e2a`。
  它与 H14 父 follow-up 的基础输入同哈希；运行器 console 第 5 行也指向同名文件。
- `evidence/statelearner_trace.jsonl` 有 1,656 个有效 JSONL 记录，按
  `session_id` 分为 37 组。每组的完整 `abstract_io.input` 字唯一匹配输入文件的
  一行，覆盖行号 1–37，无重号、缺号或前缀匹配歧义。
- 对 H14 原始 DOT（SHA-256
  `19fe51296d78d000d2ab7e9106d4555b0064ca7f5e7b78c608c39f05db5ca8d0`）排除
  `s2` 后，从 `s0` 按完整 trace 的每个输入模拟。37 组、1,656 步均找到确定性
  转移，且每步 `abstract_io.output` 与 DOT 输出相同；详见
  [h14-base-trace-model-check.json](derived/h14-base-trace-model-check.json)。

## `cleaned` 文件的边界

- console 第 9674–9676 行分别记录完整 trace、cleaned trace 及其 498 行数。
  完整 trace 为 1,656 行，因而 cleaned 文件省略了 1,158 个完整轨迹观察。
- 本记录只把完整 `statelearner_trace.jsonl` 作为直接 I/O 证据。cleaned 文件留在
  外部快照供审计，但不是覆盖、完成度、模型一致性或寄存器推断的输入。

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
