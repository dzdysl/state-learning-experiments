# Free5GC T3560 重传与 UERANSIM 学习器错位

## 结论

该失败不是单一组件独立造成的，而是以下行为的组合：

1. Free5GC v4.2.1 在 `Authentication` 状态仍接受 `IdentityResponse`，每次都会触发一次认证重启，并创建新的 T3560。
2. 新的 T3560 被写入 `amfUe.T3560`，但创建前没有停止旧 T3560；因此多个认证请求 A、B、C 的定时器会并存，且按不同起始时间交错重传。
3. UERANSIM 的 replay 判定只有一个全局“最近 NAS PDU”槽 `retransbuf`。它只丢弃与最近一次已接受 PDU 字节完全相同的下行消息；旧 timer 重传的 A/B/C 只要不同于该槽当前的 PDU，仍会被接受。
4. 已接受的异步下行消息可经 `notify_response()` 写入学习器 socket；Java SUL 读取的是无请求 ID 的 FIFO 行。到达在两个输入步骤之间的旧认证请求，因而可以被下一次 `deregistrationRequest` 消费。
5. C++ trace 的下行关联窗口为 800 ms，而本次学习器配置的 Java socket 等待为 1500 ms；此外 socket FIFO 与 trace 没有共同的步骤标识。因此“学习器实际读到的输出”和“trace 写入的该步骤输出”可以不同。

所以，`deregistrationRequest → authenticationRequest` 并非 AMF 对注销的协议响应，而是旧认证事务的 T3560 重传被错误归属给了注销输入。

## 范围、版本与证据边界

| 项目 | 取值 |
|---|---|
| 原始失败记录 | `free5gc421-ttt-inconsistency-20260723-r14` |
| 核心网源码快照 | `D:\state-learning-lab\sources\free5gc-v4.2.1`，父仓库 `52d9545faa42efca71b1567cf7d5c5afca79f490`，AMF 子模块 `92b7a3f85764b21b5a94204afc2fb1effff5ed15` |
| 复现运行的学习器 commit | `8448e4173816b7c33dceb0c4a89a79a3c4067ae7` |
| 复现运行包 | `../raw/run-20260724T094326Z-d078312d.zip`，34,332 bytes，SHA-256 `2D84C1FD065A9B392F2D114C512D2F75CBF25BAB209FB4998260D4CCC4AFF3D3` |
| 复现运行 ID | `20260724T094326Z-d078312d` |

复现包冻结了 AMF 日志、console、`statelearner_trace.jsonl` 和调试事件；未包含 UE/gNB 原始日志或 pcap。因此“AMF 已向 RAN 下发”由 AMF 日志直接证明；具体 NAS PDU 被学习器接收则以 trace 中冻结的 PDU 为准。复现包未冻结 UERANSIM 二进制 SHA-256，故源码相关结论与该运行配置、trace 现象一致，但二进制同一性仍是可改进的溯源项。

## 原始矛盾序列

冻结输入见 [`inputs/failure-query.txt`](../../../inputs/failure-query.txt)：

```text
registrationRequestGUTI identityResponse deregistrationRequest identityResponse registrationRequest registrationRequest identityResponse deregistrationAccept registrationRequest
```

三次观测输出为：

| 运行 | 九步输出 |
|---|---|
| 1 | `identityRequest authenticationRequest null_action authenticationRequest null_action authenticationRequest authenticationRequest authenticationRequest null_action` |
| 2 | `identityRequest authenticationRequest null_action authenticationRequest null_action authenticationRequest authenticationRequest null_action authenticationRequest` |
| 3 | `identityRequest authenticationRequest null_action authenticationRequest null_action authenticationRequest authenticationRequest null_action authenticationRequest` |

`hypothesis_13` 对同一输入预测第 7–9 步均为 `null_action`。三次实测都在第 7 步出现额外的 `authenticationRequest`；第 8、9 步的额外认证请求又在不同运行间错位。这是主动学习中同一输入序列产生非确定输出的直接表现。

## 本次复现实验

复现输入为：

```text
registrationRequestGUTI identityResponse identityResponse identityResponse deregistrationRequest deregistrationRequest deregistrationRequest deregistrationRequest deregistrationRequest
```

前三次 `identityResponse` 让 AMF 在 Authentication 状态连续创建三组认证请求，分别记为 A、B、C。三个初始认证请求的下行 NAS PDU 均不同；trace 中 B 为：

```text
7E005600020000211428A7078C952428465AB85774EA6B5C20104E83CD4E75E08000679A89EA78570DC3
```

控制台的抽象输出是：

```text
identityRequest authenticationRequest authenticationRequest authenticationRequest null_action authenticationRequest authenticationRequest authenticationRequest authenticationRequest
```

