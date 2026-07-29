# 待验证假设

## 与 CORECRISIS P1 的关系（中等置信度）

**假设：CORECRISIS 表 2 的 Open5GS P1 与本记录属于同一逻辑漏洞族，但现有公开材料不足以确认是同一条 fatal 路径。**

支持关系的线索是：两者都描述 Open5GS AMF 的 sink/DoS，且官方 PR #3122 修复了本记录同样依赖的异常 Identity Response → Authentication Request 前置转换。

仍缺少的证据是 P1 的最小序列、目标版本、AMF 栈和源码函数。论文列出的 `CVE-2024-33233` 截至 2026-07-27 在 CVE Services 中返回 `CVE_RECORD_DNE`，不能作为确认编号。只有获得可逐行比对的序列或栈，才能把两者提升为同一问题。

## 修复版本行为（待实验）

**假设：合入 PR #3122 的版本会在 Identity Response 异常入口处终止该路径，因此不会继续进入本记录的残留 `nas.message_type = 69` Security Mode Complete fatal。**

源码修复方向支持这一预期，但本仓库尚无 v2.7.1 或当前受支持版本的同序列运行证据。必须用相同输入、可比 UERANSIM/SUL 和完整核心日志复验后才能确认。
