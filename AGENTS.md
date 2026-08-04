# State-Learning Experiments Repository

## Purpose and boundaries

This repository is the evidence and conclusion layer for state-learning work. Keep source code, learner JARs, UERANSIM/SUL implementations, and core-network runtime changes in their respective source repositories. Keep cross-platform analysis scripts in `D:\state-learning-lab\projects\state-learning-tools`.

One record must describe one clearly bounded experiment run, experiment iteration, or failure investigation. Never mix logs or conclusions from unrelated runs in the same record.

## Language for prose

- README、provenance 的说明字段、分析报告、观察、假设、图注和其他面向读者的文字说明一律使用中文。文件名、路径、代码标识符、协议字段、公式、命令和原始日志内容可保留其原有语言；不得为了翻译而改写原始证据字节。

## 派生结果的替换与精简

- 当新的分析或运行结果以同一问题、同一证据边界和可复现命令为基础，并且能完整取代旧的派生结果时，可以直接覆盖或移除旧的派生结果，使项目结构保持精简、不保留无意义的重复副本。替换前应确认新结果的输入、命令和结论均已记录；若旧结果仍具有独立的比较价值，应在新结果或记录说明中保留其来源关系。
- 此规则仅适用于 `analysis/` 下的派生文件、可再生成输出和任务创建的临时材料。`evidence/` 中的原始证据、已冻结的输入、原始日志快照及其哈希记录不得覆盖或删除；它们仍遵循证据不可变规则。

## 同一运行的后续材料与重复内容

- 同一 `run_id` 的后续导出、完成结果或收尾异常，如果仍在解释同一次学习生命周期，应作为父记录的独立 `followups/<descriptive-id>/` 保存。父记录保留其原始证据边界；后续记录只保存该后续时间窗的最终证据、结论和派生物，并在双方 README 互相链接。
- 不同运行、不同输入边界或不同验证问题（例如对部分序列的替换验证）也应各自作为 sibling follow-up，不得把新运行的 raw、provenance 或结论混入原运行。上层 README 必须明确哪个后续结果取代了哪一段**派生分析**，但不得声称取代原始证据。
- 完整 `raw/` 导出中与 `evidence/` 的最终 DOT、manifest 或日志同哈希，或同一导出内同时含 `learnedModel.dot` 与最终 `hypothesis_N.dot`，属于可审计的角色重复：前者保存完整原始导出，后者提供最小直接证据。只要 `raw/` 是有界完整导出，两者均保留，并在 README/observations 记录哈希关系；不得为了节省空间删除 raw 成员。
- 可再生成的 `analysis/derived/` 不保留旧名称的平行副本。分析范围扩大后，应把配置、结果、报告及其全部引用一起改为反映真实范围的名称；在可再生性、输入哈希和替代关系已记录后，可直接替换旧派生文件。

## 报告交付前的可读性检查

- 每次新建、覆盖或实质性更新面向读者的 Markdown 报告后，必须在交付前检查排版可读性；这项检查不能只验证内容或哈希。
- 检查长路径、逻辑消息对、状态/边 ID、公式和代码标识符的换行。表格中的 `A/B` 形式应在 `/` 后显式换行，避免长的未断行标识符撑宽列或溢出。
- 报告含表格时，必须检查列宽是否符合内容：长候选公式列应留有足够宽度，`候选等级`、状态/结论等中文说明列不得窄到逐字竖排。必要时使用固定布局 HTML 表格、`colgroup` 宽度和 `<br>` 调整；同时保持窄屏下可读。
- 检查所有循环、边或其他承诺列出的结果是否完整覆盖，表头与单元格对齐，代码公式与普通说明均可辨认。完成检查后，在报告中简要记录所采用的换行与列宽策略。
- Excel 的工作簿预览 PNG 只可位于系统临时目录；artifact-tool 产生的
  `<workbook>.inspect.ndjson` 属于检查中间文件，验证后必须删除，不得放入 `analysis/`、`evidence/`、`raw/` 或 Git。

## Record types and IDs

