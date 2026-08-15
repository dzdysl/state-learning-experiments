# H14 基础环覆盖运行时验证

## 状态

运行器完整执行了 H14 基础组 `repeat-10` 输入的 37 条逻辑序列；完整原始
轨迹中的 1,656 个抽象 I/O 步均与 H14 原始 DOT 的确定性模拟一致。基于同一完整
轨迹的 ngKSI 观察寄存器候选推断已经完成。Markdown 以 H13 四列格式摘要 52 个具体
DOT 边组；完整公式树、循环局部候选、交集、联合拟合、迁移和反推样本保留在 JSON，见
[寄存器推断摘要](analysis/register-inference/summary.md)、
[机器可读候选](analysis/register-inference/candidates.json)与
[边分类叠加图](analysis/register-inference/edge-categories.svg)。
在推断完成后，另以有序直接区域为轨迹完成了独立的循环相似度与聚类后处理；结果见
[轨迹聚类报告](analysis/register-inference/trajectory-clustering-report.md)与
[机器可读聚类结果](analysis/register-inference/trajectory-clusters.json)。
面向阅读的摘要与工作簿将 `unknown/<reason>` 显示为 `unknown`、将
`r_i[ngksi_uplink]` 显示为 `r_i`，并省略输入更新的观察来源括号；完整原始枚举与证据仍保留在 JSON。
该结果不扩展为对未覆盖边、额外短环组或 AMF 内部寄存器的证明。

运行期间的核心网启动/停止不稳定：80 个核心会话均以退出码 134 结束，其中
43 次启动直接遇到 UPF 端口占用。此异常不构成已观察 I/O 与 H14 的反例，但使
本次验证不适合用来评估干净重启、时序或性能。

## 范围

这是 H14 完成记录的独立运行时验证 follow-up，而非原主动学习运行的一部分。
其精确输入与 H14 基础组输入同哈希，运行 ID 为
`20260804T034905Z-2b7f2823`。运行器记录的源树处于 dirty 状态，且实际
Open5GS 二进制没有版本或哈希；因此 H14 行为一致性结论仅依赖冻结的输入、
完整 trace 与 H14 DOT，不把参考源码当作实际部署源码。

## 已确认结论

- `evidence/statelearner_trace.jsonl` 含 37 个会话、1,656 条 I/O 记录；每个会话
  唯一对应 `inputs/base-cycle-repeat10.seq` 的一行。
- 排除 `s2` 后，从 `s0` 在 H14 原始 DOT 上逐输入模拟，37/37 条序列、
  1,656/1,656 步的下行抽象输出完全相同；详见
  [模型核对结果](analysis/model-check.json)。
- `evidence/console.log` 的行 9677–9687 记录 `Primary total: 37`、
  `Primary kept: 37`、`Failed primary ids: []` 与 `Final kept sequences: 37`；
  它确认所有逻辑序列完成。
- `statelearner_trace.cleaned.jsonl` 只保留 498 行，覆盖前 17 条逻辑序列；
  它是派生筛选视图，不能用于本记录的完成度、覆盖率、一致性或寄存器推断。

## 相对稳定推断与前序反推

工具先按 `cycle_id` 分别构建假设性候选的循环局部最简树。精确交集非空时直接采用交集；交集为空时
合并这些循环的全部样本重新拟合。`E0073` 的联合拟合仍无精确候选，因此记录具体边级
`combined_sample_fit_failed`，顶层候选为空，但继续执行相对稳定推断迁移检验与前序反推。

相对稳定推断按完整有效信号上下文、`input` 和 `output` 合并单边区域；不适用已配置信号的 I/O 才只按
输入输出合并。H14 得到 3 个相对稳定推断：

- 无信号、`authenticationResponse/securityModeCommand`：`r'=r`；没有假设性目标。
- `{isInitMsg=0}`、`registrationRequest/authenticationRequest`：已观察 `s=0`
  分支为 `r'=ite(r<6,r+1,0)`，`s=1` 分支未观察。
