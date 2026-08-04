# C01–C14 边级 ngKSI 候选与输入寄存器推断

## 范围与结论

本结果基于完整轨迹 `20260802T042407Z-fa7991d2`，覆盖 C01–C14，使用 schema v3 的边级推断器。它将原始区域 `(r_before, ordered_observation_items, r_after)` 保留为证据，同时按实际 DOT 边投影出最后写入有效快照和输入寄存器更新。结果是启发式、可被未来后缀反驳的观察候选，不是 AMF 源码变量或更新时点的证明。

有限 Mealy 观察等价类不证明真实 AMF 只维护有限数量的寄存器；本实验只建模可由当前 SUL/UE 观测支持的 `r`（下行 KSI 观测）及 `ngksi_uplink`（注册上行 KSI 输入寄存器）。普通注册与 GUTI 注册都通过 YAML 显式写入同一个 `ngksi_uplink`，因此后一个事件值覆盖同类型的前一个值。第 2 轮初始化输入寄存器，第 3–10 轮用于拟合。

## 输入与复现

- 配置：[c01-c14-ngksi-signal-inference.yaml](c01-c14-ngksi-signal-inference.yaml)，SHA-256：`223d74e36b2a8cb64d0a1f87d496327bd9e19a6fd94b8472266084d672e127c1`
- 结果：[c01-c14-ngksi-signal-candidates.json](c01-c14-ngksi-signal-candidates.json)，SHA-256：`a781c065ab5d4dc31318bebd3abd90bfa299a5301ffa106ce4a69154b53c7445`
- 完整轨迹：`D:/state-learning-lab/run-data/open5gs/20260802T042407Z-fa7991d2/statelearner_trace.jsonl`，SHA-256：`73862231f221608b791df34923f871b109b889c01775a8b942e27939594eec73`
- 推断脚本：[infer_cycle_ngksi_regions.py](D:/state-learning-lab/projects/state-learning-tools/analysis/register_inference/experiments/infer_cycle_ngksi_regions.py)，本次运行 SHA-256：`64ced86271484b62dbf3366db9e8ba26f96caa3c5a50293fec84648110f8e768`

```powershell
$base = 'D:\state-learning-lab\projects\state-learning-experiments\experiments\open5gs\ueransim-smc-context-pdu-selection\open5gs266-smc-context-h13-interrupted-20260730\followups\cycle-cover-repeat10-register-analysis-20260731\analysis\derived\register_inference'
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\infer_cycle_ngksi_regions.py `
  --config "$base\c01-c14-ngksi-signal-inference.yaml" `
  --output "$base\c01-c14-ngksi-signal-candidates.json"
```

本组派生文件以 `c01-c14-*` 明确其实际覆盖范围；旧 `c01-c02-*` 名称已直接替换，不保留重复副本。

## 边级解释规则

- 直接由单一 DOT 边闭合的区域，其已观察精确候选标为 `relatively_stable_candidate`。
- 区域包含多条边时，末端携带下行锚点的边拟合 `r`；前序无锚点边使用最简候选，全部标为 `hypothetical_candidate`，并记录 `region_to_edge_decomposition` 或 `minimal_predecessor_default`。
- 每条边先更新 `ngksi_uplink`：有对应输入时 `r_i'=i`，无输入时 `r_i'=r_i`。当前不为输入寄存器枚举信号常数分支。
- 所有信号树统一写为 `ite(s=1, true, false)`。未观察、支持不足或无锚点的分支保持 `unknown`。
- 对假设性边，表中“边级候选”仍仅写跨全部样本精确成立的全局交集；JSON 的 `hypothetical_reconciliation` 同时保留按循环分区的局部候选、交集、非共识候选与冲突证据。局部候选不混入全局 `candidate_index`。

## 重点结果

