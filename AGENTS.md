# State-Learning Experiments Repository

## Purpose and boundaries

This repository is the evidence and conclusion layer for state-learning work. Keep source code, learner JARs, UERANSIM/SUL implementations, and core-network runtime changes in their respective source repositories. Keep cross-platform analysis scripts in `D:\state-learning-lab\projects\state-learning-tools`.

One record must describe one clearly bounded experiment run, experiment iteration, or failure investigation. Never mix logs or conclusions from unrelated runs in the same record.

## Language for prose

- README、provenance 的说明字段、分析报告、观察、假设、图注和其他面向读者的文字说明一律使用中文。文件名、路径、代码标识符、协议字段、公式、命令和原始日志内容可保留其原有语言；不得为了翻译而改写原始证据字节。

## 术语纪律

- 报告、实验流程和交付说明优先使用用户已经给出的术语，以及当前目录适用的专用 `AGENTS.md` 中定义的
  规范术语。除非用户明确要求新命名，或原术语确有歧义且已先说明，否则不得自行创造近义术语、状态名或
  分类名。代码中的兼容字段、内部枚举和临时变量不得未经解释直接提升为面向读者的概念。
- 本仓库只记录实验输入、证据边界和交付结果。跨实验分析算法的术语与流程应链接其工具仓库的唯一规范，
  不在多个实验报告或本文件中分别改写出不同版本。

## 目录层级、路径与命名

- 实验目录锚点为 `experiments/<platform>/<series>/`，失败目录锚点为
  `failures/<platform>/`。锚点之后最多使用三层目录，文件名不计入层数。例如
  `<record>/analysis/register-inference/candidates.json` 合规；不得继续创建
  `analysis/derived/<tool>/<variant>/...` 一类流水线式层级。
- 仓库内任何新增、移动或再生成文件的 Windows 完整绝对路径不得超过 240 个字符。
  在运行生成命令前必须先解析最终绝对输出路径并检查层数与字符数；超限时先缩短记录名、
  主题目录或文件名，不得生成后再留下长路径副本。
- 同一 series 下的后续导出、复现、比较、验证和收尾记录使用兄弟记录目录，通过双方
  README 和 `provenance.yaml` 中相对的 `parent_record` 或 `related_records` 表达关系；
  不再新建嵌套 `followups/<id>/`。
- 记录内优先使用 `inputs/`、`evidence/`、`raw/`、`analysis/`，每类目录最多再增加一层
  有业务含义的主题目录，例如 `analysis/model/`、`analysis/cycle-cover/`、
  `analysis/register-inference/` 或 `evidence/hypotheses/`。单个小结果直接放在分类目录。
- 文件与目录使用简短、可读的 kebab-case 名称。父级已经表达的平台、series、记录 ID、
  hypothesis 或分析主题不得在子文件名中机械重复；在主题目录内优先使用 `result.json`、
  `report.md`、`config.yaml`、`audit.xlsx`、`smp.svg` 等角色名，必要的状态、边、循环或
  轮次编号可以保留。
- `raw/` 完整导出、原始日志、协议证据和外部快照保留原始文件名与字节；不得仅为缩短或
  美化而改写。路径迁移时用清单核对移动前后的字节数与 SHA-256。
- 未迁移的旧记录可以保留既有深层目录，但不得向超限目录新增文件。若需要再生成或替换
  旧派生结果，应把同一结果族迁入合规主题目录，更新全部活动引用与 provenance 哈希，
  并删除被完整取代的旧派生路径。

## 派生结果的替换与精简

- 当新的分析或运行结果以同一问题、同一证据边界和可复现命令为基础，并且能完整取代旧的派生结果时，可以直接覆盖或移除旧的派生结果，使项目结构保持精简、不保留无意义的重复副本。替换前应确认新结果的输入、命令和结论均已记录；若旧结果仍具有独立的比较价值，应在新结果或记录说明中保留其来源关系。
- 此规则仅适用于 `analysis/` 下的派生文件、可再生成输出和任务创建的临时材料。`evidence/` 中的原始证据、已冻结的输入、原始日志快照及其哈希记录不得覆盖或删除；它们仍遵循证据不可变规则。

