# C01–C05 时序信号门控 ngKSI 候选推断

## 范围与结论

本结果使用完整轨迹 `20260802T042407Z-fa7991d2`，对 C01–C05 的重复 2–10 进行区域对齐和候选推断，替换先前仅覆盖 C01 的派生结果。区域表示为
`(r_before, ordered_observation_items, r_after)`；每个观察项保留类型、字段、逻辑输入、轨迹位置和出现序号。

本次配置把 `ue_side.fields.isInitMsg` 作为 `registrationRequest` 与
`registrationRequestGUTI` 所伴随的“初始上行传输上下文信号”。它不是 NAS PDU 内的显式 IE；AMF 可以通过 NGAP `InitialUEMessage` 与普通 `UplinkNASTransport` 的不同入口区分这一上下文。当前消息选择范围只是 C01–C05 的验证配置，不是推断代码的限制。

源码对照边界如下：实验版 UERANSIM/SUL 的源码相关版本为
`open5gs-state-learning@f1b98aca9292eda0eb8193d78bda994b2adf0a39`（标签
`isinitmsg-uplink-trace-20260801`）。`src/ue/nas/mm/messaging.cpp:279-288,349-358`
构造并清除该信号，`src/ue/app/state_learner.cpp:635-676` 将其写入轨迹；当前没有捕获该次部署二进制或源码归档哈希，因此这里只作源码相关性说明，不把当前工作树哈希冒充部署哈希。Open5GS v2.6.6 快照
`499c70d8be63e5f87c2d1c2bcd4e521d50ce1a6b` 在
`src/amf/ngap-sm.c:85-89` 分派两个 NGAP procedure，并在
`src/amf/ngap-handler.c:374-724` 使用不同处理入口。

## 输入与复现

- 配置：[c01-c02-ngksi-signal-inference.yaml](c01-c02-ngksi-signal-inference.yaml)，SHA-256：`cb5ee706cb2aa3b5bc6ec471d7970784564e77f4d6f215a3a82884119a887d2a`
- 结果：[c01-c02-ngksi-signal-candidates.json](c01-c02-ngksi-signal-candidates.json)，SHA-256：`a74eda8e826fbda74726cb475329d8c6859f429c8fe30dad36adec36fdc554a9`
- 完整轨迹：`D:/state-learning-lab/run-data/open5gs/20260802T042407Z-fa7991d2/statelearner_trace.jsonl`，SHA-256：`73862231f221608b791df34923f871b109b889c01775a8b942e27939594eec73`
- 推断脚本：`D:/state-learning-lab/projects/state-learning-tools/analysis/register_inference/experiments/infer_cycle_ngksi_regions.py`，本次运行版本 SHA-256：`842315a53bb0e1bf0e17cc7851a66e22ccad10175692db85ccf9b892d9b1289a`

```powershell
$base = 'D:\state-learning-lab\projects\state-learning-experiments\experiments\open5gs\ueransim-smc-context-pdu-selection\open5gs266-smc-context-h13-interrupted-20260730\followups\cycle-cover-repeat10-register-analysis-20260731\analysis\derived\register_inference'
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\infer_cycle_ngksi_regions.py `
  --config "$base\c01-c02-ngksi-signal-inference.yaml" `
  --output "$base\c01-c02-ngksi-signal-candidates.json"
```

为直接补齐既有派生文件且避免保留重复副本，配置、结果和总结的文件名仍沿用
`c01-c02-*`；这些文件当前的实际内容范围均为 C01–C05。

## 实际观察组合

C02 普通注册边 E0145 的前三个对齐区域为：

```text
(1,{initial_uplink_context=1},[registration_ksi_value=7],2)
(2,{initial_uplink_context=1},[registration_ksi_value=7],3)
(3,{initial_uplink_context=1},[registration_ksi_value=7],4)
```

三个观察项均来自对应轨迹事件；内部 JSON 还记录了 `input_symbol`、`field_path`、
`event_position`、`trace_line` 和 `occurrence_index`。信号在同一事件中稳定排列于数值输入之前。

C03 普通注册边 E0169 的序列从 `(2,{0},[7],3)` 递增，在第 6 轮形成
`(6,{0},[7],0)` 回绕；GUTI 注册边 E0170 的输入槽随寄存器同步变化，例如
`(2,{0},[2],3)`、`(3,{0},[3],4)`。

C04 E0073 跨越两个携带注册 KSI 的上行事件；其区域中的有序观察项为
`(0,{1},[7],{0},[7],0)`。因此它不是两个匿名输入，而是两个带独立消息位置和
出现序号的信号/数值槽位对。C05 继续经过 E0019，令该具体 SMC 边的总支持样本由
C01 的 18 个增加至 27 个。