- `{isInitMsg=0}`、`registrationRequestGUTI/authenticationRequest`：已观察 `s=0`
  分支为 `r'=ite(r_i=7,ite(r<6,r+1,0),ite(r_i<6,r_i+1,0))`，`s=1` 分支未观察。

迁移只在相同 `{s}/input/output` 分组中执行。9 条假设性目标边共有 22 个循环—边分区：4 个迁移成功，
17 个因目标为 `{isInitMsg=1}` 而没有同组相对稳定推断，只有 `E0073/S008` 迁移失败并进入
前序反推。这里不借用 `s=0` 的模型检验 `s=1` 目标。

第三个相对稳定推断首次产生 `derived_value_guard: r_i == 7` 后，工具立即激活动态偏好 `T=7`，作用于
脚本剩余生命周期中新生成的精确候选；既有候选不回排，配置也没有预设 `T`。后续若精确常数 `r'=7`
覆盖全部样本，它排在首位；含 `r_i==7` 分裂的候选其次；其他候选仍按原复杂度排序并全部保留。

完整迁移实例为 `E0073: s6 → s1, registrationRequest/authenticationRequest`，其待迁移相对稳定推断
绑定 `{isInitMsg=0}`，模型树为
`r'=ite(s=1,unknown,ite(r<6,r+1,0))`：

- `S008`、输入行 10（`V01`）、R3–R10：上行 KSI 为 7，前一下行 KSI 为 0，当前下行 KSI 为 0；
  映射观察区域为 `(0,{isInitMsg=0},[ngksi_uplink=7],0)`。代入模型树预测 1，8/8 个
  样本不匹配。
- `S036`、输入行 21/23（`V01/V03`）、R3–R10：上行 KSI 始终为 7，信号同为
  `{isInitMsg=0}`；映射观察区域的下行 KSI 依次为
  `5→6, 0→1, 2→3, 4→5, 6→0, 1→2, 3→4, 5→6`，16/16 个样本迁移成功。

S008 的完整链为
`E0016: s1→s0, deregistrationRequest/null_action` →
`E0002: s0→s6, registrationRequestGUTI/identityRequest` →
`E0073: s6→s1, registrationRequest/authenticationRequest`。前序反推保持 E0016 为 `r'=r`，只对最近的
无 KSI 下行边 E0002 求前像。E0002 自身上下文为 `{isInitMsg=1}`、数值输入为
`[ngksi_uplink=7]`，没有 KSI 下行；在全局观测值域 `{0,1,2,3,4,5,6,7}` 中，8 个样本的允许输出均为
`{6,7}`。六个精确候选全部保留，并因动态 `T=7` 排为：`r'=7`、`r'=6`、`r'=r+6`、`r'=r+7`、
`r'=r_i-1`、`r'=r_i`。

这些结果仍是带相对稳定推断迁移来源、E0016 保持假设和最近前序边归因的
`hypothetical_candidate`，不能确认 E0002 的真实实现更新，也不把 E0073 的联合拟合失败解释为源码事实。

## 循环轨迹相似度与聚类

独立后处理只读取完成反推后的 `candidates.json`，不修改候选。每个 `cycle_id + sequence_line` 保留
R3–R10 八个原始样本；严格检查 R3/R10 同相位后，将 R3–R9 七点基本周期复制为两轮，形成 14 个
`analysis_points`。其中第二轮只有 R10 是实测点，其余点明确标记为模式补齐，不作为 Open5GS 实测数据。
82 条轨迹首尾严格一致；另外 4 条只使用 R10 的数值输入补齐 R3 同相位缺失值，没有进行趋势外推。

共提取 57 条相对稳定推断轨迹和 29 条假设性候选轨迹。14 点的 `(r_before,r_after,i)` 完全一致时标记为
`low_discriminability`：稳定轨迹 25 条、假设性轨迹 13 条，共 38 条。它们保留在 JSON 和图中作为灰色
背景，但完全退出距离矩阵、层次聚类、簇数选择和迁移一致性统计。实际参与聚类的是稳定 32 条、假设性
16 条，共 48 条。

