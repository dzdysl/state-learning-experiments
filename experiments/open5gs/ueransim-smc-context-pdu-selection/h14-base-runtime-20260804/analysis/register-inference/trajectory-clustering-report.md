# 循环轨迹相似度与聚类

本报告只消费已完成的寄存器推断 `candidates.json`；不参与候选推断。比较对象为同一 `cycle_id + sequence_line` 的有序直接区域样本集合。

## 输入与边界

- 输入：<span style="overflow-wrap:anywhere">D:\<wbr>state-learning-lab\<wbr>projects\<wbr>state-learning-experiments\<wbr>experiments\<wbr>open5gs\<wbr>ueransim-smc-context-pdu-selection\<wbr>h14-base-runtime-20260804\<wbr>analysis\<wbr>register-inference\<wbr>candidates.json</span>
- 提取轨迹：86；低辨别力排除：38；实际聚类：48（相对稳定推断 32、假设性候选 16）。
- 迁移成功轨迹：总计 6，参与聚类 4，未参与 2。
- 以 R3–R9 为七点基本周期；R10 仅用于 R3 同相位严格核验或缺失 `i` 补齐，第二周期其余点是模式补齐，不是 Open5GS 实测数据。
- 所有比较按完全相同的 `input/output/isInitMsg` 切片；`s` 不进入距离，跨切片不自动匹配。

## 自动聚类

### 相对稳定推断内部

- `authenticationResponse/<br>securityModeCommand/<br>not_applicable`：k=4：`E0019:S036:L21`、<wbr>`E0019:S036:L23`、<wbr>`E0163:S036:L21`、<wbr>`E0163:S036:L22` ｜<br>`E0019:S036:L22`、<wbr>`E0019:S036:L24`、<wbr>`E0163:S036:L23`、<wbr>`E0163:S036:L24` ｜<br>`E0019:S002:L1`、<wbr>`E0127:S004:L4`、<wbr>`E0163:S006:L8`、<wbr>`E0163:S012:L14`、<wbr>`E0163:S022:L19` ｜<br>`E0019:S002:L2`、<wbr>`E0019:S009:L12`、<wbr>`E0019:S017:L17`、<wbr>`E0019:F002:L31`、<wbr>`E0127:S004:L5`、<wbr>`E0163:S006:L9`、<wbr>`E0163:S012:L15`、<wbr>`E0163:S022:L20`；相对稳定推断被进一步细分。
- `registrationRequest/<br>authenticationRequest/<br>0`：k=1：`E0013:L001:L32`、<wbr>`E0037:S002:L1`、<wbr>`E0121:L003:L34`、<wbr>`E0157:L005:L36`、<wbr>`E0169:S006:L8`、<wbr>`E0205:S004:L4`；相对稳定推断未被进一步细分。
- `registrationRequestGUTI/<br>authenticationRequest/<br>0`：k=3：`E0014:L002:L33` ｜<br>`E0098:S017:L17` ｜<br>`E0038:S002:L2`、<wbr>`E0170:S006:L9`、<wbr>`E0206:S004:L5`；相对稳定推断被进一步细分。
### 假设性候选内部

- `registrationRequest/<br>authenticationRequest/<br>0`：k=1：`E0073:S036:L21`、<wbr>`E0073:S036:L23`。
- `registrationRequest/<br>authenticationRequest/<br>1`：k=3：`E0085:S017:L17` ｜<br>`E0145:S036:L21`、<wbr>`E0145:S036:L22` ｜<br>`E0133:S003:L3`、<wbr>`E0133:F001:L30`、<wbr>`E0145:S005:L6`、<wbr>`E0145:S012:L14`、<wbr>`E0145:S022:L19`。
- `registrationRequestGUTI/<br>authenticationRequest/<br>0`：k=1：`E0050:S009:L12`、<wbr>`E0050:F002:L31`。
- `registrationRequestGUTI/<br>authenticationRequest/<br>1`：k=2：`E0146:S012:L15`、<wbr>`E0146:S022:L20` ｜<br>`E0146:S036:L23`、<wbr>`E0146:S036:L24`。
### 相对稳定推断与假设性候选联合

- `registrationRequest/<br>authenticationRequest/<br>0`：k=2：`E0073:S036:L21`、<wbr>`E0073:S036:L23` ｜<br>`E0013:L001:L32`、<wbr>`E0037:S002:L1`、<wbr>`E0121:L003:L34`、<wbr>`E0157:L005:L36`、<wbr>`E0169:S006:L8`、<wbr>`E0205:S004:L4`。
- `registrationRequest/<br>authenticationRequest/<br>1`：k=3：`E0085:S017:L17` ｜<br>`E0145:S036:L21`、<wbr>`E0145:S036:L22` ｜<br>`E0133:S003:L3`、<wbr>`E0133:F001:L30`、<wbr>`E0145:S005:L6`、<wbr>`E0145:S012:L14`、<wbr>`E0145:S022:L19`。
- `registrationRequestGUTI/<br>authenticationRequest/<br>0`：k=3：`E0014:L002:L33` ｜<br>`E0098:S017:L17` ｜<br>`E0038:S002:L2`、<wbr>`E0170:S006:L9`、<wbr>`E0206:S004:L5`、<wbr>`E0050:S009:L12`、<wbr>`E0050:F002:L31`。
- `registrationRequestGUTI/<br>authenticationRequest/<br>1`：k=2：`E0146:S012:L15`、<wbr>`E0146:S022:L20` ｜<br>`E0146:S036:L23`、<wbr>`E0146:S036:L24`。

