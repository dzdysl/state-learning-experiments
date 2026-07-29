# 观察

## 运行证据

- `raw/statelearner_trace.jsonl` 第 3 行：首次 `securityModeComplete` 的输出为 `registrationAccept`。
- 同文件第 5 行：`deregistrationRequest` 输出为 `null_action`；该输入使用已建立的安全上下文。
- 同文件第 6、7 行：后续 `identityResponse` 得到 `authenticationRequest`，`authenticationResponse` 得到 `securityModeCommand`。
- 同文件第 8 行：最终 `securityModeComplete` 使用完整性保护的 NAS PDU，输出为 `null_action`，记录注释为 `trace_timeout_no_downlink`。
- `raw/core.log` 第 214-226 行：核心先处理 Deregistration Request，随后接收新的 InitialUEMessage 和 Identity Response，最后在 `12:43:44.271` 报出 `Invalid OGS_NAS_5GS[69]`。
- `raw/core.log` 第 227-236 行：紧接着出现 `gmm_state_security_mode: should not be reached` 和 AMF backtrace。`raw/session.json` 记录会话 `exit_code` 为 134。
- `raw/statelearner_sht_debug.jsonl` 记录 learner 发送注销后立即清除 NAS security contexts，并在 RRC release 后刷新 provided 5G-S-TMSI。

## 源码对应（Open5GS snapshot `open5gs_old`, 499c70d)

- `src/amf/context.h:221`：`amf_ue->nas.message_type` 的源码注释为 “Type of last specific NAS message received”。该字段不是 NAS 解码器中的当前消息类型，而是 AMF-UE NAS 子上下文中保存的关键入口消息类型。
- `src/amf/context.c:1481`：新建 `amf_ue` 时整个结构 `memset` 为 0，因此 `nas.message_type` 初始为 0。
- `src/amf/gmm-handler.c:780`：`gmm_handle_deregistration_request()` 将 `amf_ue->nas.message_type` 设置为 `OGS_NAS_5GS_DEREGISTRATION_REQUEST_FROM_UE`。
- `src/amf/gmm-handler.c:183,589,780`：在 v2.6.6 AMF 中，可检索到的显式写入点分别是 Registration Request、Service Request 和 Deregistration Request handler。未检索到流程成功后显式把该字段清零的路径。
- `lib/nas/5gs/message.h:75`：该常量的数值为 69。
- `src/amf/gmm-sm.c:1231-1263`：Identity Response 分支在取得 SUCI 后发起 AUSF 鉴权并迁移到 `gmm_state_authentication`，此分支没有重设 `amf_ue->nas.message_type`。
- `src/amf/gmm-sm.c:1651-1715`：Security Mode Complete 经完整性与安全上下文检查、处理函数和 UDM 注册请求后，状态机仅允许保存的消息类型为 Registration Request 或 Service Request；其他值在 1714 行触发 `ogs_fatal()`。
- `lib/core/ogs-log.h:35,105-126`：`ogs_fatal()` 本身只记 fatal 日志；紧随其后的 `ogs_assert_if_reached()` 才调用 `ogs_abort()`。相反，`ogs_expect()` 只记录 error，不会终止进程。
- `ogs_abort()` 是进程级终止，故虽然 `nas.message_type` 属于单个 `amf_ue`，其 abort 后果覆盖该 AMF 实例，而非只影响该 UE。

## InitialUEMessage / AMF-UE 上下文选择