- Put ordinary, repeatable measurements under `experiments/<platform>/<series>/<iteration>/`.
- Put a crash, query inconsistency, timeout, state explosion, non-deterministic output, or suspected learner/core defect under `failures/<platform>/<failure-id>/`.
- Use `platform` values `open5gs`, `free5gc`, `oai`, or `cross-platform`.
- Name an iteration or failure ID in lower-case kebab case. Include the platform/version, concise phenomenon, date, and optional round, for example `free5gc421-ttt-inconsistency-20260723-r14`.
- Put only stable, broadly reused sequence families in `sequences/canonical/`. A one-off sequence belongs to its record's `inputs/`; a crash-inducing sequence belongs to the failure record and is never mixed into ordinary canonical sequences.

## Outcome classification

- The active-learning outcome takes precedence over post-completion runtime cleanup. When the learner has emitted its final completion summary and final hypothesis, a subsequent core-launcher/finalizer exit (including Open5GS exit status 134 during teardown) is a recorded `teardown_anomaly`, not a failed experiment.
- Keep the raw finalizer status, exit code, and supporting logs in provenance/evidence. State the anomaly and its impact boundary in the record README, but classify the experiment as successful for the purpose of active-learning completion.
- A learner that has not completed its algorithm or has not exported its final result remains incomplete or failed as appropriate; this exception must not hide failures that occur before completion.

## Experiment version and series assignment

- Determine an experiment's primary version and `series` from the actual UERANSIM/SUL source version deployed for the run, not from the learner JAR's embedded Git version. UERANSIM-only commits may intentionally reuse an older learner JAR.
- Record the UERANSIM/SUL repository, exact commit and tag, `src/` path, and an available deployed binary/source-archive hash in `provenance.yaml`. Record the learner commit, JAR hash and runtime dirty state separately as secondary provenance.
- Prefer direct deployment evidence such as a UERANSIM commit marker, binary hash, `src.zip` hash or immutable deployment manifest. If only source-correlated behavior and timing identify the UERANSIM version, state the confidence and missing byte-level boundary explicitly; never present a reference-worktree hash as a captured deployment hash.
- Do not infer the experiment version from an inherited `output_dir`, directory basename, learner branch name or current working-tree HEAD.
- A new semantic UERANSIM/SUL version starts a new experiment `series` by default. Repeated runs of the same UERANSIM/SUL version use separate descriptive iterations unless the experiment question requires an independent lifecycle.

## Required record layout

Use the same evidence model for both `experiments/` and `failures/`. A failure additionally freezes the smallest known reproducer when available.

```text
<record>/
  README.md                 # status, confirmed facts, scope, next action
  provenance.yaml           # time window, source/tool commits, JAR/config hashes, environment
  artifacts.yaml            # external/raw artifact path, bytes, SHA-256, relevance, note
  inputs/
    reproducer.seq          # failure only, once the syntax is confirmed
    ...                     # exact queries, configs, command lines, selected input copies
  evidence/                 # immutable, small, directly relevant evidence
    hypotheses/             # original exported hypothesis_*.dot files
    ...                     # selected excerpts, query outputs, compact traces
  raw/                      # selected complete raw files only when total size <= 10 MiB
  analysis/
    observations.md         # facts with a path/line/time reference; no causal claims
    hypotheses.md           # explicitly labelled possible causes and confidence
    rendering.md            # source DOT, render command, output paths and hashes
    figures/                # final SVG/PDF/PNG figures derived from evidence
    derived/                # simplified DOT or other non-source analytical derivatives
  followups/
    .gitkeep                # reproducing, minimising, root-cause and fix-verification tasks
```

Do not create every optional file before it has content. Preserve the top-level names when they exist so tools and later analysis remain predictable.

## Adaptive organization of mixed materials

Treat the required layout as an evidence model, not a rule that every record must have the same directories. Inventory the supplied material before moving it and classify each item by the question it answers: original failure/experiment evidence, exact input, planned comparison, regression, reproduction or minimisation, root-cause analysis, fix verification, figure/derived output, or mixed/external artifact.

The following is an optional pattern for a failure investigation that accumulates materially different later runs:

```text
<failure>/
  README.md
  inputs/                    # original failure input
  evidence/                  # original failure evidence
  analysis/                  # cross-investigation index and model analysis
  followups/
    <comparison-id>/
      README.md
      provenance.yaml
      artifacts.yaml
      inputs/
      evidence/
      raw/
      analysis/
    <reproduction-id>/
      ...
```