`isInitMsg` 仅作为 `s` 切片变量，不进入点距离或方向距离；`s=0`、`s=1` 与“不适用”分别聚类，不做
跨 `s` 联合聚类或自动簇匹配。采用 14 点两轮周期后，`registrationRequest/authenticationRequest`
的动态稳定轨迹在 `s=0` 切片内保持为 1 簇，不再因八点窗口重复了不同相位而拆分。

`authenticationResponse/securityModeCommand` 当前自动选择 4 簇，但该候选的 merge gap 为
`3.231×10^12`，来自前一合并高度为 0 时使用 `1e-12` 分母的数值放大。机器结果和标签暂时保留，
交互图显示警告；不得把这 4 簇直接解释为 4 种稳健函数逻辑。

原有 6 条迁移成功轨迹中，2 条因低辨别力退出一致性统计，另外 4 条只在各自 I/O 与 `s` 切片内报告
聚类一致性。该检查是迁移完成后的独立轨迹形状比较，不修改原迁移状态。聚类形状簇尚不能等同于函数
逻辑簇；本阶段也不据此实施跨 `s` 迁移。

## 有向折线族交互页面

新的离线页面沿用 `trajectory-clusters.json` 中 29 个 EID、86 条循环轨迹的成员边界，但不读取或展示
距离、soft-DTW、自动簇及其评价指标。每条轨迹只使用真实的 R3→R10 八点；八个
`(r_before,r_i,r_after)` 三元组全同者归为静态模板，其余归为动态模板。同一 EID、同一类型内的八点
有序三元组逐点全等时合并，最终得到 49 个精确模板，完整保留全部 `cycle_id + sequence_line` 成员。

页面按推断类型、I/O、`s`、EID、模板类型、模板和循环成员逐级筛选，并同步提供三维
`r_before / r_i / r_after`、二维 `r_before–r_after` 与二维 `r_i–r_after` 三个有向视图。公式区只展示
相对稳定推断的共享模型树、协调后的假设性候选公式或 E0073 的结构化“联合拟合失败，当前无公式”状态；
不混入前序反推、迁移失败公式或未纳入 I/O。每张模板卡片直接列出公共的 R3→R10 八点三元组序列；
其下每个循环成员可独立展开，查看该成员的 `s`、八个点和逐点来源。

三个视图的坐标范围均由全部 86 条轨迹预先确定；改变筛选条件或通过图例隐藏成员不会触发局部自动缩放。
两个二维视图的横纵轴单位长度相同，三维视图的三个轴也按数据单位等比例显示。

页面生成时按 `sequence_id` 重放冻结 trace 中的输入寄存器保持状态。因此
`E0019:S036:L22`、`E0019:S036:L24` 的 R3 `r_i` 为 3，
`E0019:S037:L26`、`E0019:S037:L28` 的 R3 `r_i` 为 1，来源均标为 `carried_from_R2`。
该局部修正不修改现有 `candidates.json` 或 `trajectory-clusters.json`，旧聚类报告、JSON 与静态 SVG
仍按原语义保留。

## 轨迹归类算法 B：二维公式候选、稳定性聚合与新稳定推断

轨迹归类算法 B 合并分析 `authenticationResponse/securityModeCommand`、
`registrationRequest/authenticationRequest` 与 `registrationRequestGUTI/authenticationRequest`，直接从
候选观察区域、完整冻结 trace、cycle-cover 和推断配置重建 29 个 EID 的 87 条真实 R3–R10 轨迹，共
696 点。三组 I/O 分别覆盖 `4/39/312`、`15/32/256` 和 `10/16/128` 个 EID／轨迹／点；每组产生两个
I/O 内候选组，总计六个。它不读取算法 A 的 `trajectory-clusters.json`；第一阶段的候选等级和 `s=0/1`
只作审计，不参与成员拆分或公式拟合。完整算法语义只由工具仓库的
`analysis/register_inference/experiments/AGENTS.md` 维护。