## 迁移成功一致性

- `E0050:S009:L12`：一致（`E0038:S002:L2`、<wbr>`E0170:S006:L9`、<wbr>`E0206:S004:L5`、<wbr>`E0050:S009:L12`、<wbr>`E0050:F002:L31`）；`s=0`；匹配相对稳定推断组 3。
- `E0050:F002:L31`：一致（`E0038:S002:L2`、<wbr>`E0170:S006:L9`、<wbr>`E0206:S004:L5`、<wbr>`E0050:S009:L12`、<wbr>`E0050:F002:L31`）；`s=0`；匹配相对稳定推断组 3。
- `E0073:S036:L21`：聚类不一致（`E0073:S036:L21`、<wbr>`E0073:S036:L23`）；`s=0`；匹配相对稳定推断组 2。
- `E0073:S036:L23`：聚类不一致（`E0073:S036:L21`、<wbr>`E0073:S036:L23`）；`s=0`；匹配相对稳定推断组 2。

## 低辨别力排除

- 共 38 条：相对稳定推断 25 条，假设性候选 13 条；它们只作为灰色背景轨迹，不进入距离矩阵、层次聚类、簇数选择或迁移一致性。
- 相对稳定推断：`E0019:S017:L16`、<wbr>`E0019:S018:L18`、<wbr>`E0019:S037:L25`、<wbr>`E0019:S037:L26`、<wbr>`E0019:S037:L27`、<wbr>`E0019:S037:L28`、<wbr>`E0019:S039:L29`、<wbr>`E0097:S017:L16`、<wbr>`E0103:S010:L13`、<wbr>`E0103:S037:L25`、<wbr>`E0103:S037:L26`、<wbr>`E0103:S037:L27`、<wbr>`E0103:S037:L28`、<wbr>`E0103:S039:L29`、<wbr>`E0109:S037:L25`、<wbr>`E0109:S037:L26`、<wbr>`E0110:S037:L27`、<wbr>`E0110:S037:L28`、<wbr>`E0122:L004:L35`、<wbr>`E0127:S037:L25`、<wbr>`E0127:S037:L26`、<wbr>`E0127:S037:L27`、<wbr>`E0127:S037:L28`、<wbr>`E0127:S039:L29`、<wbr>`E0158:L006:L37`。
- 假设性候选：`E0001:S018:L18`、<wbr>`E0085:S010:L13`、<wbr>`E0085:S017:L16`、<wbr>`E0085:S037:L25`、<wbr>`E0085:S037:L26`、<wbr>`E0085:S037:L27`、<wbr>`E0085:S037:L28`、<wbr>`E0085:S039:L29`、<wbr>`E0133:S039:L29`、<wbr>`E0146:S005:L7`、<wbr>`E0181:S039:L29`、<wbr>`E0193:S037:L25`、<wbr>`E0193:S037:L27`。
- 迁移成功但退出聚类统计：`E0193:S037:L25`、<wbr>`E0193:S037:L27`。

## 轨迹证据

### `E0001:S018:L18`

