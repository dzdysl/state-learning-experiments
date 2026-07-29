# Follow-up：注销后 SMC fatal 与 direct Identity Response reject 对照

## 状态

已整理。该 follow-up 保留 `D:\downloads\runs\20260727T052451Z-3196bc78\` 的完整 24 个文件，作为父 failure 的多序列对照证据。

归档脚本 dry-run 因嵌套目录中存在重复文件名（多个 `core.log`、`session.json`、`launcher.log`）而拒绝扁平化复制；因此本记录在 `raw/20260727T052451Z-3196bc78/` 下保留原始相对目录结构。

## 这次 run 里实际有什么

- `seq 1` 和 `seq 4` 都执行了：
  `registrationRequest authenticationResponse securityModeComplete registrationComplete deregistrationRequest identityResponse authenticationResponse securityModeComplete`
- 这两次输出均为：
  `authenticationRequest securityModeCommand registrationAccept configurationUpdateCommand null_action authenticationRequest securityModeCommand null_action`
- `core-session-001` 与 `core-session-003` 均在最终 `securityModeComplete` 后出现 `Invalid OGS_NAS_5GS[69]` 与 `gmm_state_security_mode: should not be reached`，进程 exit code 为 134。
- `seq 7` 和 `seq 8` 执行的是独立的：
  `identityResponse authenticationResponse securityModeComplete registrationComplete`
- 这两次输出均为：
  `authenticationRequest authenticationReject null_action null_action`
- `core-session-005` 对应两次 `identityResponse -> authenticationRequest -> authenticationResponse` 后，AMF 日志出现 `MAC failure`，随后发送 `Authentication reject`。

## 对父记录结论的影响

这批材料修正并加强了前面的说法：不是“这个环境永远没有 Authentication Reject”。更准确地说：

- 在注册后、注销后、再使用 5G-S-TMSI/Identity Response 的序列中，AMF 对 `authenticationResponse` 返回 `securityModeCommand`，随后死在 `securityModeComplete` 的残留 `nas.message_type = 69` 分支。
- 在 reset 后直接从 `identityResponse` 开始的序列中，AMF 能走到认证失败处理，日志明确显示 `MAC failure`，并下发 `authenticationReject`。复算确认 UE/SUL 发送的 RES* 与配置 K/OPc、RAND 和正确 SNN `5G:mnc070.mcc999.3gppnetwork.org` 一致；AMF 日志中保存的 HXRES* 则与零 PLMN SNN `5G:mnc000.mcc000.3gppnetwork.org` 一致。

因此，`authenticationReject` 是否出现取决于该次 Authentication Response 是否被 AMF/AUSF 接受；父 failure 的 DoS 触发点仍是后续 Security Mode Complete，而不是认证拒绝路径。

## 两个角度

1. 注销后 SMC fatal：前置 `RegistrationRequest` 已把 `ran_ue->saved.nr_tai` 复制到 `amf_ue->nr_tai`，所以注销后的异常 Identity Response 仍使用正确 serving network name 发起 AKA，`AuthenticationResponse` 被接受并得到 `securityModeCommand`。随后 SMCompl 使用残留的 `amf_ue->nas.message_type = 69`，触发 `gmm_state_security_mode` 的 fatal 分支。
2. Direct identity reject：reset 后直接发 `identityResponse` 时，Registration Request handler 没有运行，新建 `amf_ue->nr_tai` 未从 `ran_ue->saved.nr_tai` 初始化。AMF 仍异常启动 AKA，但给 AUSF 的 serving network name 来自未初始化/零 PLMN；因此 AMF 保存的 HXRES* 与 UE 按正确 PLMN 计算出的 RES* 不一致，表现为 `MAC failure` 和 `authenticationReject`。

补充：`amf_ue->nas.message_type` 在这里应理解为 AMF 保存的 procedure 入口标记，而不是每条 NAS 消息的即时类型。它通常由 Registration/Service/Deregistration Request handler 写入，流程完成后没有显式清零；异常 Identity Response 入口能启动 AKA，却不会覆盖这个标记，因此会保留先前注销留下的 69。

上下文选择补充：`IdentityResponse` 可以作为 NGAP `InitialUEMessage` 的 NAS PDU 被 AMF 分发到 GMM。若 NGAP 外层 `FiveG_S_TMSI` 命中旧 UE，AMF 会先复用旧 `amf_ue`；若没有命中，`amf_ue_find_by_message()` 不会按 Identity Response/SUCI 查旧上下文，因为它只处理 Registration Request、Service Request 和 UE 发起的 Deregistration Request，随后会 `amf_ue_add(ran_ue)` 新建并清零 AMF-UE。这正是注销后序列与 direct identity 序列表现不同的入口条件之一。

## 下一步

- 将 `seq 1/4` 与 `seq 7/8` 的差异保留为父记录总览的一部分，避免把 `AuthenticationFailure`、`AuthenticationReject` 和 `SMCompl fatal` 混成一个现象。
- 若继续最小化，应分别冻结两个序列族：注销后 SMC fatal 族，以及直接 identity 导致 Authentication Reject 族。