<table style="width:100%; table-layout:fixed">
  <colgroup>
    <col style="width:27%">
    <col style="width:32%">
    <col style="width:16%">
    <col style="width:25%">
  </colgroup>
  <thead>
    <tr>
      <th>循环、边与节点</th>
      <th>边级候选</th>
      <th>输入寄存器</th>
      <th>候选等级</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>C01/<br>C05/<br>C08/<br>C09/<br>C12/<br>C13/<br>C14 E0019：<br><code>s1 → s3</code><br><code>authenticationResponse/<br>securityModeCommand</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>相对稳定；完整观察精确</td>
    </tr>
    <tr>
      <td>C01 E0037：<br><code>s3 → s1</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：<br><code>ite(r&lt;6,r+1,0)</code>；<br><code>s=1</code> 未观察</td>
      <td><code>r_i'=i</code></td>
      <td>相对稳定；树含未知分支</td>
    </tr>
    <tr>
      <td>C01 E0038：<br><code>s3 → s1</code><br><code>registrationRequestGUTI/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：4 个并列<br>guard 为 <code>r</code> 或 <code>r_i</code>；<br>真分支为 <code>r+1</code> 或 <code>r_i+1</code>；<br>否则均为 <code>0</code></td>
      <td><code>r_i'=i</code></td>
      <td>相对稳定；树含未知分支</td>
    </tr>
    <tr>
      <td>C02 E0160：<br><code>s13 → s12</code><br><code>deregistrationRequest/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C02/<br>C07/<br>C11/<br>C12/<br>C14 E0145：<br><code>s12 → s13</code><br>普通注册</td>
      <td>已观察 <code>s=1</code>：<br><code>ite(r&lt;6,r+1,0)</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；区域拆分</td>
    </tr>
    <tr>
      <td>C02/<br>C07/<br>C11/<br>C12/<br>C14 E0146：<br><code>s12 → s13</code><br>GUTI 注册</td>
      <td>已观察 <code>s=1</code>：2 个并列<br>guard 为 <code>r</code> 或 <code>r_i</code>；<br>真分支均为 <code>r_i+1</code>；<br>否则均为 <code>0</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；区域拆分</td>
    </tr>
    <tr>
      <td>C03/<br>C07/<br>C11/<br>C12/<br>C14 E0163：<br><code>s13 → s14</code><br><code>authenticationResponse/<br>securityModeCommand</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>相对稳定；完整观察精确</td>
    </tr>
    <tr>
      <td>C03 E0169：<br><code>s14 → s13</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：<br><code>ite(r&lt;6,r+1,0)</code>；<br><code>s=1</code> 未观察</td>
      <td><code>r_i'=i</code></td>
      <td>相对稳定；树含未知分支</td>
    </tr>
    <tr>
      <td>C03 E0170：<br><code>s14 → s13</code><br><code>registrationRequestGUTI/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：4 个并列<br>guard 为 <code>r</code> 或 <code>r_i</code>；<br>真分支为 <code>r+1</code> 或 <code>r_i+1</code>；<br>否则均为 <code>0</code></td>
      <td><code>r_i'=i</code></td>
      <td>相对稳定；树含未知分支</td>
    </tr>
    <tr>
      <td>C04 E0002：<br><code>s0 → s6</code><br><code>registrationRequestGUTI/<br>identityRequest</code></td>
      <td><code>ite(isInitMsg=1,<br>unknown,r)</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；无下行锚点</td>
    </tr>
    <tr>
      <td>C04/<br>C14 E0073：<br><code>s6 → s1</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>全局交集：无。<br>C04 局部：<code>r'=r</code>、<br><code>r'=0</code>、<code>r'=r_i-7</code>；<br>C14 局部：<code>ite(r&lt;6,r+1,0)</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；分区冲突；<br>全局交集为空</td>
    </tr>
    <tr>
      <td>C04/<br>C14 E0083：<br><code>s6 → s1</code><br><code>identityResponse/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=1</code>：2 个并列<br><code>ite(r_i&lt;6,r+1,0)</code>；<br><code>ite(r_i&lt;6,r_i+1,0)</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；区域拆分；<br>树含未知分支</td>
    </tr>
    <tr>
      <td>C04 E0016：<br><code>s1 → s0</code><br><code>deregistrationRequest/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C05/<br>C12/<br>C14 E0046：<br><code>s3 → s4</code><br><code>securityModeComplete/<br>registrationAccept</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C05 E0050：<br><code>s4 → s1</code><br><code>registrationRequestGUTI/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：4 个并列<br>guard 为 <code>r</code> 或 <code>r_i</code>；<br>真分支为 <code>r+1</code> 或 <code>r_i+1</code>；<br>否则均为 <code>0</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；区域拆分；<br>树含未知分支</td>
    </tr>
    <tr>
      <td>C06/<br>C08/<br>C13 E0085：<br><code>s7 → s8</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=1</code>：并列<br><code>r'=0</code>、<br><code>r'=r_i-7</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；区域拆分；<br>树含未知分支</td>
    </tr>
    <tr>
      <td>C06/<br>C13 E0103：<br><code>s8 → s9</code><br><code>authenticationResponse/<br>securityModeCommand</code></td>
      <td>并列 <code>r'=r</code>、<code>r'=0</code>、<br><code>r'=r_i-7</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>相对稳定；完整观察精确</td>
    </tr>
    <tr>
      <td>C06 E0114：<br><code>s9 → s7</code><br><code>securityModeReject/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C07 E0172：<br><code>s14 → s12</code><br><code>deregistrationRequest/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C08/<br>C09/<br>C13 E0042：<br><code>s3 → s7</code><br><code>securityModeReject/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C08 E0097：<br><code>s8 → s1</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：并列<br><code>r'=1</code>、<code>r'=r+1</code>、<br><code>r'=r_i-6</code></td>
      <td><code>r_i'=i</code></td>
      <td>相对稳定；树含未知分支</td>
    </tr>
    <tr>
      <td>C08 E0098：<br><code>s8 → s1</code><br><code>registrationRequestGUTI/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：<br><code>ite(r_i&lt;6,r_i+1,0)</code>；<br><code>s=1</code> 未观察</td>
      <td><code>r_i'=i</code></td>
      <td>相对稳定；树含未知分支</td>
    </tr>
    <tr>
      <td>C09 E0001：<br><code>s0 → s1</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=1</code>：并列<br><code>r'=r</code>、<code>r'=0</code>、<br><code>r'=r_i-7</code>；<br><code>s=0</code> 未观察</td>
      <td><code>r_i'=i</code></td>
      <td>假设性；区域拆分；<br>树含未知分支</td>
    </tr>
    <tr>
      <td>C09 E0086：<br><code>s7 → s6</code><br><code>registrationRequestGUTI/<br>identityRequest</code></td>
      <td><code>ite(isInitMsg=1,<br>unknown,r)</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；无下行锚点</td>
    </tr>
    <tr>
      <td>C09 E0076：<br><code>s6 → s0</code><br><code>deregistrationRequest/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C10 E0112：<br><code>s9 → s11</code><br><code>deregistrationRequest/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C10 E0127：<br><code>s10 → s9</code><br><code>authenticationResponse/<br>securityModeCommand</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>相对稳定；完整观察精确</td>
    </tr>
    <tr>
      <td>C10 E0133：<br><code>s11 → s10</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=1</code>：<br><code>ite(r&lt;6,r+1,0)</code>；<br><code>s=0</code> 未观察</td>
      <td><code>r_i'=i</code></td>
      <td>假设性；区域拆分；<br>树含未知分支</td>
    </tr>
    <tr>
      <td>C10/<br>C13 E0134：<br><code>s11 → s16</code><br><code>registrationRequestGUTI/<br>identityRequest</code></td>
      <td><code>ite(isInitMsg=1,<br>unknown,r)</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；无下行锚点</td>
    </tr>
    <tr>
      <td>C10 E0196：<br><code>s16 → s11</code><br><code>deregistrationRequest/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C11/<br>C12/<br>C14 E0051：<br><code>s4 → s5</code><br><code>registrationComplete/<br>configurationUpdateCommand</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C11 E0062：<br><code>s5 → s4</code><br><code>registrationRequestGUTI/<br>registrationAccept</code></td>
      <td><code>ite(isInitMsg=1,<br>unknown,r)</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；无下行锚点</td>
    </tr>
    <tr>
      <td>C11/<br>C12/<br>C14 E0064：<br><code>s5 → s12</code><br><code>deregistrationRequest/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C11 E0178：<br><code>s14 → s4</code><br><code>securityModeComplete/<br>registrationAccept</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C12/<br>C14 E0174：<br><code>s14 → s15</code><br><code>securityModeReject/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C12 E0181：<br><code>s15 → s1</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=1</code>：并列<br><code>r'=0</code>、<code>r'=r-1</code>、<br><code>r'=r_i-7</code>；<br><code>s=0</code> 未观察</td>
      <td><code>r_i'=i</code></td>
      <td>假设性；区域拆分；<br>树含未知分支</td>
    </tr>
    <tr>
      <td>C13 E0109：<br><code>s9 → s10</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：并列<br><code>r'=1</code>、<code>r'=r+1</code>、<br><code>r'=r_i-6</code></td>
      <td><code>r_i'=i</code></td>
      <td>相对稳定；树含未知分支</td>
    </tr>
    <tr>
      <td>C13 E0110：<br><code>s9 → s10</code><br><code>registrationRequestGUTI/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：并列<br><code>r'=1</code>、<code>r'=r+1</code>、<br><code>r'=r_i+1</code></td>
      <td><code>r_i'=i</code></td>
      <td>相对稳定；树含未知分支</td>
    </tr>
    <tr>
      <td>C13 E0124：<br><code>s10 → s11</code><br><code>deregistrationRequest/<br>null_action</code></td>
      <td><code>r'=r</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；前序最简</td>
    </tr>
    <tr>
      <td>C13 E0193：<br><code>s16 → s1</code><br><code>registrationRequest/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=0</code>：并列<br><code>r'=r</code>、<code>r'=1</code>、<br><code>r'=r_i-6</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；区域拆分；<br>树含未知分支</td>
    </tr>
    <tr>
      <td>C13 E0203：<br><code>s16 → s1</code><br><code>identityResponse/<br>authenticationRequest</code></td>
      <td>已观察 <code>s=1</code>：并列<br><code>r'=r</code>、<code>r'=1</code>、<br><code>r'=r_i+1</code></td>
      <td><code>r_i'=r_i</code></td>
      <td>假设性；区域拆分；<br>树含未知分支</td>
    </tr>
    <tr>
      <td>C14 E0182：<br><code>s15 → s6</code><br><code>registrationRequestGUTI/<br>identityRequest</code></td>
      <td><code>ite(isInitMsg=1,<br>unknown,r)</code></td>
      <td><code>r_i'=i</code></td>
      <td>假设性；无下行锚点</td>
    </tr>
  </tbody>
