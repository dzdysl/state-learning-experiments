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

## 失败记录

- [`free5gc421-ttt-inconsistency-20260723-r14`](failures/free5gc/free5gc421-ttt-inconsistency-20260723-r14/README.md)：Free5GC v4.2.1 的 TTT 第 14 轮查询不一致；原因机制已定向复现，修复验证待进行。
