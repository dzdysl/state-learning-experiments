# H14 SMP 派生图渲染记录

## 输入与输出

- 源 DOT（原始证据，未修改）：`evidence/hypotheses/hypothesis_14.dot`，SHA-256：`19fe51296d78d000d2ab7e9106d4555b0064ca7f5e7b78c608c39f05db5ca8d0`。
- SMP 派生 DOT：[smp.dot](model/smp.dot)。
- SVG：[smp.svg](model/smp.svg)，SHA-256：`7683a685e53fc926fc5c6d1508f8b949070d822cae6f782fac05e169197fc3e1`，26,312 bytes。
- PDF：[smp.pdf](model/smp.pdf)，SHA-256：`837e6a6fc881a83f1a5007b1cdc35523429f2434a52fd9e9b84c50d4a5f2`，34,028 bytes。

## 可复现命令

```powershell
$record = 'D:\state-learning-lab\projects\state-learning-experiments\experiments\open5gs\ueransim-smc-context-pdu-selection\h14-complete-teardown-20260801'
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  "$record\evidence\hypotheses\hypothesis_14.dot" `
  --simplify --formats svg,pdf --basename smp --output-dir "$record\analysis\model"
```

渲染器为 `C:\Program Files\Graphviz\bin\dot.EXE`，布局引擎为 `dot`。该图采用 SMP 派生简化：原始证据 DOT 保持不变；派生图按既定规则删除展示噪声并合并同源、同目标、同输出的转移。因此它用于分析与展示，不替代原始模型证据。

## 视觉复核

已通过临时 PNG 预览检查完整画布：18 个状态节点和连接边均被画布包含，未见裁切或缺失标签。预览文件为任务临时材料，复核后已删除；仅保留上述可复现的 DOT、SVG 与 PDF。

## H14 分层环选取与可执行序列

- 工具：`analyze_cycle_cover.py`（位于
  `D:\\state-learning-lab\\projects\\state-learning-tools\\analysis\\cycle_cover\\`），
  SHA-256：`e0f993939cf138000d6d9bd587455f4e632c50d290403d7c7960411bc1d33fa1`。
- 覆盖语义：36 条 SMP 具体目标边先由 15 条简单环覆盖可简单覆盖部分；`E009`、`E033` 仅由 2 条信令合格复合闭合游走补足。6 条具体 KSI 下行自环独立追加至基础组，均重复 10 次。
- 基础组：[JSON](cycle-cover/base-result.json)、
  [报告](cycle-cover/base-report.md)、23 个 SVG 和
  [repeat-10 输入](../inputs/base-cycle-repeat10.seq)。基础序列共 37 行；
  序列 SHA-256：`4b1f05b6e4a9030de0c0b0170124dc8ac635e03de7f872b6f4f3d1c987df9e2a`；
  JSON SHA-256：`c600ebcd412db1134b0865dee357671bc833962149ce56de9442c0bd07fe6b80`。
- 额外组：保留全部 16 条长度 3/4/5 的信令合格短环，以及 30 条“短环中具体自环每轮
  重复 3 次、完整增强环重复 10 次”的变体。
  [JSON](cycle-cover/extra-result.json)、
  [报告](cycle-cover/extra-report.md)、46 个 SVG 和
  [repeat-10 输入](../inputs/extra-cycle-repeat10.seq)。额外序列共 70 行；
  序列 SHA-256：`15db9da0527063c132e64eb3440fd4b549a2ec46266c3d06e0226b3c9ac0e4d4`；
  JSON SHA-256：`6e21e0477306393a64678f9b4435a38f12e7fa1c4cd156c5f784a04c408ccc94`。
- 两组路线均按 `s0` 的确定性最短访问前缀、`expand` 合并输入策略构造，并逐行在过滤后的原始 H14 DOT 上模拟：每轮路线都返回其规范循环起点；`s2` 与合成起点未出现于访问前缀或循环。
- 每个分析组先构造包含该组全部补充闭环边和嵌入自环并集的中性 canonical DOT，给每条
  具体边写入稳定 SVG ID，再只调用一次 Graphviz。各独立 SVG 从同一 canonical SVG 派生，
  只修改当前路线边的 `fill`/`stroke` 颜色，不改变节点、边、标签、画布或线宽。基础组 23 图
  统一为 `3218pt × 1413pt`、45 个 SVG 边组；额外组 46 图统一为
  `3505pt × 1413pt`、49 个 SVG 边组。`S008` 与 `S036` 去除边颜色后的 SVG XML 完全一致。
- 视觉复核：将最终 `S008`、`S036` SVG 转为系统临时 PNG 后并排检查，画布、节点、普通边、
  补充边和标签位置一致；两图分别只高亮本路线的 3 条与 9 条具体边，未见裁切或错位。
  临时 PNG 已在验证后删除。

```powershell
$record = 'D:\state-learning-lab\projects\state-learning-experiments\experiments\open5gs\ueransim-smc-context-pdu-selection\h14-complete-teardown-20260801'
$stage = Join-Path ([System.IO.Path]::GetTempPath()) 'h14-cycle-cover-regeneration'
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\analysis\cycle_cover\analyze_cycle_cover.py `
  --dot "$record\analysis\model\smp.dot" `
  --closure-dot "$record\evidence\hypotheses\hypothesis_14.dot" `
  --exclude-state s2 `
  --required-output authenticationRequest `
  --required-output securityModeCommand `
  --signal-mode output-only `
  --output-dir "$stage\base" `
  --basename hypothesis_14_base `
  --formats svg `
  --sequence-output "$record\inputs\base-cycle-repeat10.seq" `
  --sequence-start-state s0 `
  --sequence-repeat-count 10 `
  --sequence-merged-input-policy expand `
  --extra-output-dir "$stage\extra" `
  --extra-basename hypothesis_14_extra `
  --extra-sequence-output "$record\inputs\extra-cycle-repeat10.seq" `
  --base-overlay-output "$stage\base\hypothesis_14_base_cycle_overlay.svg"
```