- Use a separate `followups/<descriptive-id>/` when later material comes from a different run, build, test set, or question but still investigates the same parent failure. Give that follow-up its own status, provenance, artifacts and conclusion; create only the populated subdirectories.
- Keep the parent `README.md` and, when useful, `analysis/README.md` as navigation and a confidence-aware synthesis. Do not copy a follow-up conclusion into the original evidence layer.
- Create a new top-level experiment/failure record instead of a follow-up when the material is unrelated, has a different primary phenomenon, or needs an independent lifecycle.
- A small one-off analysis may remain directly under `analysis/`; a single exact reproducer may remain under `inputs/`; a large frozen run may be only an external snapshot plus `artifacts.yaml`. Do not introduce nested structure merely for symmetry.
- Preserve original bytes and SHA-256 values when reorganizing. Move complete raw files to `raw/`, compact direct excerpts to `evidence/`, exact commands/sequences/configs to `inputs/`, and interpretations or figures to `analysis/`.
- Before applying this optional pattern, account for the actual material and any user-specified structure. Explicit user requirements override the pattern. If classification would change the experiment boundary, mix conclusions, overwrite an existing record, or discard provenance, ask before acting.

## Evidence rules

- `inputs/` contains exact input material. Copy a query/config/sequence rather than describing it from memory.
- `evidence/` contains original small direct evidence. Treat `evidence/hypotheses/*.dot` as immutable exported model evidence: do not simplify, recolour, merge, or overwrite it.
- `raw/` contains a complete, deliberately selected raw snapshot only when the aggregate selected size is at most 10 MiB.
- `analysis/` contains derived work. Keep facts in `observations.md`; causal explanations, alternatives, and confidence in `hypotheses.md`; do not turn an interpretation into evidence by placing it under `evidence/`.
- `artifacts.yaml` is required whenever a relevant artifact stays outside Git. Record absolute original/snapshot path, byte size, SHA-256, relevance (`direct`, `supporting`, `mixed`, or `indirect`) and whether later runs may have appended to it.
- Do not treat an append-only root log as direct proof unless the exact time-bounded segment has been frozen.

## Source-correlated sequence and failure analysis

- 分析实验序列、崩溃序列、协议输出或错误时，必须同时对照：对应版本的核心网源码快照，以及实际使用的 UERANSIM/SUL 源码版本；不要只根据日志、Mealy 输出或当前工作树推断行为。
- 核心网快照位于 `D:\state-learning-lab\sources\`：`open5gs_old`（Open5GS v2.6.6）、`open5gs_new`（实验标签 v2.8.0）、`free5gc-v4.2.1`、`free5gc-v4.2.2`、`oai-cn5g-v2.0.1`（OAI CN5G v2.0.1 Docker federation）。以 `D:\state-learning-lab\sources\source-manifest.yaml` 中的 revision 和来源为准。
- UERANSIM/SUL 必须从三套学习器仓库中与该实验 commit 对应的 `src/` 查找：`projects/open5gs-state-learning`、`projects/free5gc-state-learning`、`projects/oai-state-learning`。历史实验使用 `git show <commit>:src/<path>` 或独立 detached worktree；不得悄悄改用当前分支源码。
- 原生上游 UERANSIM 对照位于 `D:\state-learning-lab\sources\UERANSIM`。它用于判断实验版 SUL 相对原生实现修改了什么，不得把它的 commit 写成实验实际部署版本。
- CoreCrisis 公开 artifact 位于 `D:\state-learning-lab\sources\CoreCrisis`：`Corelearner` 用于对照状态推断与等价查询，`CoreFuzzer` 用于对照状态机引导测试，修改版消息适配器位于 `CoreCrisis\UERANSIM_CoreTesting`。该子目录属于 CoreCrisis 父仓库，不是独立 Git 仓库；引用时记录父仓库 commit 和该子目录 tree hash。
- `D:\state-learning-lab\sources\UERANSIM_CoreTesting - 快捷方式.lnk` 只是指向上述真实目录的 Windows 快捷方式。不要扫描、引用或写入 provenance 作为源码路径。
- 在 `provenance.yaml` 分开记录实际运行源码与参考源码：核心网 snapshot ID/commit、学习器 commit、实际 UERANSIM/SUL 路径/commit/tag/`src.zip` 哈希属于 runtime provenance；原生 UERANSIM 或 CoreCrisis 的 snapshot ID、commit/tree 和比较目的属于 `reference_sources`。在 `analysis/observations.md` 记录支持结论的源码文件、函数/模块、行范围和 revision。因果猜测仍只写入 `analysis/hypotheses.md`。

## DOT models and figures

For `failures/free5gc/free5gc421-ttt-inconsistency-20260723-r14`, original hypothesis DOT files are found at:

```text
evidence/hypotheses/hypothesis_1.dot ... hypothesis_13.dot
```

Unless a request explicitly asks for a pure/original view, generate the SMP derivative by default. It does not modify the evidence DOT and writes its `_smp.dot` plus figure files under `analysis/derived/`:

```powershell
$record = 'D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14'
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  "$record\evidence\hypotheses\hypothesis_13.dot" `
  --simplify --formats svg,pdf --output-dir "$record\analysis\derived"
```

