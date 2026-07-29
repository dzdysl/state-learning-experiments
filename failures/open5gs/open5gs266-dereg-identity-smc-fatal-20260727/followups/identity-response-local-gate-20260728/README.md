# Follow-up：Identity Response 被 StateLearner 本地阻止

## 状态

已整理。运行 `20260728T083613Z-23b95c25` 的 8 个原始文件共 49,712 bytes，已按原始相对目录完整复制到 `raw/20260728T083613Z-23b95c25/`。文件大小与 SHA-256 见 `archive-summary.json`。

本次 `identityResponse -> null_action` 不是 AMF 收到 Identity Response 后无响应，也不是 AMF sink/crash。该输入在实验版 StateLearner 内部被拦截，没有生成或发送上行 NAS PDU。

## 实际序列

输入：

`registrationRequest authenticationResponse securityModeComplete registrationRequestGUTI authenticationResponse securityModeReject registrationRequestGUTI identityResponse`

输出：

`authenticationRequest securityModeCommand registrationAccept authenticationRequest securityModeCommand null_action identityRequest null_action`

精确输入另存于 `inputs/observed-sequence.json`。

## 证据链

1. `statelearner_trace.jsonl:8` 将最后一步标为 `identityResponse -> null_action`，同时记录 `ue_side.concrete_hex: ""`、空 UE 字段以及 `note: "local_null_action"`。这证明该步没有上行 NAS Identity Response。
2. `raw/console.log:58-61` 显示执行器要求发送 `identityResponse`，随后直接从 RRC/SUL 收到本地 `null_action`。
3. Open5GS `core.log:219-226` 显示第二个 `registrationRequestGUTI` 使用的 5G-S-TMSI 已无法匹配 AMF-UE；AMF 新建上下文并返回 Identity Request。
4. `core.log:227` 在 15 秒后记录 `Retransmission of Identity-Request failed. Stop retransmission`，期间没有 `Identity response` 日志；随后 AMF 正常释放该 UE 上下文。该日志不支持 AMF crash。

## 源码解释

本次实验固定使用 learner/UERANSIM commit `11453a35e1c1dba5b49ae2d487f9e48ff0b493a8`。

- 首次 Registration Accept 由 `src/ue/nas/mm/register.cpp:353-357` 把 UE 切到 `MM_REGISTERED_NORMAL_SERVICE`。
- `registrationRequestGUTI`、`authenticationResponse` 和 `securityModeReject` 的 StateLearner 分支只调用 `sendNasMessage()`，没有把本地 MM 状态切换到 `MM_REGISTERED_INITIATED` 或其他非 registered-normal 状态，见 `src/ue/app/state_learner.cpp:925-929,1084-1098`。
- 收到 Identity Request 后，`src/ue/nas/mm/identity.cpp:21-65` 构造并保存 Identity Response，但自动发送语句已被注释，也没有切换 MM 状态。
- 最后，`src/ue/app/state_learner.cpp:1144-1156` 仅在 `m_mmSubState != MM_REGISTERED_NORMAL_SERVICE` 时发送已保存的 Identity Response；若仍为 `MM_REGISTERED_NORMAL_SERVICE`，则直接调用 `notify_response("null_action")`。

因此状态链是：

`Registration Accept -> MM_REGISTERED_NORMAL_SERVICE -> 后续抽象符号未改变该状态 -> Identity Request 只保存响应 -> identityResponse 符号命中本地 registered-normal 限制 -> local_null_action`

## 与父故障的关系

父记录证明 Open5GS v2.6.6 在特定上下文中确实能够接收 Identity Response，并可能由它异常启动 AKA。本 follow-up 不否定该结论；它说明另一条 StateLearner 符号路径根本没有把 Identity Response 发到 AMF。

因此，实验中出现 `identityResponse -> null_action` 时必须先区分：

- `ue_side.concrete_hex` 非空：消息已经发送，需继续检查 AMF 丢弃、超时或进程异常；
- `ue_side.concrete_hex` 为空且 `note=local_null_action`：消息被 SUL 本地阻止，不能据此推断 AMF 行为。

## 记录边界与限制

- 冻结的 `run-manifest.json` 和 `session.json` 仍标记 `status: running`，没有最终会话退出码；因此本 follow-up 不以它们证明会话正常结束。
- 本结论只解释本次最后一步为什么没有可观察的 AMF 响应。若要验证 AMF 在这条上下文链上收到 Identity Response 后的行为，需要让 SUL 先离开 `MM_REGISTERED_NORMAL_SERVICE`，或在受控测试版本中取消该本地限制，并确认 trace 中出现非空上行 NAS hex。
