# ngKSI 边级寄存器候选推断摘要

## 范围与读取规则

本报告由 schema v3 推断器直接生成。循环以 `cycle_id` 为主键；变体 `Vxx` 只描述
`expand` 产生的逻辑输入差异，不把物理 `.seq` 行号作为报告主键。每个具体 DOT 边均按
`src → dst` 与 `input / output` 列出；同一边在不同循环中的使用不会合并删除。

候选是可被后续行为反驳的观察候选，不是 AMF 源码变量或源码级更新时点。观察区域统一写为
`(r_before, ordered_observation_items, r_after)`；信号量使用 `{signal=value}`，数值输入使用
`[input=value]`。假设性候选先取循环局部最简树交集；交集为空时执行联合拟合。联合拟合无候选
只记录该具体边的生成失败，仍保留相对稳定推断迁移检验和前序反推。

## 输入与参数

- 拟合轮次：`R2–R10`；输入寄存器初始化：`R2`；拟合起点：`R3`。
- <code>base-result.json</code>：<code>c600ebcd412db1134b0865dee357671bc833962149ce56de9442c0bd07fe6b80</code>
- <code>hypothesis_14.dot</code>：<code>19fe51296d78d000d2ab7e9106d4555b0064ca7f5e7b78c608c39f05db5ca8d0</code>
- <code>base-cycle-repeat10.seq</code>：<code>4b1f05b6e4a9030de0c0b0170124dc8ac635e03de7f872b6f4f3d1c987df9e2a</code>
- <code>statelearner_trace.jsonl</code>：<code>cbab627a7542e72733f2cd5576926bc8621f5e330067e43a985bf341a49a68c6</code>
- <code>config.yaml</code>：<code>1a99fd6bcd442b42e7e6cfc544859731c854d506d83bc9ec442de7710b866f89</code>
- <code>candidates.json</code>：同次命令生成的机器可读候选 JSON
- JSON、YAML、完整原始 trace 与环导出均由同次命令记录；不使用 cleaned trace。

## 重点结果

下表按 H13 固定四列格式整理全部具体 DOT 边组。全局候选保留并列公式；完整树、循环局部候选、
交集或联合拟合、相对稳定推断迁移和前序反推均保留在 JSON。