静态点轨迹和纯铅垂轨迹均不独立产生公式，只作为相容或未解决退化证据保留；顶层常数必须由单条轨迹内
实际水平移动支持。因此 E0103 不再拥有两个弱常数，`registrationRequest/authenticationRequest` 只有
E0085 在 `r_before–r_after` 投影拥有 `r'=0`。其余五组是 `r'=r`、两个 I/O 内独立的
`r'=ite(r<6,r+1,0)`，以及两个 I/O 内独立的 `r'=ite(r_i<6,r_i+1,0)`。全部拥有者、相容退化轨迹、
未解决点和 87 条具体轨迹由 JSON 与中文报告保存。

稳定性推断聚合只联合现有相对稳定推断的源边，三组 I/O 分别使用 `4/39/312`、`8/9/72` 和
`8/9/72` 个 EID／轨迹／点。`authResp/SMCmd` 得到 `r'=r`；`regReq/authReq` 在 `s=0` 条件下得到
`r'=ite(r<6,r+1,0)`；`regReqGUTI/authReq` 自动发现唯一输入寄存器铅垂值 `r_i=7`，得到
`r'=ite(r_i=7,ite(r<6,r+1,0),ite(r_i<6,r_i+1,0))`。三式分别逐点通过 `312/312`、`72/72`
和 `72/72` 验证。`s=0` 只作为适用条件，不进入公式树；`authResp/SMCmd` 标记信号不适用。

第三阶段扫描当前三组 I/O 的30条假设性轨迹，不以旧迁移成功、迁移失败或无匹配状态过滤。严格要求
长度2、R3–R10完整且三元组循环内变化后，共有6条轨迹入选；5条完整落在同 I/O 稳定三元组并集内且
主要方向一致，支持具体前序边 `E0046`、`E0124`、`E0160`、`E0172` 的条件性 `r'=r`。`E0172` 同时由
`E0145:S012:L14` 与 `E0146:S012:L15` 两个不同末端 I/O 支持。唯一未落入的动态轨迹
`E0085:S017:L17` 对其前序事件 `E0042` 逐轮得到集合前像 `{6,7}`；候选赋值方案 `A6/A7` 只在这条
证据轨迹内分别采用一致的伪 `r_after`，不构成 `E0042` 的边级公式。

用上述具体 EID 保持假设和事件级集合前像进行一次假设性区域重划后，去重得到5个真实、完整且动态的
新增长度1入口：`E0050:S009:L12`、`E0133:S003:L3`、`E0145:S005:L6`、`E0145:S012:L14`、
`E0146:S012:L15`。本阶段只标记其下一阶段可稳定性推断资格，不重新拟合；伪边界和静态长度1区域不作为
独立稳定证据。

新增“新稳定推断”阶段只读取上述5条动态长度1轨迹。它们的三元组均落入对应 I/O 的旧稳定轨迹点集，
去重有向段主要方向一致，且旧聚合树逐点精确，因此本次无需触发同信号联合重聚合：
`registrationRequest/authenticationRequest` 的旧 `s=0` 72点与新 `s=1` 24点共同通过
`r'=ite(r<6,r+1,0)`，验证 `96/96`；`registrationRequestGUTI/authenticationRequest` 的旧 `s=0`
72点及新 `s=0/1` 共16点共同通过原跨投影树，验证 `88/88`。两个 I/O 的 `s=0/1` 分支公式分别相同，
所以均化简为单一公式，不生成冗余 `signal_guard`；各信号分支的轨迹与样本来源仍在 JSON 中分别记录。