For a pure/original view that preserves every DOT edge, omit `--simplify` and write the result under `analysis/figures/`:

```powershell
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  "$record\evidence\hypotheses\hypothesis_13.dot" `
  --formats svg,pdf --output-dir "$record\analysis\figures"
```

The default command creates `hypothesis_13_smp.dot` and rendered files in `analysis/derived/`. Record the command, source relative path, source/output SHA-256 values, purpose, and visual conclusion in `analysis/rendering.md`. Keep only final figures in Git; place exploratory renders under `tmp/` or outside the repository.

### Cycle-cover sequence export

When selected cycle-cover routes are converted into executable inputs, use the
versioned `state-learning-tools/analysis/cycle_cover/analyze_cycle_cover.py`
sequence-export options. For each route, rotate it to the numerically smallest
`sN` state, compute a deterministic shortest access prefix from `s0` on the
filtered original hypothesis, materialize merged SMP inputs according to the
recorded policy, and repeat each concrete closed route as requested. Simulate
every generated line against the original hypothesis before retaining it.

Store the final `.seq` under the experiment record's `inputs/`, not in
`state-learning-tools` or `analysis/`. Keep the evidence DOT immutable. Record
the source and output SHA-256 values, command, access-prefix rule,
merged-input policy, repetition count and line count in the record README or
observations. The canonical semantics and CLI are documented in
`state-learning-tools/docs/mealy/cycle-cover-sequence-workflow.md`.

### Repeated-cycle register-candidate analysis

在运行寄存器推断前，必须将冻结 raw 快照中的完整
`statelearner_trace.jsonl` 无损物化到记录的 `evidence/`：使用
`state-learning-tools/analysis/register_inference/experiments/prepare_register_inference_trace.py`
按同一个推断 YAML 的 `.seq`、`cycle_cover.sequence_export.cycles` 与 `cycle_id` 选择
校验每个变体。物化清单必须记录源/目标 SHA-256、字节数、记录数、`sequence_id` 组数和
变体映射，并声明 `payload_transformation: none`。`cleaned`、筛选、重排、去重、CSV、
日志/pcap 补字段或 live run 目录都不是合法推断输入；契约失败时停止推断，不得修补数据。

schema v3 寄存器推断必须同时生成 JSON、H13 式 Markdown 摘要和完整 Excel 审计工作簿；`--report`
与 `--workbook` 都不得省略。Markdown 以固定四列表覆盖全部具体 DOT 边组：循环、边与节点、边级
候选、输入寄存器、候选等级。完整材料在 Excel 中按 `cycle_id` 分为边级协调、循环—边使用、变体、
候选明细和协调证据；同一边被不同循环复用时必须分别出现。`expand` 变体显示稳定 `V01…` 及完整
`loop_inputs`，不以物理 `.seq` 行号为阅读主键。工作簿必须单列精确候选类型
`hypothetical_candidate` 或 `relatively_stable_candidate`，保留全部并列、交集、非共识、局部候选和
直接冲突证据。原始区域与重复样本继续仅作为 JSON 的无损证据。Markdown 须使用固定布局 HTML、
`colgroup` 列宽和 `/` 后换行；Excel 须冻结表头、启用筛选、换行和可读列宽；空交集不自动表述为
矛盾，只有完整类型化观察键对应不同 `r_after` 时才是 `confirmed_observational_conflict`。

Treat each generated `.seq` line as one concrete-cycle test. Keep the shortest
prefix and repetition 1 as setup context, then align repetitions 2-10 by the
same cycle edge, logical input, message direction/type and field path. Missing,
extra, duplicate, retransmitted or reordered messages are anomalies and must
not be hidden by shifting the alignment.

For every aligned field series, first try simple AMF logical-register
candidates such as `r'=r`, `r'=0`, `r'=c`, `r'=r+1`, `r'=r-1`, fixed-step or
modular increments, Boolean toggle and copy-from-input. Report all matching
candidates from simplest to most specific. If no candidate fits all nine
samples, report the first breaking repetition, the closest rule and the exact
values. These are heuristic candidates, not confirmed internal variable
names.

