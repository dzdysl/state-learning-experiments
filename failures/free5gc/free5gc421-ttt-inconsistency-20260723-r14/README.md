# Free5GC 4.2.1 TTT query inconsistency

状态：原因机制已定向复现；修复验证尚未进行。

该失败在 2026-07-23 05:45--05:46（北京时间）出现。TTT 在开始第 14 轮细化后，因同一查询的三次运行产生不一致输出而停止。直接失败信息、精确输入和每次运行的输出位于 `inputs/` 与 `evidence/`。

## 结果摘要

- 核心网运行日志标明 Free5GC v4.2.1。
- `deregistrationAccept` 的输出在不同运行间为 `authenticationRequest` 或 `null_action`。
- 学习器以 `FATAL QUERY INCONSISTENCY` 停止，并抛出 `IllegalStateException: Could not refine hypothesis`。
- `hypothesis_13` 对失败输入的后三步均预测 `null_action`，三次实测均不完全匹配该模型。
- 50 条序列的 baseline 与 multiSeq-MDF 回归在 470 个抽象步骤上完全一致，且均未再次触发 TTT inconsistency；这只能说明该轮回归行为等价，不能证明 MDF 已修复原问题。
- 2026-07-24 的定向复现证明三组旧 T3560 认证重传可以交错出现，并被后续 `deregistrationRequest` 读到。结合对应版本源码，当前最强解释是 Free5GC 并行旧 timer、UERANSIM 单槽 replay 判定和无步骤关联的 socket FIFO 共同导致下行错位。

## 结果导航

- 原始失败输入：`inputs/failure-query.txt`
- 原始失败证据：`evidence/`
- 模型与失败序列对照：`analysis/README.md`
- 50 条序列回归：`followups/baseline-vs-multiseq-mdf-20260723/`
- T3560 定向复现与根因报告：`followups/t3560-retransmission-reproduction-20260724/`
- 外部或历史大日志：`artifacts.yaml`

## 证据边界

原始失败所用 learner commit 仍未知，因此不能声称已经用完全相同的历史二进制重放出原始崩溃。定向复现使用的 JAR 标记为 dirty，且没有冻结 UERANSIM 二进制哈希；所以“多 timer 与旧认证请求错位”已有直接运行证据，“该机制完整解释原始第 14 轮失败”仍按高置信结论管理。

下一步应分别验证三个修复点：Free5GC 启动新认证前取消旧 T3560；UERANSIM 使用事务化或多项 replay 历史；学习器输出携带步骤关联或在步骤边界隔离异步旧消息。