<table id="edge-summary" style="width:100%; table-layout:fixed">
  <colgroup>
    <col style="width:27%">
    <col style="width:32%">
    <col style="width:16%">
    <col style="width:25%">
  </colgroup>
  <thead><tr><th>循环、边与节点</th><th>边级候选</th><th>输入寄存器</th><th>候选等级</th></tr></thead>
  <tbody>
    <tr>
      <td><code>S018</code> <code>E0001</code><br>
        <code>s0 → s1</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          r&#x27; = r<br>
        else:<br>
          unknown</code><br>
        <br>
        <code>if s0 == 1:<br>
          r&#x27; = 0<br>
        else:<br>
          unknown</code><br>
        <br>
        <code>if s0 == 1:<br>
          r&#x27; = r_i - 7<br>
        else:<br>
          unknown</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        采用最简交集</td>
    </tr>
    <tr>
      <td><code>S008</code> <code>E0002</code><br>
        <code>s0 → s6</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>identityRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r</code><br>
        <br>
        前序反推分支：<br>
        <code>S008</code> 反推 <code>{isInitMsg=1}</code>：<br>
        <code>r&#x27; = 7</code><br>
        <br>
        <code>r&#x27; = 6</code><br>
        <br>
        <code>r&#x27; = r + 6</code><br>
        <br>
        <code>r&#x27; = r + 7</code><br>
        <br>
        <code>r&#x27; = r_i - 1</code><br>
        <br>
        <code>r&#x27; = r_i</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>L001</code> <code>E0013</code><br>
        <code>s1 → s1</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>L002</code> <code>E0014</code><br>
        <code>s1 → s1</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S008</code> <code>E0016</code><br>
        <code>s1 → s0</code><br>
        <code>deregistrationRequest</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>F002</code><br>
        <code>S002</code><br>
        <code>S009</code><br>
        <code>S017</code><br>
        <code>S018</code><br>
        <code>S036</code><br>
        <code>S037</code><br>
        <code>S039</code> <code>E0019</code><br>
        <code>s1 → s3</code><br>
        <code>authenticationResponse</code>/<br>
        <code>securityModeCommand</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S002</code> <code>E0037</code><br>
        <code>s3 → s1</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S002</code> <code>E0038</code><br>
        <code>s3 → s1</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S017</code><br>
        <code>S018</code><br>
        <code>S037</code><br>
        <code>S039</code> <code>E0042</code><br>
        <code>s3 → s7</code><br>
        <code>securityModeReject</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>F002</code><br>
        <code>S009</code><br>
        <code>S036</code> <code>E0046</code><br>
        <code>s3 → s4</code><br>
        <code>securityModeComplete</code>/<br>
        <code>registrationAccept</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>F002</code><br>
        <code>S009</code> <code>E0050</code><br>
        <code>s4 → s1</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        采用最简交集</td>
    </tr>
    <tr>
      <td><code>F002</code><br>
        <code>S022</code><br>
        <code>S036</code> <code>E0051</code><br>
        <code>s4 → s5</code><br>
        <code>registrationComplete</code>/<br>
        <code>configurationUpdateCommand</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>F002</code> <code>E0062</code><br>
        <code>s5 → s4</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>registrationAccept</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S022</code><br>
        <code>S036</code> <code>E0064</code><br>
        <code>s5 → s12</code><br>
        <code>deregistrationRequest</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S008</code><br>
        <code>S036</code> <code>E0073</code><br>
        <code>s6 → s1</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td>无</td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        联合拟合失败；本边没有前向候选，但仍保留迁移检验与前序反推资格</td>
    </tr>
    <tr>
      <td><code>S018</code> <code>E0076</code><br>
        <code>s6 → s0</code><br>
        <code>deregistrationRequest</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S008</code><br>
        <code>S036</code> <code>E0083</code><br>
        <code>s6 → s1</code><br>
        <code>identityResponse</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0<br>
        else:<br>
          unknown</code><br>
        <br>
        <code>if s0 == 1:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0<br>
        else:<br>
          unknown</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        联合拟合成功</td>
    </tr>
    <tr>
      <td><code>S010</code><br>
        <code>S017</code><br>
        <code>S037</code><br>
        <code>S039</code> <code>E0085</code><br>
        <code>s7 → s8</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          r&#x27; = 0<br>
        else:<br>
          unknown</code><br>
        <br>
        <code>if s0 == 1:<br>
          r&#x27; = r_i - 7<br>
        else:<br>
          unknown</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        采用最简交集</td>
    </tr>
    <tr>
      <td><code>S018</code> <code>E0086</code><br>
        <code>s7 → s6</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>identityRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S017</code> <code>E0097</code><br>
        <code>s8 → s1</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = 1</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r + 1</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r_i - 6</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S017</code> <code>E0098</code><br>
        <code>s8 → s1</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S010</code><br>
        <code>S037</code><br>
        <code>S039</code> <code>E0103</code><br>
        <code>s8 → s9</code><br>
        <code>authenticationResponse</code>/<br>
        <code>securityModeCommand</code></td>
      <td><code>r&#x27; = r</code><br>
        <br>
        <code>r&#x27; = 0</code><br>
        <br>
        <code>r&#x27; = r_i - 7</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S037</code> <code>E0109</code><br>
        <code>s9 → s10</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = 1</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r + 1</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r_i - 6</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S037</code> <code>E0110</code><br>
        <code>s9 → s10</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = 1</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r + 1</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r_i + 1</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S039</code> <code>E0112</code><br>
        <code>s9 → s11</code><br>
        <code>deregistrationRequest</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S010</code> <code>E0114</code><br>
        <code>s9 → s7</code><br>
        <code>securityModeReject</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>L003</code> <code>E0121</code><br>
        <code>s10 → s10</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>L004</code> <code>E0122</code><br>
        <code>s10 → s10</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = 1</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r_i + 1</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>F001</code><br>
        <code>S003</code> <code>E0124</code><br>
        <code>s10 → s11</code><br>
        <code>deregistrationRequest</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S004</code><br>
        <code>S037</code><br>
        <code>S039</code> <code>E0127</code><br>
        <code>s10 → s17</code><br>
        <code>authenticationResponse</code>/<br>
        <code>securityModeCommand</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>F001</code><br>
        <code>S003</code><br>
        <code>S039</code> <code>E0133</code><br>
        <code>s11 → s10</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0<br>
        else:<br>
          unknown</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        联合拟合成功</td>
    </tr>
    <tr>
      <td><code>F001</code><br>
        <code>S037</code> <code>E0134</code><br>
        <code>s11 → s16</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>identityRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S005</code><br>
        <code>S012</code><br>
        <code>S022</code><br>
        <code>S036</code> <code>E0145</code><br>
        <code>s12 → s13</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0<br>
        else:<br>
          unknown</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        采用最简交集</td>
    </tr>
    <tr>
      <td><code>S005</code><br>
        <code>S012</code><br>
        <code>S022</code><br>
        <code>S036</code> <code>E0146</code><br>
        <code>s12 → s13</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0<br>
        else:<br>
          unknown</code><br>
        <br>
        <code>if s0 == 1:<br>
          if r &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0<br>
        else:<br>
          unknown</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        联合拟合成功</td>
    </tr>
    <tr>
      <td><code>L005</code> <code>E0157</code><br>
        <code>s13 → s13</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>L006</code> <code>E0158</code><br>
        <code>s13 → s13</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = 1</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r_i + 1</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S005</code> <code>E0160</code><br>
        <code>s13 → s12</code><br>
        <code>deregistrationRequest</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S006</code><br>
        <code>S012</code><br>
        <code>S022</code><br>
        <code>S036</code> <code>E0163</code><br>
        <code>s13 → s14</code><br>
        <code>authenticationResponse</code>/<br>
        <code>securityModeCommand</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S006</code> <code>E0169</code><br>
        <code>s14 → s13</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S006</code> <code>E0170</code><br>
        <code>s14 → s13</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S012</code> <code>E0172</code><br>
        <code>s14 → s12</code><br>
        <code>deregistrationRequest</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S036</code> <code>E0174</code><br>
        <code>s14 → s15</code><br>
        <code>securityModeReject</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S022</code> <code>E0178</code><br>
        <code>s14 → s4</code><br>
        <code>securityModeComplete</code>/<br>
        <code>registrationAccept</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S039</code> <code>E0181</code><br>
        <code>s15 → s1</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          r&#x27; = 0<br>
        else:<br>
          unknown</code><br>
        <br>
        <code>if s0 == 1:<br>
          r&#x27; = r - 1<br>
        else:<br>
          unknown</code><br>
        <br>
        <code>if s0 == 1:<br>
          r&#x27; = r_i - 7<br>
        else:<br>
          unknown</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        采用最简交集</td>
    </tr>
    <tr>
      <td><code>S036</code> <code>E0182</code><br>
        <code>s15 → s6</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>identityRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S037</code> <code>E0193</code><br>
        <code>s16 → s1</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = 2</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r + 1</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          r&#x27; = r_i - 5</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>假设性<br>
        采用最简交集</td>
    </tr>
    <tr>
      <td><code>F001</code> <code>E0196</code><br>
        <code>s16 → s11</code><br>
        <code>deregistrationRequest</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S037</code> <code>E0203</code><br>
        <code>s16 → s1</code><br>
        <code>identityResponse</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          r&#x27; = 2<br>
        else:<br>
          unknown</code><br>
        <br>
        <code>if s0 == 1:<br>
          r&#x27; = r + 1<br>
        else:<br>
          unknown</code><br>
        <br>
        <code>if s0 == 1:<br>
          r&#x27; = r_i + 1<br>
        else:<br>
          unknown</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        采用最简交集</td>
    </tr>
    <tr>
      <td><code>S004</code> <code>E0205</code><br>
        <code>s17 → s10</code><br>
        <code>registrationRequest</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S004</code> <code>E0206</code><br>
        <code>s17 → s10</code><br>
        <code>registrationRequestGUTI</code>/<br>
        <code>authenticationRequest</code></td>
      <td><code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r_i &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0</code><br>
        <br>
        <code>if s0 == 1:<br>
          unknown<br>
        else:<br>
          if r &lt; 6:<br>
            r&#x27; = r_i + 1<br>
          else:<br>
            r&#x27; = 0</code></td>
      <td><code>r_i&#x27; = i</code></td>
      <td>相对稳定<br>
        相对稳定推断</td>
    </tr>
    <tr>
      <td><code>S037</code> <code>E0208</code><br>
        <code>s17 → s11</code><br>
        <code>deregistrationRequest</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
    <tr>
      <td><code>S039</code> <code>E0210</code><br>
        <code>s17 → s15</code><br>
        <code>securityModeReject</code>/<br>
        <code>null_action</code></td>
      <td><code>r&#x27; = r</code></td>
      <td><code>r_i&#x27; = r_i</code></td>
      <td>假设性<br>
        无下行锚点</td>
    </tr>
  </tbody>
