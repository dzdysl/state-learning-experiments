# State Learning Experiments

该仓库只保存实验定义、精确输入、版本清单、结论和失败案例索引。大体积原始日志、trace、pcap 和数据库保存在仓库外部，并以路径和 SHA-256 引用。

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