重复循环的类型化时序推断还必须遵守以下规则：

- 区域统一表示为 `(r_before, ordered_observation_items, r_after)`。信号量和数值输入都要保留字段路径、逻辑输入、事件位置和出现序号；缺失、重复、乱序或槽位身份不一致必须报告对齐异常，不得移动其他观察量补位。
- 信号只能通过通用 `mapping.signal_definitions` 配置，消息选择器支持任意列表和单独的 `"*"` 通配符；不得在推断代码中硬编码 `registrationRequest`、`registrationRequestGUTI` 或其他具体消息名。同一消息可携带多个信号，同一信号可应用于多个消息。
- 区域内先按轨迹事件时间排序；同一事件的信号在数值输入之前；多个信号和多个数值输入分别按配置声明顺序排列。相同字段出现在不同消息中时仍是独立槽位。
- 所有已配置并在区域中出现的信号都作为最外层 `signal_guard`，即使样本中的信号恒为 0 或恒为 1。未观察分支写为 `unknown/unobserved_signal_branch`，支持不足分支写为 `unknown/insufficient_support`；含未知叶子的候选只能标记为 `partial_observational_candidate`。
- 严格区分三类节点：`signal_guard` 使用 `s == 0/1`；`threshold_guard` 只表示 `ite(x < T, f, 0)` 形式的回绕，`else` 必须是常数 0；`derived_value_guard` 只在基础公式和阈值树均失败后枚举输入槽的观测值。派生值的两侧必须非空并各自满足最小连续支持，不能仅凭数值 7 自动赋予协议语义。
- 配置信号层数、`max_numeric_depth` 和 `max_derived_signal_depth` 独立计数。对 `r'=c`、`r'=r+k`、`r'=ij+k` 以及精确并列树不做任意取舍；候选索引使用有序 `guard_path + update_tree + status` 并维护具体 DOT 边集合。
- `isInitMsg` 在文字中称为伴随 NAS 输入事件的“初始上行传输上下文信号”。它不是 NAS PDU 内的显式 IE，但 AMF 可通过 NGAP `InitialUEMessage` 与普通 `UplinkNASTransport` 的不同入口区分这一上下文。当前 C01–C05 仅验证门控结构和已观察分支，不得声称验证未知分支或泛化能力。

Then inspect the exact experiment AMF source snapshot and UERANSIM/SUL revision
for a matching context member and update/reset/copy site on that edge. Report
the candidate equation, source location and high/medium/low confidence.
Perform this exploration manually first; do not create a fixed script until
real traces establish stable edge boundaries, message names and field paths.
The full procedure is in
`state-learning-tools/docs/mealy/cycle-cover-sequence-workflow.md`.

## From raw data to a complete record

When given a new set of logs, sequences, database files, screenshots, or learner output:

