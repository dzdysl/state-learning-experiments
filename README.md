# State Learning Experiments

该仓库只保存实验定义、精确输入、版本清单、结论和失败案例索引。一次运行明确选中的原始文件总量不超过 10 MiB 时，可以完整放入对应记录的 `raw/` 并由普通 Git 管理；超过 10 MiB 的原始日志、trace、pcap 和数据库保存在 `D:\state-learning-lab\run-data` 外部快照中，并以路径和 SHA-256 引用。

## 结构

```text
experiments/
  open5gs/<series>/<iteration>/
  free5gc/<series>/<iteration>/
  oai/<series>/<iteration>/
  cross-platform/<series>/<iteration>/
failures/
  open5gs/<failure-id>/
  free5gc/<failure-id>/
  oai/<failure-id>/
sequences/canonical/
```

临时序列放在对应 iteration 的 `inputs/`；少量长期稳定的主流程序列才进入 `sequences/canonical/`；导致崩溃的序列冻结为 failure 的 `reproducer.seq`。

## 分析所用源码

分析序列、状态差异或故障时，先从记录的 `provenance.yaml` 确认实际运行版本，再到以下位置对照源码：

- 核心网版本快照：`D:\state-learning-lab\sources\` 下的 Open5GS、free5GC、OAI 目录，具体 revision 见 `source-manifest.yaml`。
- 实际实验 UERANSIM/SUL：`D:\state-learning-lab\projects\open5gs-state-learning\src`、`free5gc-state-learning\src` 或 `oai-state-learning\src` 在对应实验 commit/tag 下的内容；历史版本使用 `git show`、detached worktree 或对应 `src.zip`。
- 原生 UERANSIM 对照：`D:\state-learning-lab\sources\UERANSIM`。
- CoreCrisis artifact：`D:\state-learning-lab\sources\CoreCrisis`；其中修改版消息适配器位于 `CoreCrisis\UERANSIM_CoreTesting`，学习器和 guided testing 实现分别位于 `Corelearner`、`CoreFuzzer`。

`D:\state-learning-lab\sources\UERANSIM_CoreTesting - 快捷方式.lnk` 不是独立源码或独立 Git 版本。分析记录应引用真实目录，并为 CoreCrisis 子目录记录父仓库 commit 和 tree hash。参考源码不能替代实际运行源码的 provenance。

## 实验记录

- [`open5gs/baseline-mdf-servreq/2026-07-17`](experiments/open5gs/baseline-mdf-servreq/2026-07-17/README.md)：Open5GS v2.6.6 baseline MDF / Service Request 主动学习运行；H23 分类、迭代细化和二分类回溯已完成，AB 聚类与寄存器推断待继续。
- [`open5gs/ueransim-registration-state-init/open5gs266-buildfix-20260728`](experiments/open5gs/ueransim-registration-state-init/open5gs266-buildfix-20260728/README.md)：Open5GS v2.6.6、UERANSIM `learner-registration-state-init-buildfix` 主动学习运行；运行在 H20 后中断，完整证据已归档，最新 H20 的 SMP SVG 已生成。
- [`open5gs/ueransim-smc-context-pdu-selection/open5gs266-smc-context-h13-interrupted-20260730`](experiments/open5gs/ueransim-smc-context-pdu-selection/open5gs266-smc-context-h13-interrupted-20260730/README.md)：Open5GS v2.6.6、UERANSIM `smc-context-pdu-selection` 主动学习运行；原 H13 冻结边界已归档，后续同一 run 已完成 H14/18-state 学习，但运行 finalizer 因 Open5GS launcher status 134 失败。

## 失败记录

- [`free5gc421-ttt-inconsistency-20260723-r14`](failures/free5gc/free5gc421-ttt-inconsistency-20260723-r14/README.md)：Free5GC v4.2.1 的 TTT 第 14 轮查询不一致；原因机制已定向复现，修复验证待进行。
- [`open5gs266-dereg-identity-smc-fatal-20260727`](failures/open5gs/open5gs266-dereg-identity-smc-fatal-20260727/README.md)：Open5GS v2.6.6 注销后由 Identity Response 重新进入鉴权，最终 SMCompl 触发 AMF 致命分支并退出；修复版本复验待进行。