## 同一运行的后续材料与重复内容

- 同一 `run_id` 的后续导出、完成结果或收尾异常，如果仍在解释同一次学习生命周期，应作为同一 series 下的兄弟记录保存。父记录保留其原始证据边界；后续记录只保存该后续时间窗的最终证据、结论和派生物，并在双方 README 与 provenance 互相链接。
- 不同运行、不同输入边界或不同验证问题（例如对部分序列的替换验证）也应各自作为 sibling follow-up，不得把新运行的 raw、provenance 或结论混入原运行。上层 README 必须明确哪个后续结果取代了哪一段**派生分析**，但不得声称取代原始证据。
- 完整 `raw/` 导出中与 `evidence/` 的最终 DOT、manifest 或日志同哈希，或同一导出内同时含 `learnedModel.dot` 与最终 `hypothesis_N.dot`，属于可审计的角色重复：前者保存完整原始导出，后者提供最小直接证据。只要 `raw/` 是有界完整导出，两者均保留，并在 README/observations 记录哈希关系；不得为了节省空间删除 raw 成员。
- 可再生成的 `analysis/<topic>/` 不保留旧名称的平行副本。分析范围扩大后，应把配置、结果、报告及其全部引用一起改为反映真实范围的名称；在可再生性、输入哈希和替代关系已记录后，可直接替换旧派生文件。

## 报告交付前的可读性检查

- 每次新建、覆盖或实质性更新面向读者的 Markdown 报告后，必须在交付前检查排版可读性；这项检查不能只验证内容或哈希。
- 检查长路径、逻辑消息对、状态/边 ID、公式和代码标识符的换行。表格中的 `A/B` 形式应在 `/` 后显式换行，避免长的未断行标识符撑宽列或溢出。
- 报告含表格时，必须检查列宽是否符合内容：长候选公式列应留有足够宽度，`候选等级`、状态/结论等中文说明列不得窄到逐字竖排。必要时使用固定布局 HTML 表格、`colgroup` 宽度和 `<br>` 调整；同时保持窄屏下可读。
- 检查所有循环、边或其他承诺列出的结果是否完整覆盖，表头与单元格对齐，代码公式与普通说明均可辨认。完成检查后，在报告中简要记录所采用的换行与列宽策略。
- Excel 的工作簿预览 PNG 只可位于系统临时目录；artifact-tool 产生的
  `<workbook>.inspect.ndjson` 属于检查中间文件，验证后必须删除，不得放入 `analysis/`、`evidence/`、`raw/` 或 Git。

## Record types and IDs

- Put ordinary, repeatable measurements under `experiments/<platform>/<series>/<record-id>/`.
- Put a crash, query inconsistency, timeout, state explosion, non-deterministic output, or suspected learner/core defect under `failures/<platform>/<failure-id>/`.
- Use `platform` values `open5gs`, `free5gc`, `oai`, or `cross-platform`.
- Name a record or failure ID in lower-case kebab case. Avoid repeating platform/version/series context already expressed by parent directories; retain the concise phase or phenomenon, date and optional round.
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
    <topic>/                # optional single topic layer for final derivatives and figures
```

Do not create every optional file before it has content. Preserve the top-level names when they exist so tools and later analysis remain predictable.

## Adaptive organization of mixed materials

Treat the required layout as an evidence model, not a rule that every record must have the same directories. Inventory the supplied material before moving it and classify each item by the question it answers: original failure/experiment evidence, exact input, planned comparison, regression, reproduction or minimisation, root-cause analysis, fix verification, figure/derived output, or mixed/external artifact.

The following is an optional pattern for an investigation that accumulates materially different later runs. Related records are siblings, not nested descendants:

```text
<collection>/
  <parent-id>/
    README.md
    provenance.yaml
    inputs/
    evidence/
    analysis/
  <comparison-id>/
    README.md
    provenance.yaml          # parent_record: ../<parent-id>
    evidence/
    analysis/
  <reproduction-id>/
    ...