- EID：`E0001`；`src/dst`：`s0/s1`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (0,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0013:L001:L32`

- EID：`E0013`；`src/dst`：`s1/s1`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=0}[ngksi_uplink=7],3)；<wbr>R4 (3,{isInitMsg=0}[ngksi_uplink=7],4)；<wbr>R5 (4,{isInitMsg=0}[ngksi_uplink=7],5)；<wbr>R6 (5,{isInitMsg=0}[ngksi_uplink=7],6)；<wbr>R7 (6,{isInitMsg=0}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R10 (2,{isInitMsg=0}[ngksi_uplink=7],3)。
### `E0014:L002:L33`

- EID：`E0014`；`src/dst`：`s1/s1`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=0}[ngksi_uplink=7],3)；<wbr>R4 (3,{isInitMsg=0}[ngksi_uplink=7],4)；<wbr>R5 (4,{isInitMsg=0}[ngksi_uplink=7],5)；<wbr>R6 (5,{isInitMsg=0}[ngksi_uplink=7],6)；<wbr>R7 (6,{isInitMsg=0}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R10 (2,{isInitMsg=0}[ngksi_uplink=7],3)。
### `E0019:S002:L1`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (2,∅,2)；<wbr>R4 (3,∅,3)；<wbr>R5 (4,∅,4)；<wbr>R6 (5,∅,5)；<wbr>R7 (6,∅,6)；<wbr>R8 (0,∅,0)；<wbr>R9 (1,∅,1)；<wbr>R10 (2,∅,2)。
### `E0019:S002:L2`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (2,∅,2)；<wbr>R4 (3,∅,3)；<wbr>R5 (4,∅,4)；<wbr>R6 (5,∅,5)；<wbr>R7 (6,∅,6)；<wbr>R8 (0,∅,0)；<wbr>R9 (1,∅,1)；<wbr>R10 (2,∅,2)。
### `E0019:S009:L12`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (2,∅,2)；<wbr>R4 (3,∅,3)；<wbr>R5 (4,∅,4)；<wbr>R6 (5,∅,5)；<wbr>R7 (6,∅,6)；<wbr>R8 (0,∅,0)；<wbr>R9 (1,∅,1)；<wbr>R10 (2,∅,2)。
### `E0019:S017:L16`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (1,∅,1)；<wbr>R4 (1,∅,1)；<wbr>R5 (1,∅,1)；<wbr>R6 (1,∅,1)；<wbr>R7 (1,∅,1)；<wbr>R8 (1,∅,1)；<wbr>R9 (1,∅,1)；<wbr>R10 (1,∅,1)。
### `E0019:S017:L17`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (2,∅,2)；<wbr>R4 (3,∅,3)；<wbr>R5 (4,∅,4)；<wbr>R6 (5,∅,5)；<wbr>R7 (6,∅,6)；<wbr>R8 (0,∅,0)；<wbr>R9 (1,∅,1)；<wbr>R10 (2,∅,2)。
### `E0019:S018:L18`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (0,∅,0)；<wbr>R4 (0,∅,0)；<wbr>R5 (0,∅,0)；<wbr>R6 (0,∅,0)；<wbr>R7 (0,∅,0)；<wbr>R8 (0,∅,0)；<wbr>R9 (0,∅,0)；<wbr>R10 (0,∅,0)。
### `E0019:S036:L21`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (4,∅,4)；<wbr>R4 (6,∅,6)；<wbr>R5 (1,∅,1)；<wbr>R6 (3,∅,3)；<wbr>R7 (5,∅,5)；<wbr>R8 (0,∅,0)；<wbr>R9 (2,∅,2)；<wbr>R10 (4,∅,4)。
### `E0019:S036:L22`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (4,∅,4)；<wbr>R4 (6,∅,6)；<wbr>R5 (1,∅,1)；<wbr>R6 (3,∅,3)；<wbr>R7 (5,∅,5)；<wbr>R8 (0,∅,0)；<wbr>R9 (2,∅,2)；<wbr>R10 (4,∅,4)。
### `E0019:S036:L23`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (4,∅,4)；<wbr>R4 (6,∅,6)；<wbr>R5 (1,∅,1)；<wbr>R6 (3,∅,3)；<wbr>R7 (5,∅,5)；<wbr>R8 (0,∅,0)；<wbr>R9 (2,∅,2)；<wbr>R10 (4,∅,4)。
### `E0019:S036:L24`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (4,∅,4)；<wbr>R4 (6,∅,6)；<wbr>R5 (1,∅,1)；<wbr>R6 (3,∅,3)；<wbr>R7 (5,∅,5)；<wbr>R8 (0,∅,0)；<wbr>R9 (2,∅,2)；<wbr>R10 (4,∅,4)。
### `E0019:S037:L25`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (2,∅,2)；<wbr>R4 (2,∅,2)；<wbr>R5 (2,∅,2)；<wbr>R6 (2,∅,2)；<wbr>R7 (2,∅,2)；<wbr>R8 (2,∅,2)；<wbr>R9 (2,∅,2)；<wbr>R10 (2,∅,2)。
### `E0019:S037:L26`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (2,∅,2)；<wbr>R4 (2,∅,2)；<wbr>R5 (2,∅,2)；<wbr>R6 (2,∅,2)；<wbr>R7 (2,∅,2)；<wbr>R8 (2,∅,2)；<wbr>R9 (2,∅,2)；<wbr>R10 (2,∅,2)。
### `E0019:S037:L27`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (2,∅,2)；<wbr>R4 (2,∅,2)；<wbr>R5 (2,∅,2)；<wbr>R6 (2,∅,2)；<wbr>R7 (2,∅,2)；<wbr>R8 (2,∅,2)；<wbr>R9 (2,∅,2)；<wbr>R10 (2,∅,2)。
### `E0019:S037:L28`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (2,∅,2)；<wbr>R4 (2,∅,2)；<wbr>R5 (2,∅,2)；<wbr>R6 (2,∅,2)；<wbr>R7 (2,∅,2)；<wbr>R8 (2,∅,2)；<wbr>R9 (2,∅,2)；<wbr>R10 (2,∅,2)。
### `E0019:S039:L29`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (0,∅,0)；<wbr>R4 (0,∅,0)；<wbr>R5 (0,∅,0)；<wbr>R6 (0,∅,0)；<wbr>R7 (0,∅,0)；<wbr>R8 (0,∅,0)；<wbr>R9 (0,∅,0)；<wbr>R10 (0,∅,0)。
### `E0019:F002:L31`

- EID：`E0019`；`src/dst`：`s1/s3`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (2,∅,2)；<wbr>R4 (3,∅,3)；<wbr>R5 (4,∅,4)；<wbr>R6 (5,∅,5)；<wbr>R7 (6,∅,6)；<wbr>R8 (0,∅,0)；<wbr>R9 (1,∅,1)；<wbr>R10 (2,∅,2)。
### `E0037:S002:L1`

- EID：`E0037`；`src/dst`：`s3/s1`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=0}[ngksi_uplink=7],3)；<wbr>R4 (3,{isInitMsg=0}[ngksi_uplink=7],4)；<wbr>R5 (4,{isInitMsg=0}[ngksi_uplink=7],5)；<wbr>R6 (5,{isInitMsg=0}[ngksi_uplink=7],6)；<wbr>R7 (6,{isInitMsg=0}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R10 (2,{isInitMsg=0}[ngksi_uplink=7],3)。
### `E0038:S002:L2`

- EID：`E0038`；`src/dst`：`s3/s1`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=0}[ngksi_uplink=2],3)；<wbr>R4 (3,{isInitMsg=0}[ngksi_uplink=3],4)；<wbr>R5 (4,{isInitMsg=0}[ngksi_uplink=4],5)；<wbr>R6 (5,{isInitMsg=0}[ngksi_uplink=5],6)；<wbr>R7 (6,{isInitMsg=0}[ngksi_uplink=6],0)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=1],2)；<wbr>R10 (2,{isInitMsg=0}[ngksi_uplink=2],3)。
### `E0050:S009:L12`

- EID：`E0050`；`src/dst`：`s4/s1`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=0}[ngksi_uplink=2],3)；<wbr>R4 (3,{isInitMsg=0}[ngksi_uplink=3],4)；<wbr>R5 (4,{isInitMsg=0}[ngksi_uplink=4],5)；<wbr>R6 (5,{isInitMsg=0}[ngksi_uplink=5],6)；<wbr>R7 (6,{isInitMsg=0}[ngksi_uplink=6],0)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=1],2)；<wbr>R10 (2,{isInitMsg=0}[ngksi_uplink=2],3)。
### `E0050:F002:L31`

- EID：`E0050`；`src/dst`：`s4/s1`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=0}[ngksi_uplink=2],3)；<wbr>R4 (3,{isInitMsg=0}[ngksi_uplink=3],4)；<wbr>R5 (4,{isInitMsg=0}[ngksi_uplink=4],5)；<wbr>R6 (5,{isInitMsg=0}[ngksi_uplink=5],6)；<wbr>R7 (6,{isInitMsg=0}[ngksi_uplink=6],0)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=1],2)；<wbr>R10 (2,{isInitMsg=0}[ngksi_uplink=2],3)。
### `E0073:S036:L21`

- EID：`E0073`；`src/dst`：`s6/s1`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (5,{isInitMsg=0}[ngksi_uplink=7],6)；<wbr>R4 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R5 (2,{isInitMsg=0}[ngksi_uplink=7],3)；<wbr>R6 (4,{isInitMsg=0}[ngksi_uplink=7],5)；<wbr>R7 (6,{isInitMsg=0}[ngksi_uplink=7],0)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R9 (3,{isInitMsg=0}[ngksi_uplink=7],4)；<wbr>R10 (5,{isInitMsg=0}[ngksi_uplink=7],6)。
### `E0073:S036:L23`

- EID：`E0073`；`src/dst`：`s6/s1`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (5,{isInitMsg=0}[ngksi_uplink=7],6)；<wbr>R4 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R5 (2,{isInitMsg=0}[ngksi_uplink=7],3)；<wbr>R6 (4,{isInitMsg=0}[ngksi_uplink=7],5)；<wbr>R7 (6,{isInitMsg=0}[ngksi_uplink=7],0)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R9 (3,{isInitMsg=0}[ngksi_uplink=7],4)；<wbr>R10 (5,{isInitMsg=0}[ngksi_uplink=7],6)。
### `E0085:S010:L13`

- EID：`E0085`；`src/dst`：`s7/s8`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (0,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0085:S017:L16`

- EID：`E0085`；`src/dst`：`s7/s8`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (1,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0085:S017:L17`

- EID：`E0085`；`src/dst`：`s7/s8`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (3,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (4,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (5,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (6,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0085:S037:L25`

- EID：`E0085`；`src/dst`：`s7/s8`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0085:S037:L26`

- EID：`E0085`；`src/dst`：`s7/s8`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0085:S037:L27`

- EID：`E0085`；`src/dst`：`s7/s8`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0085:S037:L28`

- EID：`E0085`；`src/dst`：`s7/s8`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0085:S039:L29`

- EID：`E0085`；`src/dst`：`s7/s8`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (0,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (0,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0097:S017:L16`

- EID：`E0097`；`src/dst`：`s8/s1`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R4 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R5 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R6 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R9 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R10 (0,{isInitMsg=0}[ngksi_uplink=7],1)。
### `E0098:S017:L17`

- EID：`E0098`；`src/dst`：`s8/s1`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=0}[ngksi_uplink=2],3)；<wbr>R4 (0,{isInitMsg=0}[ngksi_uplink=3],4)；<wbr>R5 (0,{isInitMsg=0}[ngksi_uplink=4],5)；<wbr>R6 (0,{isInitMsg=0}[ngksi_uplink=5],6)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=6],0)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R9 (0,{isInitMsg=0}[ngksi_uplink=1],2)；<wbr>R10 (0,{isInitMsg=0}[ngksi_uplink=2],3)。
### `E0103:S010:L13`

- EID：`E0103`；`src/dst`：`s8/s9`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (0,∅,0)；<wbr>R4 (0,∅,0)；<wbr>R5 (0,∅,0)；<wbr>R6 (0,∅,0)；<wbr>R7 (0,∅,0)；<wbr>R8 (0,∅,0)；<wbr>R9 (0,∅,0)；<wbr>R10 (0,∅,0)。
### `E0103:S037:L25`

- EID：`E0103`；`src/dst`：`s8/s9`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (0,∅,0)；<wbr>R4 (0,∅,0)；<wbr>R5 (0,∅,0)；<wbr>R6 (0,∅,0)；<wbr>R7 (0,∅,0)；<wbr>R8 (0,∅,0)；<wbr>R9 (0,∅,0)；<wbr>R10 (0,∅,0)。
### `E0103:S037:L26`

- EID：`E0103`；`src/dst`：`s8/s9`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (0,∅,0)；<wbr>R4 (0,∅,0)；<wbr>R5 (0,∅,0)；<wbr>R6 (0,∅,0)；<wbr>R7 (0,∅,0)；<wbr>R8 (0,∅,0)；<wbr>R9 (0,∅,0)；<wbr>R10 (0,∅,0)。
### `E0103:S037:L27`

- EID：`E0103`；`src/dst`：`s8/s9`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (0,∅,0)；<wbr>R4 (0,∅,0)；<wbr>R5 (0,∅,0)；<wbr>R6 (0,∅,0)；<wbr>R7 (0,∅,0)；<wbr>R8 (0,∅,0)；<wbr>R9 (0,∅,0)；<wbr>R10 (0,∅,0)。
### `E0103:S037:L28`

- EID：`E0103`；`src/dst`：`s8/s9`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (0,∅,0)；<wbr>R4 (0,∅,0)；<wbr>R5 (0,∅,0)；<wbr>R6 (0,∅,0)；<wbr>R7 (0,∅,0)；<wbr>R8 (0,∅,0)；<wbr>R9 (0,∅,0)；<wbr>R10 (0,∅,0)。
### `E0103:S039:L29`

- EID：`E0103`；`src/dst`：`s8/s9`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (0,∅,0)；<wbr>R4 (0,∅,0)；<wbr>R5 (0,∅,0)；<wbr>R6 (0,∅,0)；<wbr>R7 (0,∅,0)；<wbr>R8 (0,∅,0)；<wbr>R9 (0,∅,0)；<wbr>R10 (0,∅,0)。
### `E0109:S037:L25`

- EID：`E0109`；`src/dst`：`s9/s10`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R4 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R5 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R6 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R9 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R10 (0,{isInitMsg=0}[ngksi_uplink=7],1)。
### `E0109:S037:L26`

- EID：`E0109`；`src/dst`：`s9/s10`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R4 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R5 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R6 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R9 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R10 (0,{isInitMsg=0}[ngksi_uplink=7],1)。
### `E0110:S037:L27`

- EID：`E0110`；`src/dst`：`s9/s10`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R4 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R5 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R6 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R9 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R10 (0,{isInitMsg=0}[ngksi_uplink=0],1)。
### `E0110:S037:L28`

- EID：`E0110`；`src/dst`：`s9/s10`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R4 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R5 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R6 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R8 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R9 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R10 (0,{isInitMsg=0}[ngksi_uplink=0],1)。
### `E0121:L003:L34`

- EID：`E0121`；`src/dst`：`s10/s10`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (3,{isInitMsg=0}[ngksi_uplink=7],4)；<wbr>R4 (4,{isInitMsg=0}[ngksi_uplink=7],5)；<wbr>R5 (5,{isInitMsg=0}[ngksi_uplink=7],6)；<wbr>R6 (6,{isInitMsg=0}[ngksi_uplink=7],0)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R9 (2,{isInitMsg=0}[ngksi_uplink=7],3)；<wbr>R10 (3,{isInitMsg=0}[ngksi_uplink=7],4)。
### `E0122:L004:L35`

- EID：`E0122`；`src/dst`：`s10/s10`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R4 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R5 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R6 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R7 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R10 (1,{isInitMsg=0}[ngksi_uplink=0],1)。
### `E0127:S004:L4`

- EID：`E0127`；`src/dst`：`s10/s17`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (3,∅,3)；<wbr>R4 (4,∅,4)；<wbr>R5 (5,∅,5)；<wbr>R6 (6,∅,6)；<wbr>R7 (0,∅,0)；<wbr>R8 (1,∅,1)；<wbr>R9 (2,∅,2)；<wbr>R10 (3,∅,3)。
### `E0127:S004:L5`

- EID：`E0127`；`src/dst`：`s10/s17`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (3,∅,3)；<wbr>R4 (4,∅,4)；<wbr>R5 (5,∅,5)；<wbr>R6 (6,∅,6)；<wbr>R7 (0,∅,0)；<wbr>R8 (1,∅,1)；<wbr>R9 (2,∅,2)；<wbr>R10 (3,∅,3)。
### `E0127:S037:L25`

- EID：`E0127`；`src/dst`：`s10/s17`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (1,∅,1)；<wbr>R4 (1,∅,1)；<wbr>R5 (1,∅,1)；<wbr>R6 (1,∅,1)；<wbr>R7 (1,∅,1)；<wbr>R8 (1,∅,1)；<wbr>R9 (1,∅,1)；<wbr>R10 (1,∅,1)。
### `E0127:S037:L26`

- EID：`E0127`；`src/dst`：`s10/s17`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (1,∅,1)；<wbr>R4 (1,∅,1)；<wbr>R5 (1,∅,1)；<wbr>R6 (1,∅,1)；<wbr>R7 (1,∅,1)；<wbr>R8 (1,∅,1)；<wbr>R9 (1,∅,1)；<wbr>R10 (1,∅,1)。
### `E0127:S037:L27`

- EID：`E0127`；`src/dst`：`s10/s17`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (1,∅,1)；<wbr>R4 (1,∅,1)；<wbr>R5 (1,∅,1)；<wbr>R6 (1,∅,1)；<wbr>R7 (1,∅,1)；<wbr>R8 (1,∅,1)；<wbr>R9 (1,∅,1)；<wbr>R10 (1,∅,1)。
### `E0127:S037:L28`

- EID：`E0127`；`src/dst`：`s10/s17`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (1,∅,1)；<wbr>R4 (1,∅,1)；<wbr>R5 (1,∅,1)；<wbr>R6 (1,∅,1)；<wbr>R7 (1,∅,1)；<wbr>R8 (1,∅,1)；<wbr>R9 (1,∅,1)；<wbr>R10 (1,∅,1)。
### `E0127:S039:L29`

- EID：`E0127`；`src/dst`：`s10/s17`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (1,∅,1)；<wbr>R4 (1,∅,1)；<wbr>R5 (1,∅,1)；<wbr>R6 (1,∅,1)；<wbr>R7 (1,∅,1)；<wbr>R8 (1,∅,1)；<wbr>R9 (1,∅,1)；<wbr>R10 (1,∅,1)。
### `E0133:S003:L3`

- EID：`E0133`；`src/dst`：`s11/s10`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (3,{isInitMsg=1}[ngksi_uplink=7],4)；<wbr>R4 (4,{isInitMsg=1}[ngksi_uplink=7],5)；<wbr>R5 (5,{isInitMsg=1}[ngksi_uplink=7],6)；<wbr>R6 (6,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R8 (1,{isInitMsg=1}[ngksi_uplink=7],2)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=7],3)；<wbr>R10 (3,{isInitMsg=1}[ngksi_uplink=7],4)。
### `E0133:S039:L29`

- EID：`E0133`；`src/dst`：`s11/s10`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R4 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R5 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R6 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R7 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R9 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R10 (0,{isInitMsg=1}[ngksi_uplink=7],1)。
### `E0133:F001:L30`

- EID：`E0133`；`src/dst`：`s11/s10`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (3,{isInitMsg=1}[ngksi_uplink=7],4)；<wbr>R4 (4,{isInitMsg=1}[ngksi_uplink=7],5)；<wbr>R5 (5,{isInitMsg=1}[ngksi_uplink=7],6)；<wbr>R6 (6,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R8 (1,{isInitMsg=1}[ngksi_uplink=7],2)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=7],3)；<wbr>R10 (3,{isInitMsg=1}[ngksi_uplink=7],4)。
### `E0145:S005:L6`

- EID：`E0145`；`src/dst`：`s12/s13`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=7],3)；<wbr>R4 (3,{isInitMsg=1}[ngksi_uplink=7],4)；<wbr>R5 (4,{isInitMsg=1}[ngksi_uplink=7],5)；<wbr>R6 (5,{isInitMsg=1}[ngksi_uplink=7],6)；<wbr>R7 (6,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R9 (1,{isInitMsg=1}[ngksi_uplink=7],2)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=7],3)。
### `E0145:S012:L14`

- EID：`E0145`；`src/dst`：`s12/s13`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=7],3)；<wbr>R4 (3,{isInitMsg=1}[ngksi_uplink=7],4)；<wbr>R5 (4,{isInitMsg=1}[ngksi_uplink=7],5)；<wbr>R6 (5,{isInitMsg=1}[ngksi_uplink=7],6)；<wbr>R7 (6,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R9 (1,{isInitMsg=1}[ngksi_uplink=7],2)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=7],3)。
### `E0145:S022:L19`

- EID：`E0145`；`src/dst`：`s12/s13`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=7],3)；<wbr>R4 (3,{isInitMsg=1}[ngksi_uplink=7],4)；<wbr>R5 (4,{isInitMsg=1}[ngksi_uplink=7],5)；<wbr>R6 (5,{isInitMsg=1}[ngksi_uplink=7],6)；<wbr>R7 (6,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R9 (1,{isInitMsg=1}[ngksi_uplink=7],2)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=7],3)。
### `E0145:S036:L21`

- EID：`E0145`；`src/dst`：`s12/s13`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (4,{isInitMsg=1}[ngksi_uplink=7],5)；<wbr>R4 (6,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (1,{isInitMsg=1}[ngksi_uplink=7],2)；<wbr>R6 (3,{isInitMsg=1}[ngksi_uplink=7],4)；<wbr>R7 (5,{isInitMsg=1}[ngksi_uplink=7],6)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=7],3)；<wbr>R10 (4,{isInitMsg=1}[ngksi_uplink=7],5)。
### `E0145:S036:L22`

- EID：`E0145`；`src/dst`：`s12/s13`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (4,{isInitMsg=1}[ngksi_uplink=7],5)；<wbr>R4 (6,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (1,{isInitMsg=1}[ngksi_uplink=7],2)；<wbr>R6 (3,{isInitMsg=1}[ngksi_uplink=7],4)；<wbr>R7 (5,{isInitMsg=1}[ngksi_uplink=7],6)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=7],1)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=7],3)；<wbr>R10 (4,{isInitMsg=1}[ngksi_uplink=7],5)。
### `E0146:S005:L7`

- EID：`E0146`；`src/dst`：`s12/s13`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (1,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R4 (1,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R5 (1,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R6 (1,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R7 (1,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R8 (1,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R9 (1,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R10 (1,{isInitMsg=1}[ngksi_uplink=0],1)。
### `E0146:S012:L15`

- EID：`E0146`；`src/dst`：`s12/s13`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=2],3)；<wbr>R4 (3,{isInitMsg=1}[ngksi_uplink=3],4)；<wbr>R5 (4,{isInitMsg=1}[ngksi_uplink=4],5)；<wbr>R6 (5,{isInitMsg=1}[ngksi_uplink=5],6)；<wbr>R7 (6,{isInitMsg=1}[ngksi_uplink=6],0)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R9 (1,{isInitMsg=1}[ngksi_uplink=1],2)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=2],3)。
### `E0146:S022:L20`

- EID：`E0146`；`src/dst`：`s12/s13`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (2,{isInitMsg=1}[ngksi_uplink=2],3)；<wbr>R4 (3,{isInitMsg=1}[ngksi_uplink=3],4)；<wbr>R5 (4,{isInitMsg=1}[ngksi_uplink=4],5)；<wbr>R6 (5,{isInitMsg=1}[ngksi_uplink=5],6)；<wbr>R7 (6,{isInitMsg=1}[ngksi_uplink=6],0)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R9 (1,{isInitMsg=1}[ngksi_uplink=1],2)；<wbr>R10 (2,{isInitMsg=1}[ngksi_uplink=2],3)。
### `E0146:S036:L23`

- EID：`E0146`；`src/dst`：`s12/s13`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (4,{isInitMsg=1}[ngksi_uplink=4],5)；<wbr>R4 (6,{isInitMsg=1}[ngksi_uplink=6],0)；<wbr>R5 (1,{isInitMsg=1}[ngksi_uplink=1],2)；<wbr>R6 (3,{isInitMsg=1}[ngksi_uplink=3],4)；<wbr>R7 (5,{isInitMsg=1}[ngksi_uplink=5],6)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=2],3)；<wbr>R10 (4,{isInitMsg=1}[ngksi_uplink=4],5)。
### `E0146:S036:L24`

- EID：`E0146`；`src/dst`：`s12/s13`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (4,{isInitMsg=1}[ngksi_uplink=4],5)；<wbr>R4 (6,{isInitMsg=1}[ngksi_uplink=6],0)；<wbr>R5 (1,{isInitMsg=1}[ngksi_uplink=1],2)；<wbr>R6 (3,{isInitMsg=1}[ngksi_uplink=3],4)；<wbr>R7 (5,{isInitMsg=1}[ngksi_uplink=5],6)；<wbr>R8 (0,{isInitMsg=1}[ngksi_uplink=0],1)；<wbr>R9 (2,{isInitMsg=1}[ngksi_uplink=2],3)；<wbr>R10 (4,{isInitMsg=1}[ngksi_uplink=4],5)。
### `E0157:L005:L36`

- EID：`E0157`；`src/dst`：`s13/s13`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (3,{isInitMsg=0}[ngksi_uplink=7],4)；<wbr>R4 (4,{isInitMsg=0}[ngksi_uplink=7],5)；<wbr>R5 (5,{isInitMsg=0}[ngksi_uplink=7],6)；<wbr>R6 (6,{isInitMsg=0}[ngksi_uplink=7],0)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R9 (2,{isInitMsg=0}[ngksi_uplink=7],3)；<wbr>R10 (3,{isInitMsg=0}[ngksi_uplink=7],4)。
### `E0158:L006:L37`

- EID：`E0158`；`src/dst`：`s13/s13`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R4 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R5 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R6 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R7 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R10 (1,{isInitMsg=0}[ngksi_uplink=0],1)。
### `E0163:S006:L8`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (3,∅,3)；<wbr>R4 (4,∅,4)；<wbr>R5 (5,∅,5)；<wbr>R6 (6,∅,6)；<wbr>R7 (0,∅,0)；<wbr>R8 (1,∅,1)；<wbr>R9 (2,∅,2)；<wbr>R10 (3,∅,3)。
### `E0163:S006:L9`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (3,∅,3)；<wbr>R4 (4,∅,4)；<wbr>R5 (5,∅,5)；<wbr>R6 (6,∅,6)；<wbr>R7 (0,∅,0)；<wbr>R8 (1,∅,1)；<wbr>R9 (2,∅,2)；<wbr>R10 (3,∅,3)。
### `E0163:S012:L14`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (3,∅,3)；<wbr>R4 (4,∅,4)；<wbr>R5 (5,∅,5)；<wbr>R6 (6,∅,6)；<wbr>R7 (0,∅,0)；<wbr>R8 (1,∅,1)；<wbr>R9 (2,∅,2)；<wbr>R10 (3,∅,3)。
### `E0163:S012:L15`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (3,∅,3)；<wbr>R4 (4,∅,4)；<wbr>R5 (5,∅,5)；<wbr>R6 (6,∅,6)；<wbr>R7 (0,∅,0)；<wbr>R8 (1,∅,1)；<wbr>R9 (2,∅,2)；<wbr>R10 (3,∅,3)。
### `E0163:S022:L19`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (3,∅,3)；<wbr>R4 (4,∅,4)；<wbr>R5 (5,∅,5)；<wbr>R6 (6,∅,6)；<wbr>R7 (0,∅,0)；<wbr>R8 (1,∅,1)；<wbr>R9 (2,∅,2)；<wbr>R10 (3,∅,3)。
### `E0163:S022:L20`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (3,∅,3)；<wbr>R4 (4,∅,4)；<wbr>R5 (5,∅,5)；<wbr>R6 (6,∅,6)；<wbr>R7 (0,∅,0)；<wbr>R8 (1,∅,1)；<wbr>R9 (2,∅,2)；<wbr>R10 (3,∅,3)。
### `E0163:S036:L21`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (5,∅,5)；<wbr>R4 (0,∅,0)；<wbr>R5 (2,∅,2)；<wbr>R6 (4,∅,4)；<wbr>R7 (6,∅,6)；<wbr>R8 (1,∅,1)；<wbr>R9 (3,∅,3)；<wbr>R10 (5,∅,5)。
### `E0163:S036:L22`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (5,∅,5)；<wbr>R4 (0,∅,0)；<wbr>R5 (2,∅,2)；<wbr>R6 (4,∅,4)；<wbr>R7 (6,∅,6)；<wbr>R8 (1,∅,1)；<wbr>R9 (3,∅,3)；<wbr>R10 (5,∅,5)。
### `E0163:S036:L23`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (5,∅,5)；<wbr>R4 (0,∅,0)；<wbr>R5 (2,∅,2)；<wbr>R6 (4,∅,4)；<wbr>R7 (6,∅,6)；<wbr>R8 (1,∅,1)；<wbr>R9 (3,∅,3)；<wbr>R10 (5,∅,5)。
### `E0163:S036:L24`

- EID：`E0163`；`src/dst`：`s13/s14`；`input/output`：`authenticationResponse/securityModeCommand`。
- 映射观察区域：R3 (5,∅,5)；<wbr>R4 (0,∅,0)；<wbr>R5 (2,∅,2)；<wbr>R6 (4,∅,4)；<wbr>R7 (6,∅,6)；<wbr>R8 (1,∅,1)；<wbr>R9 (3,∅,3)；<wbr>R10 (5,∅,5)。
### `E0169:S006:L8`

- EID：`E0169`；`src/dst`：`s14/s13`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (3,{isInitMsg=0}[ngksi_uplink=7],4)；<wbr>R4 (4,{isInitMsg=0}[ngksi_uplink=7],5)；<wbr>R5 (5,{isInitMsg=0}[ngksi_uplink=7],6)；<wbr>R6 (6,{isInitMsg=0}[ngksi_uplink=7],0)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R9 (2,{isInitMsg=0}[ngksi_uplink=7],3)；<wbr>R10 (3,{isInitMsg=0}[ngksi_uplink=7],4)。
### `E0170:S006:L9`

- EID：`E0170`；`src/dst`：`s14/s13`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (3,{isInitMsg=0}[ngksi_uplink=3],4)；<wbr>R4 (4,{isInitMsg=0}[ngksi_uplink=4],5)；<wbr>R5 (5,{isInitMsg=0}[ngksi_uplink=5],6)；<wbr>R6 (6,{isInitMsg=0}[ngksi_uplink=6],0)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=1],2)；<wbr>R9 (2,{isInitMsg=0}[ngksi_uplink=2],3)；<wbr>R10 (3,{isInitMsg=0}[ngksi_uplink=3],4)。
### `E0181:S039:L29`

- EID：`E0181`；`src/dst`：`s15/s1`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R4 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R5 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R6 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R7 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R8 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R9 (1,{isInitMsg=1}[ngksi_uplink=7],0)；<wbr>R10 (1,{isInitMsg=1}[ngksi_uplink=7],0)。
### `E0193:S037:L25`

- EID：`E0193`；`src/dst`：`s16/s1`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R4 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R5 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R6 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R7 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R10 (1,{isInitMsg=0}[ngksi_uplink=7],2)。
### `E0193:S037:L27`

- EID：`E0193`；`src/dst`：`s16/s1`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R4 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R5 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R6 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R7 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R9 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R10 (1,{isInitMsg=0}[ngksi_uplink=7],2)。
### `E0205:S004:L4`

- EID：`E0205`；`src/dst`：`s17/s10`；`input/output`：`registrationRequest/authenticationRequest`。
- 映射观察区域：R3 (3,{isInitMsg=0}[ngksi_uplink=7],4)；<wbr>R4 (4,{isInitMsg=0}[ngksi_uplink=7],5)；<wbr>R5 (5,{isInitMsg=0}[ngksi_uplink=7],6)；<wbr>R6 (6,{isInitMsg=0}[ngksi_uplink=7],0)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=7],1)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=7],2)；<wbr>R9 (2,{isInitMsg=0}[ngksi_uplink=7],3)；<wbr>R10 (3,{isInitMsg=0}[ngksi_uplink=7],4)。
### `E0206:S004:L5`

- EID：`E0206`；`src/dst`：`s17/s10`；`input/output`：`registrationRequestGUTI/authenticationRequest`。
- 映射观察区域：R3 (3,{isInitMsg=0}[ngksi_uplink=3],4)；<wbr>R4 (4,{isInitMsg=0}[ngksi_uplink=4],5)；<wbr>R5 (5,{isInitMsg=0}[ngksi_uplink=5],6)；<wbr>R6 (6,{isInitMsg=0}[ngksi_uplink=6],0)；<wbr>R7 (0,{isInitMsg=0}[ngksi_uplink=0],1)；<wbr>R8 (1,{isInitMsg=0}[ngksi_uplink=1],2)；<wbr>R9 (2,{isInitMsg=0}[ngksi_uplink=2],3)；<wbr>R10 (3,{isInitMsg=0}[ngksi_uplink=3],4)。

## 阶段性局限

- 不跨 `s` 切片自动匹配簇。
- 聚类仅比较轨迹形状，轨迹形状簇尚不能等同于函数逻辑簇。
- 当前结果保留启发式比较的缺口，留待后续处理。

## 可读性检查

消息对在 `/` 后显式换行；路径、成员 ID 和观察区域分隔处设置可换行点；本报告不使用宽表，距离矩阵无损保存在 JSON。