在此基础上，全模型稳定性推断迁移审计把两棵完整树应用到同 input/output 的全部 H14 边。
`registrationRequestGUTI/authenticationRequest` 覆盖10/10条；`registrationRequest/authenticationRequest`
覆盖13/15条。本阶段新增迁移 E0085 `s7→s8`、E0181 `s15→s1`、E0193 `s16→s1`，三者均为
`registrationRequest/authenticationRequest`，统一记为“稳定性推断（迁移）”；E0001 `s0→s1` 与
E0073 `s6→s1` 的同 I/O 边仍有长度4或长度3反例，暂不迁移。

E0085和E0181的9条完整长度2区域先由完整树在值域0…7上得到每轮前像 `{6,7}`，再依据旧稳定
`r_i–r_after` 投影唯一核心铅垂成分 `r_i=7` 只选择前序事件伪 `r_after=7`。该选择是算法B的候选
消歧规则，不把7表述成逆方程唯一解。冻结 trace 重放后，E0042 `s3→s7`
`securityModeReject/null_action` 的动态水平轨迹在两个投影均得到 `r'=7`；E0114 `s9→s7` 和 E0210
`s17→s15` 的同 I/O 前序边只有静态点，不独立产生公式。选择值7后，E0085与E0181的末端样本分别
通过64/64和8/8验证；E0193的两个静态轨迹直接通过16/16验证。完整推导、全部边及R3–R10轨迹见独立
迁移报告；本阶段不修改候选JSON或HTML。

独立 HTML 默认显示二维公式候选，可切换到稳定性推断聚合或新稳定推断；“前序最简与重划分”按钮暂时
置灰禁用，但其审计数据仍保留在 JSON。筛选栏依次为候选类型、输入/输出、`s`、EID、
投影、公式类型和候选组；原边摘要和证据等级筛选已删除。聚合模式只显示相对稳定推断源边，筛选只改变
画布可见内容，不重新计算聚合公式。页面继续固定全量视域、保持二维横纵单位长度一致，并使用统一公式
颜色和联动图例。新稳定模式将旧稳定轨迹显示为弱化背景、新轨迹突出显示，并提供两个二维投影；EID
选项同时给出 `EID、src/dst、input/output`。

本阶段不执行旧相对稳定推断迁移或旧前序反推，不进行全 DOT 推广，也不修改现有 `candidates.json` 或算法 A 产物。

## 全局可观察下行延伸与单轮重划叠加图

`edge-categories.svg` 另行展示一次明确标为激进假设的图形化推演，不改变上述算法 B 边界。工具检查全部
16 条完整长度2假设性区域（含动态和静态）：落入稳定三元组者的前序最简 I/O 是
`deregistrationRequest/null_action` 和 `securityModeComplete/registrationAccept`，未落入者只有
`securityModeReject/null_action`，两集合无交集。因此图中把前两个 I/O 在原始 H14 DOT 的全部 20 条对应边
标记为条件性 `r'=r` 延伸能力，并据此对全部假设性 `direct_regions` 只做一轮结构重划。延伸边不是新的
可观察下行锚点：它只把最近真实 KSI 下行的可观察值连续延伸到该边；中间一旦出现非延伸假设性边，
连续性立即失效，后续延伸边不能自行恢复。

收紧后重划得到 7 个完整长度1区域，其中5个动态、2个静态；本图只画派生角色，不为它们拟合公式。
`E0001:S018:L18` 因 E0042、E0086 已中断从 E0019 开始的真实下行连续性而被排除，后续 E0076 不能
重新建立锚点。每条 SMP 边只保留一条描边，派生角色按“新长度1、前序归因、全局延伸、原分类”优先级直接覆盖
原色；E0133、E0145 最终显示为蓝色，其同时具有的前序归因角色只保留在元数据中。紫色实线表示由
3 条匹配动态长度2区域的具体轨迹直接支持的前序最简延伸边，紫色虚线表示相同 I/O 在全 H14 的其他
条件性延伸边；两者公式同为 `r'=r`，区别只在证据来源。现有 SMP 布局省略 11 条延伸语义边：7 条非自环
回归 `s0` 边以源节点旁约 1 cm、指向左上角的紫色有向虚线短箭头表示，箭头头部固定为约 8 pt，不随
线宽放大；`s0→s0`、`s2→s2`、`s11→s11`、`s12→s12` 四条自环只记录、不绘制。节点和原路径不移动，也不重跑
Graphviz；图例和内嵌元数据均注明这些是派生标记，不是源码事实。

