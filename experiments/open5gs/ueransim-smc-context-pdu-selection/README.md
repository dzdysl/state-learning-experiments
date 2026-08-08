# Open5GS SMC-context PDU-selection 实验系列

本系列记录 Open5GS v2.6.6 与 UERANSIM/SUL `smc-context-pdu-selection`
版本边界下的主动学习、环覆盖和运行时验证。各次独立生命周期使用兄弟记录；README
与 provenance 明确记录父子或替代关系，不再通过深层 `followups/` 表达新关系。

## 主记录

- [H13 中断边界](open5gs266-smc-context-h13-interrupted-20260730/README.md)：
  原始运行在 H13 后继续执行但未在给定边界内完成，保留 17 状态 H13 及其分析。
- [H14 完成与收尾异常](h14-complete-teardown-20260801/README.md)：
  同一主动学习运行最终导出 18 状态 H14；后续 exit 134 归类为收尾异常。
- [H14 基础环运行时验证](h14-base-runtime-20260804/README.md)：
  37 条基础组逻辑序列与 H14 完全一致，并给出具体 DOT 边上的 ngKSI 观察寄存器候选。

## 旧结构记录

以下既有 H13/C14 材料本次不迁移；它们继续保留原始证据边界，但不得在旧深层目录中
新增文件：

- [H13 repeat-10 寄存器分析](open5gs266-smc-context-h13-interrupted-20260730/followups/cycle-cover-repeat10-register-analysis-20260731/README.md)
- [C14 ngKSI 同步验证](open5gs266-smc-context-h13-interrupted-20260730/followups/c14-ngksi-sync-verify-20260731/README.md)

外部 `D:\state-learning-lab\run-data` 快照保持历史目录名和原始哈希，不随仓库内记录
重命名。可读性复核采用短标题、分行相对链接和无表格索引，避免长路径撑宽页面。