这表示五次 `deregistrationRequest` 中第一次为 `null_action`，随后的四次均读到 `authenticationRequest`。AMF 日志同时证明每一次注销请求在 Authentication 状态均为 state mismatch，未触发新的认证过程。

trace 冻结的结果为：

```text
identityRequest authenticationRequest authenticationRequest authenticationRequest null_action authenticationRequest null_action null_action authenticationRequest
```

trace 的第 6、9 步均保存了上面的 B PDU，分别对应 B 的 retry #2 与 retry #3。这是 PDU 层面的重传错位复现。第 7、8 步与控制台不同：console 读到 `authenticationRequest`，而 trace 在 800 ms 后写入 `null_action`。该差异是独立的观测/归因缺陷，不能把第 7、8 步当成 trace 已精确归属的证据。

### 核心时间线（Asia/Hong_Kong）

| 时间 | AMF / 学习器事件 | 解释 |
|---|---|---|
| 17:44:11.917 | 初始认证 A，启动 T3560-A | 第一次 `identityResponse` 的正常输出 |
| 17:44:12.479 | 初始认证 B，启动 T3560-B | 第二次 `identityResponse` 的正常输出 |
| 17:44:13.043 | 初始认证 C，启动 T3560-C | 第三次 `identityResponse` 的正常输出 |
| 17:44:13.547 | 第 1 次注销，Authentication state mismatch | trace 第 5 步为 `null_action` |
| 17:44:17.918 / 18.480 / 19.044 | A / B / C retry #1 | 三组旧 timer 开始交错下行 |
| 17:44:23.922 | A retry #2 | 在第 6 次输入前到达，可进入 socket FIFO |
| 17:44:24.138 | 第 2 次注销，Authentication state mismatch | AMF 没有新认证响应 |
| 17:44:24.479 | B retry #2 | trace 第 6 步收到 B PDU |
| 17:44:25.044 | C retry #2 | 可落在 trace/Java 的不同归因窗口 |
| 17:44:25.985 / 27.850 | 第 3 / 4 次注销，均 state mismatch | console 仍可读取已排队的旧下行 |
| 17:44:29.918 | A retry #3 | 继续交错重传 |
| 17:44:30.181 | 第 5 次注销，Authentication state mismatch | AMF 仍没有新认证响应 |
| 17:44:30.479 | B retry #3 | trace 第 9 步再次收到同一 B PDU |
| 17:44:31.044 | C retry #3 | 运行随后退出 |

## 源码关联

### Free5GC：认证重启留下并行 T3560

在 AMF 的 `Authentication` 状态中，`IdentityResponse` 被处理并发送 `AuthRestartEvent`：

- [`NFs/amf/internal/gmm/sm.go:187`](D:/state-learning-lab/sources/free5gc-v4.2.1/NFs/amf/internal/gmm/sm.go:187) 匹配 `IdentityResponse`；
- [`sm.go:196`](D:/state-learning-lab/sources/free5gc-v4.2.1/NFs/amf/internal/gmm/sm.go:196) 发送 `AuthRestartEvent`；
- [`sm.go:160`](D:/state-learning-lab/sources/free5gc-v4.2.1/NFs/amf/internal/gmm/sm.go:160) 的 `AuthRestartEvent` 再次执行认证过程。

认证请求在一次调用中构建为 `nasMsg`，随后被 timer 回调闭包捕获并原样重发：

- [`NFs/amf/internal/gmm/message/send.go:166`](D:/state-learning-lab/sources/free5gc-v4.2.1/NFs/amf/internal/gmm/message/send.go:166) 构建 `nasMsg`；
- [`send.go:179`](D:/state-learning-lab/sources/free5gc-v4.2.1/NFs/amf/internal/gmm/message/send.go:179) 创建并赋值新的 `amfUe.T3560`；
- [`send.go:183`](D:/state-learning-lab/sources/free5gc-v4.2.1/NFs/amf/internal/gmm/message/send.go:183) 在 callback 中重发该 `nasMsg`。

在该路径中，新 timer 赋值前没有停止旧 timer。因而 A、B、C 虽共享同一 `amfUe.T3560` 字段名，实际先前创建的计时器仍可运行；复现日志中的三条 retry #1/#2/#3 交错时间戳直接证实了这一点。

### UERANSIM：单槽完整 PDU replay 判断

对复现运行记录的历史 commit 执行 `git show 8448e4173816b7c33dceb0c4a89a79a3c4067ae7:src/ue/nas/mm/sap.cpp`，可见学习器改造的 UERANSIM 在 NAS 投递入口定义了一个全局 `retransbuf`：