## 候选结果

| 循环与具体边 | 区域数 | 信号分支 | 保留候选 |
| --- | ---: | --- | --- |
| C01/C05 E0019：`s1 → s3`，`authenticationResponse / securityModeCommand` | 27 | 无配置匹配信号 | `r' = r` |
| C01 E0037：`s3 → s1`，`registrationRequest / authenticationRequest` | 9 | 已观察 `s0=0`；`s0=1` 未观察 | `s0=0` 时 `ite(r < 6, r + 1, 0)` |
| C01 E0038：`s3 → s1`，`registrationRequestGUTI / authenticationRequest` | 9 | 已观察 `s0=0`；`s0=1` 未观察 | `s0=0` 时保留 4 个并列精确树：guard 为 `r < 6` 或 `i0 < 6`，真分支为 `r + 1` 或 `i0 + 1`，否则均为 0 |
| C02 E0145：`s12 → s13`，`registrationRequest / authenticationRequest` | 9 | 已观察 `s0=1`；`s0=0` 未观察 | `s0=1` 时 `ite(r < 6, r + 1, 0)` |
| C02 E0146：`s12 → s13`，`registrationRequestGUTI / authenticationRequest` | 9 | 已观察 `s0=1`；`s0=0` 未观察 | `s0=1` 时并列保留 `r'=r`、`r'=1`、`r'=i0+1` |
| C03 E0163：`s13 → s14`，`authenticationResponse / securityModeCommand` | 18 | 无配置匹配信号 | `r' = r` |
| C03 E0169：`s14 → s13`，`registrationRequest / authenticationRequest` | 9 | 已观察 `s0=0`；`s0=1` 未观察 | `s0=0` 时 `ite(r < 6, r + 1, 0)` |
| C03 E0170：`s14 → s13`，`registrationRequestGUTI / authenticationRequest` | 9 | 已观察 `s0=0`；`s0=1` 未观察 | `s0=0` 时保留与 C01 E0038 相同的 4 个并列精确回绕树 |
| C04 E0073：`s6 → s1`，`registrationRequest / authenticationRequest` | 9 | 已观察 `s0=1,s1=0`；另两个组合未观察 | 已观察叶子并列保留 `r'=r`、`r'=0`、`r'=i0-7`、`r'=i1-7` |
| C04 E0083：`s6 → s1`，`identityResponse / authenticationRequest` | 9 | 已观察 `s0=1`；`s0=0` 未观察 | 已观察叶子并列保留 `r'=r`、`r'=0`、`r'=i0-7` |
| C05 E0050：`s4 → s1`，`registrationRequestGUTI / authenticationRequest` | 9 | 已观察 `s0=0`；`s0=1` 未观察 | 4 个并列精确回绕树：guard 为 `r < 6` 或 `i0 < 6`，真分支为 `r+1` 或 `i0+1`，否则均为 0 |

C02 的 E0160、C05 的 E0016 和 E0046 不产生配置的终止下行 KSI 观察，因此没有形成待拟合区域；其中 E0016、E0046 仅保留规则 ④ 的结构性 `r'=r` 候选。C05 E0002 虽有上行 KSI 输入，但没有相邻的配置下行锚点，不能构造观察区域。所有含未观察信号分支的树均正确标记为
`partial_observational_candidate`，没有把未知分支伪装成精确结论。`candidate_index` 以有序
`guard_path + update_tree + status` 为键维护具体 DOT 边集合。C03 与 C01 的三个候选结构分别相同，索引因此把 E0019/E0163、E0037/E0169 和 E0038/E0170 合并到相应候选的边集合中；加入 C04/C05 后，因两层信号树和额外叶子候选出现，总索引项为 16。

## 方法边界与论文定位

该实现可表述为“协议上下文引导的类型化时序模型树”。信号门控、数值回绕和反例派生值使用不同节点类型与独立深度预算；派生值分裂仅在基础公式和回绕树均失败后启用，并要求两侧非空且分别满足最小连续支持。因此，它作为受约束的黑盒候选生成方法是可论证的。

但 C01–C05 只验证门控结构及各自已观察分支。它们不能验证未知分支，也不能证明 `isInitMsg` 必然参与 AMF 内部的 KSI 更新。论文中的泛化结论仍需要循环外轨迹或留出轨迹，并应继续与对应版本的 AMF 和 UERANSIM/SUL 源码交叉核对。