- `src/amf/ngap-handler.c:374-390`：`ngap_handle_initial_ue_message()` 解析 `NAS_PDU`、`UserLocationInformation`、可选 `FiveG_S_TMSI` 等 NGAP IE；该入口没有限制 `NAS_PDU` 的 5GMM message type 必须为 Registration Request。
- `src/amf/ngap-handler.c:481-520`：若 NGAP 外层 `FiveG_S_TMSI` 能匹配旧 `amf_ue`，AMF 会记录该 5G-S-TMSI，并在 508 行执行 `amf_ue_associate_ran_ue(amf_ue, ran_ue)`，把新 RAN UE 关联到旧 AMF-UE 上下文。
- `src/amf/ngap-handler.c:557-574`：NGAP 入口只把 TAI/CGI 解码到 `ran_ue->saved.nr_tai` / `ran_ue->saved.nr_cgi`，然后调用 `ngap_send_to_nas(ran_ue, NGAP_ProcedureCode_id_InitialUEMessage, NAS_PDU)`，继续分发内部 NAS PDU。
- `src/amf/amf-sm.c:866-883`：`AMF_EVENT_5GMM_MESSAGE` 处理时先取 `ran_ue->amf_ue`；若为空，则调用 `amf_ue_find_by_message(&nas_message)`；仍为空时调用 `amf_ue_add(ran_ue)` 新建 AMF-UE。
- `src/amf/context.c:1722-1905`：`amf_ue_find_by_message()` 只对 Registration Request、Service Request、Deregistration Request from UE 做旧上下文查找；默认分支不处理 `OGS_NAS_5GS_IDENTITY_RESPONSE`，所以不会在这个阶段根据 Identity Response 中的 SUCI 复用旧 AMF-UE。
- `src/amf/context.c:1466-1481`：`amf_ue_add()` 分配 AMF-UE 后对整个结构 `memset(amf_ue, 0, sizeof *amf_ue)`，因此 direct Identity Response 新建上下文时，保存的 `nas.message_type` 为 0，`amf_ue->nr_tai` 也没有经过 Registration Request 的复制初始化。
- `src/amf/context.c:1908-1970`：Identity Response 后续调用 `amf_ue_set_suci()` 只是把 SUCI 绑定到当前 AMF-UE；若发现不同的旧 SUCI 上下文，会释放旧上下文并把 session list 迁移到当前上下文。这不是 `amf_ue_find_by_message()` 阶段的“按 Identity Response 复用旧 AMF-UE”路径。

## 与无显式 Registration Request 的成功后缀的区分

- `src/amf/gmm-handler.c:183` 仅在 Registration Request 处理时将保存字段设为 65；`src/amf/gmm-handler.c:780` 在 Deregistration Request 处理时将其覆写为 69。
- `src/amf/gmm-sm.c:1231-1263` 的 Identity Response 分支启动鉴权但不重设该字段；`src/amf/gmm-sm.c:1708-1715` 在收到后续 SMCompl 后依据该字段选择路径。
- 因此抽象后缀 `identityResponse authenticationResponse securityModeComplete registrationComplete` 是否成功，取决于进入该后缀前同一 AMF-UE 的保存字段。若为 65，则最终 SMCompl 走注册分支并可得到 Registration Accept；若为 69，则为本记录的 abort。该后缀自身不携带或重建该过程上下文。换言之，正常设计里 `nas.message_type` 像一个当前 procedure 的入口标记；异常 Identity Response 入口绕过了能重置/覆盖该标记的 Registration Request handler，使旧 procedure 标记残留到后续安全模式完成分支。

## 学习器 / UERANSIM-SUL 对照（learner commit `11453a35...`）