## 材料与导航

- 精确输入：[inputs/base-cycle-repeat10.seq](inputs/base-cycle-repeat10.seq)
- 原始小证据：[evidence](evidence)
- 原始 trace 无损物化清单：[analysis/register-inference/trace-materialization.json](analysis/register-inference/trace-materialization.json)
- 外部完整快照与哈希：[artifacts.yaml](artifacts.yaml)
- 实际事实：[analysis/observations.md](analysis/observations.md)
- ngKSI H13 式摘要：[analysis/register-inference/summary.md](analysis/register-inference/summary.md)
- 边分类叠加图：[analysis/register-inference/edge-categories.svg](analysis/register-inference/edge-categories.svg)
- ngKSI Excel 循环—边审计（此前明确要求时生成，未随本次术语改名刷新）：[analysis/register-inference/audit.xlsx](analysis/register-inference/audit.xlsx)
- 机器可读候选：[analysis/register-inference/candidates.json](analysis/register-inference/candidates.json)
- 轨迹聚类配置：[analysis/register-inference/trajectory-clustering-config.yaml](analysis/register-inference/trajectory-clustering-config.yaml)
- 轨迹聚类报告：[analysis/register-inference/trajectory-clustering-report.md](analysis/register-inference/trajectory-clustering-report.md)
- 轨迹聚类阶段性事实与后续研究问题：[analysis/register-inference/trajectory-clustering-research-notes.md](analysis/register-inference/trajectory-clustering-research-notes.md)
- 机器可读轨迹与聚类：[analysis/register-inference/trajectory-clusters.json](analysis/register-inference/trajectory-clusters.json)
- 有向折线族交互页面：[analysis/register-inference/trajectory-visualization.html](analysis/register-inference/trajectory-visualization.html)
- 轨迹归类算法 B 机器结果：[analysis/register-inference/trajectory-formula-candidates.json](analysis/register-inference/trajectory-formula-candidates.json)
- 轨迹归类算法 B 中文报告：[analysis/register-inference/trajectory-formula-report.md](analysis/register-inference/trajectory-formula-report.md)
- 全模型稳定性推断迁移报告：[analysis/register-inference/stable-migration-report.md](analysis/register-inference/stable-migration-report.md)
- 轨迹归类算法 B 独立交互页面：[analysis/register-inference/trajectory-formula-explorer.html](analysis/register-inference/trajectory-formula-explorer.html)
- 相对稳定推断静态图：[analysis/register-inference/trajectory-figures/stable.svg](analysis/register-inference/trajectory-figures/stable.svg)
- 假设性候选静态图：[analysis/register-inference/trajectory-figures/hypothetical.svg](analysis/register-inference/trajectory-figures/hypothetical.svg)
- 稳定与假设联合静态图：[analysis/register-inference/trajectory-figures/joint.svg](analysis/register-inference/trajectory-figures/joint.svg)
- 运行时异常假设与边界：[analysis/hypotheses.md](analysis/hypotheses.md)
- 运行/工具溯源：[provenance.yaml](provenance.yaml)
- H14 模型与基础组路线：[../h14-complete-teardown-20260801/README.md](../h14-complete-teardown-20260801/README.md)
- 系列索引：[../README.md](../README.md)

## 可复现命令

在本记录目录执行。schema v3 必须生成 Markdown 与完整 JSON；Excel 仅在明确要求审计工作簿时
才额外提供 `--workbook`。下列命令不生成 Excel。