环工具原生输出的 `base/extra/cycles` 只作为系统临时 staging。验证内容后，最终结果按
`base-result.json`、`base-report.md`、`base-<cycle-id>-lenNN.svg`、
`extra-result.json`、`extra-report.md` 与 `extra-<cycle-id>-lenNN.svg` 的映射保存到
`analysis/cycle-cover/`，并删除 staging；不得把工具原生深层目录写回记录。

可读性复核：两份报告采用窄列的路线类型、短 ID 和分行 SVG 链接；长路线只保留在
JSON 与 SVG 中，避免 Markdown 表格因状态路径或 `input / output` 标签溢出。上述两组
JSON 统一通过 `sequence_export.cycles` 导出循环；每项有 `cycle_id`、`cycle_kind`、
`loop_length`、`loop_inputs`、具体边、自环身份、插入位置、前缀、展开变体和行号；
`figure_layout` 记录一次布局、canonical SVG 哈希和 `edge_color_only` 派生契约。

### 基础组总览叠图

- 派生 DOT：[base-overlay.dot](cycle-cover/base-overlay.dot)，10,598 bytes，SHA-256：`8d2608b5d5437a0203f914569516e6f0e754050ed699238b6deb8af6b48dc5eb`。
- SVG：[base-overlay.svg](cycle-cover/base-overlay.svg)，54,239 bytes，SHA-256：`4d9bc6227a589d6b46d8b342d45f8a56620b1cef7d4d3e4f6fccf2323324c878`。
- 图中完整保留 H14 SMP；23 条基础路线各有独立颜色。彩色实线表示 SMP 目标边，彩色虚线表示复合闭环补充边或独立自环；同一边被多条路线复用时用多色线表示，SVG tooltip 与右上角图例给出路线 ID 和类型。
- 视觉复核：已从最终 DOT 渲染临时 PNG 检查，状态、长回边、多色复用边和完整图例均在画布内可辨；临时预览不作为交付物保留。