</table>

表格已完成可读性复核：42 个边组均已列出；每条边的起始/目标节点独占一行，循环编号及消息对均在 `/` 后显式换行；表格采用固定布局，“边级候选”列为 32%、“候选等级”列为 25%，避免长公式溢出及中文说明逐字竖排。

C07 复用 E0145、E0146 和 E0163，并新增 E0172。其 GUTI 变体提供 `r_i=0,1,2,...` 的递增样本，使 E0146 原有的 `r'=r` 与 `r'=1` 并列解释不再满足全部样本；剩余两棵精确树均在已观察 `s=1` 分支按 `r_i+1` 回绕。

C08 复用 E0019 和 E0085，并新增 E0042、E0097、E0098。C08 的第二个注册边处于已观察 `s=0` 分支：普通注册使 E0097 保留常数、`r+1` 与输入寄存器解释；GUTI 注册使 E0098 得到唯一的 `r_i+1` 回绕树。同时，C08 的反例排除了 E0085 原有的 `r'=r` 候选。

C09 复用 E0019 与 E0042，并新增 E0001、E0086、E0076。E0001 在已观察 `isInitMsg=1` 分支保留 `r'=r`、`r'=0` 与 `r'=r_i-7` 三个并列候选；E0086 是无下行锚点的 GUTI 注册边，因此仅显式保留 `ite(isInitMsg=1,unknown,r)`；E0076 按前序最简规则保持 `r'=r`。三条新边均为可被后续反驳的假设性候选。