1. Identify one run window and its exact source file list. Do not select a shared append-only log directory or wildcard collection until it is frozen to the relevant time window.
2. Resolve the actual deployed UERANSIM/SUL version using the version-assignment rules above, then choose `experiments/` for a planned measurement or `failures/` for an abnormal/unstable result. Allocate the UERANSIM-version-based series and descriptive ID before copying files.
3. Create the record root and write `README.md` with status, run window, confirmed failure/measurement, version confidence and next action. Create `provenance.yaml` before analysis; record the primary UERANSIM/SUL commit/tag and available deployment hash separately from the learner/JAR commit and hash, plus the core-network snapshot, `state-learning-tools` commit, config hash, sequence variant and timezone.
4. Run a dry-run archive manifest using only the selected inputs:

   ```powershell
   & D:\state-learning-lab\projects\state-learning-tools\operations\experiment_archive\New-StateLearningArchive.ps1 `
     -Platform free5gc -FailureId <failure-id> -InputPath <explicit-file-or-frozen-directory>
   ```

5. Review file count, size, timestamps, and SHA-256. Then run the same command with `-Apply`. A total at most 10 MiB is copied to `<record>/raw/`; a larger total becomes an external immutable snapshot under `D:\state-learning-lab\run-data\<platform>\<run-id>`. Complete `artifacts.yaml` for every external/snapshot artifact.
6. Copy the minimal exact sequence/query/config into `inputs/`; freeze direct excerpts and original hypothesis DOT files in `evidence/`. Add a `reproducer.seq` only after its multiSeq syntax and behaviour have been confirmed.
7. Start objective analysis in `analysis/observations.md`; place candidate causes in `analysis/hypotheses.md`; create figures only from a documented source DOT or other recorded evidence.
8. Add concrete remaining tasks to `followups/`. Update the README status only after each reproduction, minimisation, explanation, or fix-verification step.

## Submission-ready record normalization

Before staging a new or substantially updated record, normalize the supplied material using this reusable decision process:

1. Inventory every modified and untracked path, then group material by primary phenomenon and independent lifecycle. Planned measurements belong to `experiments/`; abnormal or unstable results belong to `failures/`; a later run remains a `followups/<descriptive-id>/` only when it answers a narrower question inside the same investigation.
2. Give every top-level record and follow-up a descriptive kebab-case ID. A runtime-generated run ID belongs in `provenance.yaml`, the preserved raw directory name and evidence references; do not use a bare run ID as the follow-up ID.
3. Keep each record README concise: status, scope, confirmed conclusion, evidence/analysis navigation, follow-up summaries and next actions. Move detailed, source-referenced facts to `analysis/observations.md`; keep only unconfirmed causal explanations and explicit confidence/boundaries in `analysis/hypotheses.md`.
4. Add or update the repository README index for every top-level experiment or failure. Update parent links after renaming a follow-up, and verify that no old path or ID remains in active Markdown or metadata.
5. Remove only disposable task-created output: unreferenced intermediate renders, temporary staging copies and `.gitkeep` files in directories that now contain real records. Preserve immutable evidence, raw snapshots, requested final figures and all user-existing material whose role is uncertain.
6. Validate JSON/YAML syntax, relative Markdown links, archive file counts, byte sizes and SHA-256 values. Check external snapshots without copying large material back into Git.
7. Review ignored files explicitly. When a selected complete snapshot is at most 10 MiB, force-add only the exact reviewed `raw/` or `evidence/` paths that match ignore rules; never broaden `.gitignore` merely to stage one record.
8. Treat independent top-level records as separate commits and annotated tags by default, even when organized in one working session. Combine them only when the user explicitly requests a shared version boundary. Stage the repository index entry with the record it references so a tag never contains a dangling link.
9. Before each commit, inspect the staged path list and run format/link/hash checks plus `git diff --cached --check`. After an explicitly requested commit, create the predeclared annotated tag immediately and stop on any tag-name collision. Do not push unless explicitly requested.

## Git and generated files

- When generating files from a user command, retain only requested final deliverables plus required source evidence, provenance, and recorded derivatives. Unless explicitly requested otherwise, verify the final output and promptly remove task-created disposable intermediates, staging copies, and exploratory output. Never delete user-existing inputs, immutable evidence, requested figures/records, tracked files, or anything outside the task scope; ask if retention is unclear.
- In ordinary mode, inspect discoverable build/generation targets, environment, artifact paths, and verification scope first. If an unresolved point could change a generated result, overwrite/damage an existing artifact, or affect reproducibility, ask the user before proceeding; do not wait for Plan mode.
- Do not use `git add .`. Review selected paths and hashes first.
- The repository ignores `*.log`, `*.jsonl`, SQLite, pcap, JAR, ZIP, temporary output, and Python cache by default. If a reviewed small file under `raw/` or `evidence/` matches an ignored pattern, add that exact path with `git add -f <record>/raw/<file>` or `git add -f <record>/evidence/<file>`; record why it belongs in Git in the README or observations.
- SVG/PDF/PNG under `analysis/figures/` are allowed only when final, small, and reproducible from a documented source. Keep bulk visual exploration outside Git or under ignored `tmp/`.
- Do not commit original large traces, databases, pcaps, or mixed append-only logs just to make a record look complete. Use `artifacts.yaml` and external snapshots instead.
- Never commit, tag, or push automatically. A record is ready for review when its provenance, input, evidence locations, analysis separation, external-artifact hashes, and follow-ups are explicit.
- When the user explicitly requests a commit, immediately create an annotated, immutable tag after the commit succeeds. Use a purpose-and-date tag name. Never overwrite, delete, or move an existing tag—stop and report a name collision.
