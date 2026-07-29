# Open5GS v2.6.6：注销后 Identity Response / SMCompl 导致 AMF 退出

状态：**已复现；根因已由冻结日志和 v2.6.6 源码定位；尚未在修复版本复验。**

## 范围

- 父运行：`20260727T044327Z-2de7c9aa`，UTC `04:43:27` 至 `04:44:00`；目标为 Open5GS v2.6.6 AMF。
- 输入路径：首次注册成功后执行注销，以刷新后的 5G-S-TMSI 重新接入并发送 `identityResponse`，通过鉴权后发送最后一个 `securityModeComplete`。
- 结果：最后输入得到 `null_action`；核心日志进入 `gmm_state_security_mode` 的致命分支，会话以 `exit_code: 134` 结束。
- 归档：12 个冻结原始文件、124,948 bytes，逐文件路径和 SHA-256 见 [`archive-summary.json`](archive-summary.json)。

## 已确认结论

最后一个 Security Mode Complete 不是非法消息；它在完成安全模式处理后暴露了 AMF-UE 中由先前 Deregistration Request 留下的 `nas.message_type = 69`：

1. v2.6.6 的 Deregistration Request handler 把保存字段写为 69。
2. Identity Response 可以异常启动新的 AKA，但不会重设该 procedure 入口字段。
3. Authentication Response 被接受并得到 Security Mode Command。
4. Security Mode Complete 后的分支只接受保存值为 Registration Request 或 Service Request；值 69 进入 `ogs_fatal()`，随后进程级 abort。

AMF 退出后没有下行 NAS，学习器把超时抽象为 `null_action`，因此黑盒 Mealy 结果表现为 sink。该运行证明了 AMF 实例级可用性影响，但没有测量攻击前置条件、部署级故障切换或 CVSS；本精确 fatal 分支也没有可核验的公开 CVE 编号。

## 证据与分析导航

- 精确抽象输入与输出：[`inputs/observed-sequence.json`](inputs/observed-sequence.json)
- 冻结原始运行：[`raw/`](raw/)
- 逐行运行与源码事实：[`analysis/observations.md`](analysis/observations.md)
- 尚待验证的关联和修复假设：[`analysis/hypotheses.md`](analysis/hypotheses.md)
- 核心网、学习器、UERANSIM/SUL 和工具版本：[`provenance.yaml`](provenance.yaml)

`null_action` 本身不能区分正常丢弃、SUL 本地限制、AMF 无响应或进程退出；本记录的 crash 结论依赖 `core.log`、backtrace 和 `session.json`，而不是只依赖抽象输出。

## Follow-up

- [`dereg-smc-fatal-vs-direct-identity-reject-20260727`](followups/dereg-smc-fatal-vs-direct-identity-reject-20260727/README.md)，运行 `20260727T052451Z-3196bc78`：重复确认注销后 SMC fatal；同时证明 reset 后 direct Identity Response 会因零 PLMN/SNN 产生的认证向量不一致得到 `authenticationReject`，不能与父 failure 混为一个现象。
- [`identity-response-local-gate-20260728`](followups/identity-response-local-gate-20260728/README.md)，运行 `20260728T083613Z-23b95c25`：确认另一条 `identityResponse -> null_action` 是 StateLearner 在 `MM_REGISTERED_NORMAL_SERVICE` 下本地阻止发送，不能据此推断 AMF sink。
- [`validate-authentication-failure-observability.md`](followups/validate-authentication-failure-observability.md)：记录恢复 UERANSIM/SUL AUTN 失败语义后的待验证任务。

## 下一步

1. 在 Open5GS v2.7.1 和当前受支持版本执行同一序列，验证 PR #3122 是否同时消除异常 Identity Response 入口和本 crash。
2. 分别验证保存上下文为 Registration Request（65）、Deregistration Request（69）和新建 AMF-UE（0）时的 Security Mode Complete 行为。
3. 确认 multiSeq 语法后，从 `observed-sequence.json` 生成并实测最小 `reproducer.seq`。
4. 使用未抑制 AUTN MAC/SQN 失败的 UERANSIM/SUL 验证 `authenticationFailure` 的可观察性；若准备上报，再冻结最短序列、AMF 栈和修复版本差异。