C10 是复合闭环 `s11 → s16 → s11 → s10 → s9 → s11`。E0127 的认证响应/SMC 边独立精确满足 `r'=r`；E0133 在已观察 `isInitMsg=1` 分支满足递增回绕 `ite(r&lt;6,r+1,0)`；E0134 因无下行锚点保留 `ite(isInitMsg=1,unknown,r)`。E0112、E0196 两条去注册边均按前序最简规则保持 `r'=r`；除 E0127 外，C10 新边均为假设性候选。

C11 复用 E0145、E0146、E0163，并新增 E0051、E0062、E0064、E0178。配置更新、去注册和安全模式完成边都满足前序保持 `r'=r`；无下行锚点的 E0062 保留 `ite(isInitMsg=1,unknown,r)`，不把未观察分支解释为寄存器更新。

C12 复用 E0019、E0046、E0051、E0064、E0145、E0146、E0163，并新增 E0174、E0181。E0174 按前序最简规则保持 `r'=r`；E0181 的已观察 `s=1` 分支保留 `r'=0`、`r'=r-1` 与 `r'=r_i-7` 三个并列候选，`s=0` 仍未知。

C13 复用 E0019、E0042、E0085、E0103、E0134，并新增 E0109、E0110、E0124、E0193、E0203。两个单边注册边 E0109、E0110 在已观察 `s=0` 分支形成相对稳定候选；其余新边依赖区域拆分或前序最简规则，保持假设性。E0203 的输入寄存器在 identityResponse 边保持不变，因此候选中的 `r_i+1` 指向区域内已有的输入寄存器值，而不是该下行响应引入的新输入。