```

- Use a separate sibling `<descriptive-id>/` when later material comes from a different run, build, test set, or question but still investigates the same parent record. Give it its own status, provenance, artifacts and conclusion; create only populated subdirectories and record the parent relation explicitly.
- Keep the parent `README.md` and, when useful, a series-level `README.md` as navigation and a confidence-aware synthesis. Do not copy a related record's conclusion into the original evidence layer.
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

Unless a request explicitly asks for a pure/original view, generate the SMP derivative by default. It does not modify the evidence DOT and writes the simplified model plus figure files under `analysis/model/`:

```powershell
$record = 'D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14'
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  "$record\evidence\hypotheses\hypothesis_13.dot" `
  --simplify --formats svg,pdf --output-dir "$record\analysis\model"
```

For a pure/original view that preserves every DOT edge, omit `--simplify` and write the result under `analysis/figures/`:

```powershell
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  "$record\evidence\hypotheses\hypothesis_13.dot" `
  --formats svg,pdf --output-dir "$record\analysis\figures"
```

The default command creates the simplified DOT and rendered files in `analysis/model/`. Record the command, source relative path, source/output SHA-256 values, purpose, and visual conclusion in `analysis/rendering.md`. Keep only final figures in Git; place exploratory renders under `tmp/` or outside the repository.

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

schema v3 寄存器推断默认生成 JSON 与 H13 式 Markdown 摘要；`--report` 不得省略。只有用户明确要求
Excel 时才提供 `--workbook` 并生成 Excel 审计工作簿。Markdown 以固定四列表覆盖全部具体 DOT 边组。Excel
只保留“循环边使用”
工作表，同一边被不同循环复用时必须分别出现；不显示环内序号，`expand` 变体使用稳定 `V01…`。
工作表必须单列候选类型、本循环候选、最简交集或联合拟合、相对稳定推断、迁移检验和反推解释；
并列公式使用 `① … ｜ ② …` 横向排列。全部单元格水平/垂直居中，A-I 使用紧凑列宽，并冻结表头、
启用筛选和换行。完整公式树、各循环局部候选、最简交集、联合拟合、全部迁移与反推样本、原始区域和
重复样本继续由 JSON 无损保存。

寄存器推断的候选语言、区域构造、相对稳定推断、假设性候选协调、动态常数偏好、迁移检验和前序反推，
只以 `state-learning-tools/analysis/register_inference/experiments/AGENTS.md` 为算法规范。本仓库不得复制、
改写或另起名称描述同一算法；这里只规定冻结输入、证据边界和交付格式。若算法规范变化，应重新生成
整个派生结果族并同步报告、provenance 与哈希。

报告或解释具体实例时，必须完整列出 EID、`src/dst`、`input/output`、上行与下行 KSI、信号量、数值
输入以及映射后的观察区域。观察区域统一写成 `(r_before, ordered_observation_items, r_after)`；其中信号量
使用 `{signal=value}`，数值输入使用 `[input=value]`，并保持真实事件顺序，不得只给公式或省略上下文。

完成候选推断后，再人工对照该实验对应的 AMF 源码快照和实际 UERANSIM/SUL revision，报告候选公式、
源码位置、置信度和证据边界。源码对照只能作为后续低/中/高置信度说明，不得倒推为已经证实的内部变量
或真实更新时间。完整工作流入口为
`state-learning-tools/docs/mealy/cycle-cover-sequence-workflow.md`。

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
8. Create a sibling related record for each concrete reproduction, minimisation, explanation or fix verification, and link it from the parent README/provenance. Update status only after that work is complete.

## Submission-ready record normalization

Before staging a new or substantially updated record, normalize the supplied material using this reusable decision process:

1. Inventory every modified and untracked path, then group material by primary phenomenon and independent lifecycle. Planned measurements belong to `experiments/`; abnormal or unstable results belong to `failures/`; a later run that answers a narrower question remains a sibling related record inside the same series or platform collection.
2. Give every record a concise descriptive kebab-case ID. A runtime-generated run ID belongs in `provenance.yaml`, the preserved raw directory name and evidence references; do not use a bare run ID as the record ID.
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