</table>

## 相对稳定推断迁移检验

工具按相同有效信号上下文、`input` 和 `output` 合并单边区域，形成带信号门控的相对稳定推断，
再把目标映射观察区域的 `r_before`、有效 `r_i` 和信号量直接代入模型树。

- 相对稳定推断组数：3；假设性目标边数：9；目标循环分区数：22。
- 迁移成功：4；无匹配相对稳定推断：17；迁移失败并尝试前序反推：1。
- 从相对稳定推断的 `derived_value_guard: r_i==T` 动态提取后续排序偏好：`7`；后续精确 `r'=T` 最高，含同一派生值分裂的候选其次，其余按原复杂度排序。
- 迁移失败后只反推终止观察边之前最近的无 KSI 下行边；更早无下行边逐条保留 `r'=r` 假设。

## 详细审计工作簿

Excel 只保留“循环边使用”工作表；每行同时展示本循环候选、最简交集或联合拟合、候选生成结果、
相对稳定推断、迁移结果、首个反例、反推状态与反推候选假设。并列候选采用横向编号排列。
- 本次未请求 Excel 审计工作簿；如需生成，显式提供 `--workbook <path>`。

## 完整性与可读性复核

- 边组数：52；循环数：23；变体数：37；循环—边使用数：90。
- 已断言：全部边组进入本摘要表；完整候选树与证据保留在 JSON。本次没有请求 Excel。
- 可读性：摘要表使用固定布局 HTML、`colgroup` 固定列宽；消息对在 `/` 后换行，状态边与
  公式独立换行。
