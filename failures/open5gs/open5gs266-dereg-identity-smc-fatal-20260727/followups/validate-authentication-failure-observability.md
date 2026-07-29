# 验证 Authentication Failure 可观察性

状态：待执行。

目的：将“本次没有 `authenticationFailure`”区分为正常 AKA 成功、UERANSIM/SUL 的失败路径被修改、以及学习器抽象字母集未暴露三种因素。

1. 在 learner commit `11453a35e1c1dba5b49ae2d487f9e48ff0b493a8` 的独立工作树中，恢复 `src/ue/nas/mm/auth.cpp` 的 AUTN MAC/SQN 失败返回，并恢复 Authentication Failure 的消息保存；不要改动本 failure record 的冻结原始证据。
2. 分别构造 MAC 错误和 SQN 失步的 Authentication Request，记录 UE 日志、保存消息类型、实际发送的 NAS PDU 与抽象输出。
3. 用原始 SUL 重复同一两个输入，比较是否仍生成 Authentication Response；确认差异来自 SUL，而不是 AMF 的注销状态。
4. 将每次运行作为本 failure 的独立 follow-up，写入独立 provenance、原始输入、日志 SHA-256 和结论。