C14 复用多条已有边并新增 E0182。它还为 E0073 提供反例：同一可观测组合 `(r=0,isInitMsg=0,ngksi_uplink=7)` 在 C04 后得到 `r'=0`，在 C14 后得到 `r'=1`。因此 E0073 的全局精确交集为空；但 C04 的 `r'=r`、`r'=0`、`r'=r_i-7` 与 C14 的 `ite(r&lt;6,r+1,0)` 均作为循环局部的假设性候选保留，不能任意取舍。E0083 则收敛为两棵由 `r_i` 阈值控制的递增回绕树。

C04 E0073 的原始区域仍完整保存为：

```text
(0,{registrationRequestGUTI.isInitMsg=1},[registrationRequestGUTI.ksi=7],
   {registrationRequest.isInitMsg=0},[registrationRequest.ksi=7],0)
```

同类型最后写入投影后的末端边快照为：

```text
(0,{initial_uplink_context=0},[ngksi_uplink=7],0)
```

因此前一条 GUTI 注册事件没有被删除：它作为 E0002 的边样本与 E0073 的覆盖链保留；只是不会再作为 E0073 的第二个匿名输入/信号槽参与拟合。C14 的反例表明，最后写入投影并未消除未建模上下文；结果将其标为 `confirmed_observational_conflict`，并完整保存 C04 的 8 个与 C14 的 2 个直接证据。总计得到 42 个具体边组和 60 个全局候选索引项。

## 证据边界

`relatively_stable_candidate` 只表明单边的已观察样本无需跨边分配即精确，不能确认未知信号分支或 AMF源码。C02/C04 的候选依赖可追溯的区域拆分与最简前序假设，必须通过循环外轨迹、专门构造的未来后缀和对应版本的 AMF/UE 源码对照继续检验。对假设性边，局部候选与全局交集必须分开解读：仅同一完整类型化观察键产生多个输出时才是直接冲突；公式不同但观察键未重叠仅是分区分歧。`isInitMsg` 是伴随上行事件的传输上下文观测，不是 NAS PDU 内的显式 IE；本结果不声称它必然是 AMF 内部 KSI 更新的实际控制条件。