```powershell
$prepare = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\prepare_register_inference_trace.py'
$tool = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\infer_cycle_ngksi_regions.py'
D:\anaconda3\python.exe $prepare `
  --config analysis/register-inference/config.yaml `
  --source-trace D:\state-learning-lab\run-data\open5gs\20260804T034905Z-2b7f2823\statelearner_trace.jsonl `
  --evidence-trace evidence/statelearner_trace.jsonl `
  --manifest analysis/register-inference/trace-materialization.json `
  --overwrite
D:\anaconda3\python.exe $tool `
  --config analysis/register-inference/config.yaml `
  --output analysis/register-inference/candidates.json `
  --report analysis/register-inference/summary.md
$cluster = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\cluster_cycle_trajectories.py'
$visualize = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\visualize_cycle_trajectories.py'
$polyline = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\visualize_directed_polyline_families.py'
$formula = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\discover_trajectory_formula_candidates.py'
$migration = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\report_full_model_stable_migration.py'
$overlay = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\render_edge_category_overlay.py'
D:\anaconda3\python.exe $cluster `
  --candidates analysis/register-inference/candidates.json `
  --config analysis/register-inference/trajectory-clustering-config.yaml `
  --output analysis/register-inference/trajectory-clusters.json `
  --report analysis/register-inference/trajectory-clustering-report.md
D:\anaconda3\python.exe $visualize `
  --input analysis/register-inference/trajectory-clusters.json `
  --output-svg-dir analysis/register-inference/trajectory-figures
D:\anaconda3\python.exe $polyline `
  --candidates analysis/register-inference/candidates.json `
  --trajectory-scope analysis/register-inference/trajectory-clusters.json `
  --trace evidence/statelearner_trace.jsonl `
  --cycle-cover ../h14-complete-teardown-20260801/analysis/cycle-cover/base-result.json `
  --config analysis/register-inference/config.yaml `
  --output-html analysis/register-inference/trajectory-visualization.html
D:\anaconda3\python.exe $formula `
  --candidates analysis/register-inference/candidates.json `
  --trace evidence/statelearner_trace.jsonl `
  --cycle-cover ../h14-complete-teardown-20260801/analysis/cycle-cover/base-result.json `
  --config analysis/register-inference/config.yaml `
  --output analysis/register-inference/trajectory-formula-candidates.json `
  --report analysis/register-inference/trajectory-formula-report.md `
  --html analysis/register-inference/trajectory-formula-explorer.html
D:\anaconda3\python.exe $migration `
  --candidates analysis/register-inference/candidates.json `
  --trajectory-formulas analysis/register-inference/trajectory-formula-candidates.json `
  --trace evidence/statelearner_trace.jsonl `
  --cycle-cover ../h14-complete-teardown-20260801/analysis/cycle-cover/base-result.json `
  --config analysis/register-inference/config.yaml `
  --output analysis/register-inference/stable-migration-report.md
D:\anaconda3\python.exe $overlay `
  --base-svg ../h14-complete-teardown-20260801/analysis/model/smp.svg `
  --dot ../h14-complete-teardown-20260801/evidence/hypotheses/hypothesis_14.dot `
  --candidates analysis/register-inference/candidates.json `
  --trajectory-formulas analysis/register-inference/trajectory-formula-candidates.json `
  --output analysis/register-inference/edge-categories.svg
```

如明确要求 Excel，额外传入 `--workbook analysis/register-inference/audit.xlsx` 及本机
workbook 渲染器参数；不得把该可选审计材料误写成每次推断的必需交付。

本页及上述新增说明已检查长路径、公式和代码标识符的换行；说明未使用表格，窄屏页面按单列布局，
不会因筛选器或公式产生横向溢出。

## 后续

在使用额外短环组或下一轮重复环分析前，先修复核心网停止后的端口回收问题，
并冻结实际 Open5GS 二进制和运行时脚本哈希。后续所有 trace 推断只读取完整的
`statelearner_trace.jsonl`；`statelearner_trace.cleaned.jsonl` 不作为分析输入。
