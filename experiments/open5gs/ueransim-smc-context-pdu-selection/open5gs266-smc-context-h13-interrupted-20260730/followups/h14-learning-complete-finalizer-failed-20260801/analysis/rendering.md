# H14 SMP 派生图渲染记录

## 输入与输出

- 源 DOT（原始证据，未修改）：`evidence/hypotheses/hypothesis_14.dot`，SHA-256：`19fe51296d78d000d2ab7e9106d4555b0064ca7f5e7b78c608c39f05db5ca8d0`。
- SMP 派生 DOT：[hypothesis_14_smp.dot](derived/hypothesis_14_smp.dot)。
- SVG：[hypothesis_14_smp.svg](derived/hypothesis_14_smp.svg)，SHA-256：`7683a685e53fc926fc5c6d1508f8b949070d822cae6f782fac05e169197fc3e1`，26,312 bytes。
- PDF：[hypothesis_14_smp.pdf](derived/hypothesis_14_smp.pdf)，SHA-256：`837e6a6fc0bfc881a83f1a5007b1cdc35523429f2434a52fd9e9b84c50d4a5f2`，34,028 bytes。

## 可复现命令

```powershell
$record = 'D:\state-learning-lab\projects\state-learning-experiments\experiments\open5gs\ueransim-smc-context-pdu-selection\open5gs266-smc-context-h13-interrupted-20260730\followups\h14-learning-complete-finalizer-failed-20260801'
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  "$record\evidence\hypotheses\hypothesis_14.dot" `
  --simplify --formats svg,pdf --output-dir "$record\analysis\derived"
```

渲染器为 `C:\Program Files\Graphviz\bin\dot.EXE`，布局引擎为 `dot`。该图采用 SMP 派生简化：原始证据 DOT 保持不变；派生图按既定规则删除展示噪声并合并同源、同目标、同输出的转移。因此它用于分析与展示，不替代原始模型证据。

## 视觉复核

已通过临时 PNG 预览检查完整画布：18 个状态节点和连接边均被画布包含，未见裁切或缺失标签。预览文件为任务临时材料，复核后已删除；仅保留上述可复现的 DOT、SVG 与 PDF。

## H14 分层环选取与可执行序列

- 工具：`analyze_cycle_cover.py`（位于
  `D:\\state-learning-lab\\projects\\state-learning-tools\\analysis\\cycle_cover\\`），
  SHA-256：`d7d5d7eb7c40955a0e5066b1b031fc29583a06b7e25497af9bbebcdcbd9dccd5`。
- 覆盖语义：36 条 SMP 具体目标边先由 15 条简单环覆盖可简单覆盖部分；`E009`、`E033` 仅由 2 条信令合格复合闭合游走补足。6 条具体 KSI 下行自环独立追加至基础组，均重复 10 次。
- 基础组：[JSON](derived/cycle_cover/base/hypothesis_14_base_cycle_cover.json)、
  [报告](derived/cycle_cover/base/hypothesis_14_base_cycle_cover_report.md)、23 个 SVG 和
  [repeat-10 输入](../inputs/hypothesis_14_base_cycle_cover_repeat10.seq)。基础序列共 37 行；
  序列 SHA-256：`4b1f05b6e4a9030de0c0b0170124dc8ac635e03de7f872b6f4f3d1c987df9e2a`；
  JSON SHA-256：`c33beee39053578b15324564fb7960d421a470fd77fd3a507567fc61b23bc07d`。
- 额外组：保留全部 16 条长度 3/4/5 的信令合格短环，以及 30 条“短环中具体自环每轮
  重复 3 次、完整增强环重复 10 次”的变体。
  [JSON](derived/cycle_cover/extra/hypothesis_14_extra_cycle_cover.json)、
  [报告](derived/cycle_cover/extra/hypothesis_14_extra_cycle_cover_report.md)、46 个 SVG 和
  [repeat-10 输入](../inputs/hypothesis_14_extra_cycle_cover_repeat10.seq)。额外序列共 70 行；
  序列 SHA-256：`15db9da0527063c132e64eb3440fd4b549a2ec46266c3d06e0226b3c9ac0e4d4`；
  JSON SHA-256：`409b8d10be483675c39130ac0c03247a11a2c742fd29c21849b80952086a78cd`。
- 两组路线均按 `s0` 的确定性最短访问前缀、`expand` 合并输入策略构造，并逐行在过滤后的原始 H14 DOT 上模拟：每轮路线都返回其规范循环起点；`s2` 与合成起点未出现于访问前缀或循环。

```powershell
$record = 'D:\state-learning-lab\projects\state-learning-experiments\experiments\open5gs\ueransim-smc-context-pdu-selection\open5gs266-smc-context-h13-interrupted-20260730\followups\h14-learning-complete-finalizer-failed-20260801'
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\analysis\cycle_cover\analyze_cycle_cover.py `
  --dot "$record\analysis\derived\hypothesis_14_smp.dot" `
  --closure-dot "$record\evidence\hypotheses\hypothesis_14.dot" `
  --exclude-state s2 `
  --required-output authenticationRequest `
  --required-output securityModeCommand `
  --signal-mode output-only `
  --output-dir "$record\analysis\derived\cycle_cover\base" `
  --basename hypothesis_14_base `
  --formats svg `
  --sequence-output "$record\inputs\hypothesis_14_base_cycle_cover_repeat10.seq" `
  --sequence-start-state s0 `
  --sequence-repeat-count 10 `
  --sequence-merged-input-policy expand `
  --extra-output-dir "$record\analysis\derived\cycle_cover\extra" `
  --extra-basename hypothesis_14_extra `
  --extra-sequence-output "$record\inputs\hypothesis_14_extra_cycle_cover_repeat10.seq" `
  --base-overlay-output "$record\analysis\derived\cycle_cover\base\hypothesis_14_base_cycle_overlay.svg"
```

可读性复核：两份报告采用窄列的路线类型、短 ID 和分行 SVG 链接；长路线只保留在
JSON 与 SVG 中，避免 Markdown 表格因状态路径或 `input / output` 标签溢出。上述两组
JSON 统一通过 `sequence_export.cycles` 导出循环；每项有 `cycle_id`、`cycle_kind`、
`loop_length`、`loop_inputs`、具体边、自环身份、插入位置、前缀、展开变体和行号。

### 基础组总览叠图

- 派生 DOT：[hypothesis_14_base_cycle_overlay.dot](derived/cycle_cover/base/hypothesis_14_base_cycle_overlay.dot)，10,598 bytes，SHA-256：`8d2608b5d5437a0203f914569516e6f0e754050ed699238b6deb8af6b48dc5eb`。
- SVG：[hypothesis_14_base_cycle_overlay.svg](derived/cycle_cover/base/hypothesis_14_base_cycle_overlay.svg)，54,239 bytes，SHA-256：`4d9bc6227a589d6b46d8b342d45f8a56620b1cef7d4d3e4f6fccf2323324c878`。
- 图中完整保留 H14 SMP；23 条基础路线各有独立颜色。彩色实线表示 SMP 目标边，彩色虚线表示复合闭环补充边或独立自环；同一边被多条路线复用时用多色线表示，SVG tooltip 与右上角图例给出路线 ID 和类型。
- 视觉复核：已从最终 DOT 渲染临时 PNG 检查，状态、长回边、多色复用边和完整图例均在画布内可辨；临时预览不作为交付物保留。