- `src/ue/app/state_learner.cpp:951-1057`：learner 发送 Deregistration Request 成功后立即清除 UE 的 current/non-current NAS security contexts，并转为去注册状态；`raw/statelearner_sht_debug.jsonl` 记录了该动作及随后 5G-S-TMSI 刷新。
- `src/ue/app/state_learner.cpp:1115-1142`：仅当外部抽象输入选择 `securityModeComplete` 时，才发送已由 UE 侧存储的 SMCompl。
- `Corelearner_seqTest_pack/mylearner/src/main/java/org/example/corelearner/devices/Open5GSSUL.java:180-198`：无下行或套接字异常会返回 `null_action`，因此该输出本身不区分正常丢弃、AMF 无响应和 AMF 崩溃；本记录以 `raw/core.log` 和 `raw/session.json` 作为 crash 结论依据。
- `src/ue/nas/mm/base.cpp:218-227`：`switchMmState()` 根据细粒度 MM state 更新粗粒度 RM state。`MM_REGISTERED_INITIATED` 被映射为 `RM_DEREGISTERED`，表示注册过程尚未完成；`MM_DEREGISTERED_INITIATED` 被映射为 `RM_REGISTERED`，表示 UE 正在注销但尚未完成注销。
- `src/ue/app/state_learner.cpp:914-949,1059-1168`：多数 StateLearner 符号路径直接调用 `mm->sendNasMessage(...)` 或本地返回 `null_action`，不调用 UERANSIM 原生注册过程启动函数，也不主动切到 `MM_REGISTERED_INITIATED_PS`。其中 `registrationRequestGUTI` 仅执行 `prepareGutiRegistration(registrationRequestGUTI)` 后发送 NAS。
- `src/ue/app/state_learner.cpp:951-1040`：`deregistrationRequest` 是少数被 StateLearner 主动补本地状态的路径；发送成功后，实验版 SUL 立即清空 current/non-current NAS security context，保存最后的 deregistration request，并切到 `MM_DEREGISTERED_INITIATED_PS`。
- 因此，完成注册后执行 `deregistrationRequest -> registrationRequest` 时，网络侧 AMF 可以收到并按 Registration Request 处理该 NAS PDU；但在 UE/SUL 内部，这不等价于已通过 UERANSIM 原生 procedure trigger 进入 `MM_REGISTERED_INITIATED`。当前实验版因直接发 NAS 和注释部分状态检查，使 `MM_REGISTERED_INITIATED`、`RM_DEREGISTERED`、`MM_DEREGISTERED_INITIATED` 等状态在黑盒观测上可能表现接近，但其细粒度语义和后续处理条件不同。
- Follow-up [`identity-response-local-gate-20260728`](../followups/identity-response-local-gate-20260728/README.md) 的运行 `20260728T083613Z-23b95c25` 中，trace 第 8 行记录 `identityResponse -> null_action`，且 `ue_side.concrete_hex` 为空、`note` 为 `local_null_action`。对应 AMF 日志在 Identity Request 后没有 Identity Response，只在 15 秒后记录重传失败。固定 learner commit 的 `src/ue/app/state_learner.cpp:1144-1156` 在 `MM_REGISTERED_NORMAL_SERVICE` 下直接返回 `null_action`；`src/ue/nas/mm/identity.cpp:21-65` 只保存 Identity Response，不自动发送或切换状态。完整证据与边界见该 follow-up。

## 注销后未出现 Authentication Failure 的源码对照

- `src/ue/app/state_learner.cpp:951-1057` 在注销成功后只清空 current/non-current NAS security context；该段未清除 USIM 的长期订阅密钥、SQN 管理器或身份资料。因此，“已注销 / 无 NAS security context”本身不是 5G-AKA Authentication Failure 的条件。
- `src/ue/nas/mm/auth.cpp:276-403`：收到包含 RAND 和 AUTN 的 5G-AKA Authentication Request 后，成功分支计算 RES*、建立 partial non-current NAS security context，并以 `state_learner->store_message(resp)` 保存 `AuthenticationResponse`，供后续抽象输入发送。
- `raw/statelearner_trace.jsonl` 第 6、7 行与上项相符：注销后的 `identityResponse` 获得新的 Authentication Request（KSI 为 1，含 RAND/AUTN），随后抽象输入 `authenticationResponse` 得到 Security Mode Command。该记录证明本次运行走到了 SUL 的 Authentication Response 保存/发送路径；它本身不能证明 AUTN 的 MAC 或 SQN 校验确实通过。
- 此实验所用 SUL 修改了失败语义：`src/ue/nas/mm/auth.cpp:282-310` 虽构造 `AuthenticationFailure`，但存入学习器消息池和发送的语句均被注释；故即使进入 `sendFailure()`，学习器也不会提供可由 `authenticationFailure` 抽象动作发送的已保存 NAS 消息。
- 同一文件 `497-537` 中，AUTN 的 MAC 不匹配只记录日志，SQN 检查结果也不参与分支；`return EAutnValidationRes::MAC_FAILURE` 与同步失败返回均被注释，函数最终无条件返回 `EAutnValidationRes::OK`。因此此 learner commit 不会因为这两类 AUTN 校验结果自动走 5G-AKA Authentication Failure 分支。
- `src/ue/nas/mm/auth.cpp:405-428` 保留了原本的设计分支：MAC 失败应使用原因 `MAC_FAILURE`，SQN 失步应使用 `SYNCH_FAILURE` 并携带 AUTS。上述 SUL 改动使这些分支在该实验配置下不可由正常 AUTN 检查到达，或不可观察为学习器输出。

