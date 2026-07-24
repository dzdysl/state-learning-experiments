# State-Learning Experiments Repository

## Purpose and boundaries

This repository is the evidence and conclusion layer for state-learning work. Keep source code, learner JARs, UERANSIM/SUL implementations, and core-network runtime changes in their respective source repositories. Keep cross-platform analysis scripts in `D:\state-learning-lab\projects\state-learning-tools`.

One record must describe one clearly bounded experiment run, experiment iteration, or failure investigation. Never mix logs or conclusions from unrelated runs in the same record.

## Record types and IDs

- Put ordinary, repeatable measurements under `experiments/<platform>/<series>/<iteration>/`.
- Put a crash, query inconsistency, timeout, state explosion, non-deterministic output, or suspected learner/core defect under `failures/<platform>/<failure-id>/`.
- Use `platform` values `open5gs`, `free5gc`, `oai`, or `cross-platform`.
- Name an iteration or failure ID in lower-case kebab case. Include the platform/version, concise phenomenon, date, and optional round, for example `free5gc421-ttt-inconsistency-20260723-r14`.
- Put only stable, broadly reused sequence families in `sequences/canonical/`. A one-off sequence belongs to its record's `inputs/`; a crash-inducing sequence belongs to the failure record and is never mixed into ordinary canonical sequences.

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
- 在 `provenance.yaml` 记录核心网 snapshot ID、核心网 commit/tag、学习器 commit、UERANSIM/SUL 路径与 commit；在 `analysis/observations.md` 记录支持结论的源码文件、函数/模块、行范围和 revision。因果猜测仍只写入 `analysis/hypotheses.md`。

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

## From raw data to a complete record

When given a new set of logs, sequences, database files, screenshots, or learner output:

1. Identify one run window and its exact source file list. Do not select a shared append-only log directory or wildcard collection until it is frozen to the relevant time window.
2. Choose `experiments/` for a planned measurement or `failures/` for an abnormal/unstable result; allocate the ID before copying files.
3. Create the record root and write `README.md` with status, run window, confirmed failure/measurement, and next action. Create `provenance.yaml` before analysis; record the three platform commits/tags that apply, `state-learning-tools` commit, learner JAR SHA-256, config hash, sequence/SUL/UERANSIM variant, and timezone.
4. Run a dry-run archive manifest using only the selected inputs:

   ```powershell
   & D:\state-learning-lab\projects\state-learning-tools\operations\experiment_archive\New-StateLearningArchive.ps1 `
     -Platform free5gc -FailureId <failure-id> -InputPath <explicit-file-or-frozen-directory>
   ```

5. Review file count, size, timestamps, and SHA-256. Then run the same command with `-Apply`. A total at most 10 MiB is copied to `<record>/raw/`; a larger total becomes an external immutable snapshot under `D:\state-learning-lab\run-data\<platform>\<run-id>`. Complete `artifacts.yaml` for every external/snapshot artifact.
6. Copy the minimal exact sequence/query/config into `inputs/`; freeze direct excerpts and original hypothesis DOT files in `evidence/`. Add a `reproducer.seq` only after its multiSeq syntax and behaviour have been confirmed.
7. Start objective analysis in `analysis/observations.md`; place candidate causes in `analysis/hypotheses.md`; create figures only from a documented source DOT or other recorded evidence.
8. Add concrete remaining tasks to `followups/`. Update the README status only after each reproduction, minimisation, explanation, or fix-verification step.

## Git and generated files

- When generating files from a user command, retain only requested final deliverables plus required source evidence, provenance, and recorded derivatives. Unless explicitly requested otherwise, verify the final output and promptly remove task-created disposable intermediates, staging copies, and exploratory output. Never delete user-existing inputs, immutable evidence, requested figures/records, tracked files, or anything outside the task scope; ask if retention is unclear.
- In ordinary mode, inspect discoverable build/generation targets, environment, artifact paths, and verification scope first. If an unresolved point could change a generated result, overwrite/damage an existing artifact, or affect reproducibility, ask the user before proceeding; do not wait for Plan mode.
- Do not use `git add .`. Review selected paths and hashes first.
- The repository ignores `*.log`, `*.jsonl`, SQLite, pcap, JAR, ZIP, temporary output, and Python cache by default. If a reviewed small file under `raw/` or `evidence/` matches an ignored pattern, add that exact path with `git add -f <record>/raw/<file>` or `git add -f <record>/evidence/<file>`; record why it belongs in Git in the README or observations.
- SVG/PDF/PNG under `analysis/figures/` are allowed only when final, small, and reproducible from a documented source. Keep bulk visual exploration outside Git or under ignored `tmp/`.
- Do not commit original large traces, databases, pcaps, or mixed append-only logs just to make a record look complete. Use `artifacts.yaml` and external snapshots instead.
- Never commit, tag, or push automatically. A record is ready for review when its provenance, input, evidence locations, analysis separation, external-artifact hashes, and follow-ups are explicit.
- When the user explicitly requests a commit, immediately create an annotated, immutable tag after the commit succeeds. Use a purpose-and-date tag name. Never overwrite, delete, or move an existing tag—stop and report a name collision.