- [`src/ue/nas/mm/sap.cpp:18`](D:/state-learning-lab/projects/free5gc-state-learning/src/ue/nas/mm/sap.cpp:18) 定义 `retransbuf`；
- [`sap.cpp:39`](D:/state-learning-lab/projects/free5gc-state-learning/src/ue/nas/mm/sap.cpp:39) 只要新 PDU 与该槽不同，就解码并交给 `receiveNasMessage()`；
- [`sap.cpp:49`](D:/state-learning-lab/projects/free5gc-state-learning/src/ue/nas/mm/sap.cpp:49) 用新 PDU 覆盖该槽；
- [`sap.cpp:58`](D:/state-learning-lab/projects/free5gc-state-learning/src/ue/nas/mm/sap.cpp:58) 仅在相等时丢弃为 replay。

因此，单一认证事务的重传会被抑制：`A` 初始到达后，`A` 重传仍等于 `retransbuf`。但在三组事务交错时，状态为 `retransbuf=C` 时旧 `A` 重传不等于 C，会被错误地当作新下行接受；随后槽变为 A，旧 B 重传又会通过。该判定不是按 NAS 事务、timer 或历史集合去重。

### 学习器：trace 与 socket 的归因窗口不同

以下行为同样通过 `git show 8448e4173816b7c33dceb0c4a89a79a3c4067ae7:<path>` 对历史 revision 核对，而不是以当前工作树替代：

- [`state_learner.cpp:547`](D:/state-learning-lab/projects/free5gc-state-learning/src/ue/app/state_learner.cpp:547) 在发送上行后建立 800 ms trace 超时；若没有活动 trace 的下行输出，记录 `null_action`。
- [`state_learner.cpp:678`](D:/state-learning-lab/projects/free5gc-state-learning/src/ue/app/state_learner.cpp:678) 只在 `currentTrace.active` 时把下行写入 trace。
- [`Open5GSSUL.java:159`](D:/state-learning-lab/projects/free5gc-state-learning/Corelearner_free5gc/mylearner/src/main/java/org/example/corelearner/devices/Open5GSSUL.java:159) 使用配置的 socket 读取超时；复现配置为 1500 ms。
- [`Open5GSSUL.java:240`](D:/state-learning-lab/projects/free5gc-state-learning/Corelearner_free5gc/mylearner/src/main/java/org/example/corelearner/devices/Open5GSSUL.java:240) 直接读取下一行 socket 响应，没有步骤 ID、发送时间或 NAS PDU 关联校验。

故一个消息可能在 trace 不活动时已写入 socket，随后被下一 Java step 读取；也可能在 trace 的 800 ms 已判定 `null_action` 后、但仍在 Java 的 1500 ms 等待内到达。这解释了复现中 console 与 trace 对第 7、8 步的不同记录。

## 组合因果链

```text
重复 IdentityResponse
  → Free5GC Authentication/AuthRestart
  → A、B、C 各自创建 T3560，旧 timer 未停止
  → A/B/C 以不同相位重传
  → 旧 PDU 与 UERANSIM 最近 PDU 不同
  → 单槽 replay 检查放行
  → 异步 notify_response 写入无关联 socket FIFO
  → 后续 deregistrationRequest 读取旧 authenticationRequest
  → 同一抽象输入序列出现非确定输出，TTT 缓存/假设冲突
```

## 结论强度与未决项

| 结论 | 强度 | 依据 |
|---|---|---|
| AMF 同时存在 A/B/C 三组 T3560 重传 | 已确认 | 复现 AMF 日志的三组交错 retry 时间戳；对应源码 |
| 注销请求未触发新的认证响应 | 已确认 | 五次注销均为 Authentication state mismatch；没有相应的 `Send Authentication Request` |
| 第 6、9 步的 AuthReq 是旧 B 重传 | 已确认 | trace 中的 PDU 与第 3 步 B 完全相同，且与 B retry #2/#3 时间对齐 |
| UERANSIM 单槽 replay 使不同旧 PDU 被放行 | 高置信 | 对应源码和复现行为一致；运行二进制哈希未冻结 |
| 第 7、8 步 console/trace 差异来自窗口与 FIFO 归因 | 高置信但尚非逐事件证明 | 800 ms 与 1500 ms 窗口、无步骤关联读取，以及现象一致；缺少 UE/gNB 原始接收日志 |

该报告描述根因和复现实验，不表示已修复。修复验证应分别测试：Free5GC 认证重启前取消旧 T3560；UERANSIM 按事务/完整重放历史而非单槽去重；以及学习器在 socket 输出中携带并校验步骤/时间/PDU 关联，或在步骤边界显式隔离异步旧消息。