结论：本运行没有 `authenticationFailure` 有两层原因。协议层面，注销清除 NAS 安全上下文不等于 USIM 对新 RAND/AUTN 必然校验失败；实验实现层面，本次固定 SUL 又抑制了 AUTN MAC/SQN 失败向抽象 `authenticationFailure` 输出的转换。故不能以这次“未观察到 Authentication Failure”反推认证向量必然有效，也不能把它当作 AMF 端异常入口已被纠正的证据。

## 注销后未出现 Authentication Reject 的源码对照

对照 Open5GS v2.6.6 核心网快照 `499c70d8be63e5f87c2d1c2bcd4e521d50ce1a6b`：

- `src/amf/gmm-sm.c:1399-1408`：AMF 在 `gmm_state_authentication` 中收到 UE 的 `Authentication Response` 后调用 `gmm_handle_authentication_response()`；只有返回非 `OGS_OK` 时才发送 `nas_5gs_send_authentication_reject()` 并进入 exception。
- `src/amf/gmm-sm.c:1468-1471`：AMF 收到 UE 的 `Authentication Failure` 且未走重同步成功路径时，才发送 Authentication Reject。
- `src/amf/gmm-sm.c:1522-1525`：认证请求重传计时器 T3560 超限时发送 Authentication Reject。
- `src/amf/gmm-sm.c:1570-1588`：AUSF authenticate 或 authenticate confirmation 处理失败时发送 Authentication Reject；若 confirmation 成功，`src/amf/gmm-sm.c:1589-1590` 转入 `gmm_state_security_mode`。
- `src/amf/nas-path.c:523-540` 与 `src/amf/gmm-build.c:388-398`：`nas_5gs_send_authentication_reject()` 构造并发送 NAS 5GS message type 88 (`OGS_NAS_5GS_AUTHENTICATION_REJECT`)。

本运行中 `raw/statelearner_trace.jsonl` 第 7 行为 `authenticationResponse -> securityModeCommand`。因此该步没有走上述 Authentication Reject 分支，而是认证确认成功后进入 Security Mode。最终异常发生在随后的 `securityModeComplete`，由 `gmm_state_security_mode` 使用残留 `amf_ue->nas.message_type = 69` 触发 fatal assertion。

## 外部资料核对

- Open5GS 官方 PR #3122（2024-04-09）说明：没有 Registration/Attach Request 时，Identity Response 不应单独把 5GMM/EMM-DEREGISTERED 推入认证；该 PR 已修改相关代码。该描述与本运行的 Identity Response 异常入口一致。
- CORECRISIS 论文表 2 报告 Open5GS P1 为 “sink state / AMF DoS”，并列出 CVE-2024-33233；论文没有给出可与本记录逐行比对的触发序列或函数名。
- 2026-07-27 对 CVE Services API 查询 `CVE-2024-33233` 返回 `404` / `CVE_RECORD_DNE`；该 ID 在本记录中不能当作已发布 CVE 或官方修复跟踪号。
- NVD 的 CVE-2024-34475 和 CVE-2024-34476 关联同一官方 PR，但描述的是不同的 assertion 位置；本运行的 fatal 函数为 `gmm_state_security_mode`。

## Follow-up 事实汇总

- [`dereg-smc-fatal-vs-direct-identity-reject-20260727`](../followups/dereg-smc-fatal-vs-direct-identity-reject-20260727/README.md) 的运行 `20260727T052451Z-3196bc78` 中，序列 1/4 重复出现 `Invalid OGS_NAS_5GS[69]`、`gmm_state_security_mode: should not be reached` 和 exit code 134。序列 7/8 则从 direct Identity Response 开始，得到 `authenticationReject`；其 AMF 日志记录 `MAC failure`。该 follow-up 的向量复算把差异定位到正确 PLMN SNN 与零 PLMN SNN 生成的 RES*/HXRES* 不一致。
- [`identity-response-local-gate-20260728`](../followups/identity-response-local-gate-20260728/README.md) 证明 `identityResponse -> null_action` 还可能由 SUL 本地门控产生。判断核心网 sink/crash 前必须先确认 trace 中存在非空的上行 NAS `concrete_hex`。
