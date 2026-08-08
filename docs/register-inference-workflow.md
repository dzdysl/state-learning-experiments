# Register-state-machine inference

Use the versioned tool at
`D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\analyze_register_machine.py`
when a frozen experiment trace has been mapped explicitly to a hypothesis DOT.

Put the experiment-specific YAML configuration in `<record>/inputs/`.  It must
name the immutable DOT and frozen JSONL trace, then declare the paths for DOT
transition identity, integer register values before/after the transition, and
optional integer input values.  Do not encode a KSI-specific layout as a tool
default.

```powershell
$tool = 'D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\analyze_register_machine.py'
& D:\anaconda3\python.exe $tool run `
  --config <record>\inputs\register-inference.yaml `
  --output-dir <record>\analysis\register-inference
```

Keep the resulting `prepared.json`, `candidates.json` and `fit.json` under the
record's `analysis/register-inference/`.  Record the command, tool
commit, input SHA-256 values and result hashes in the record.  Retain all
repeated observations; do not align away missing, duplicated, reordered or
unmappable events.

## KSI-specific trajectory rule

For a KSI analysis, retain `ue_sec_ctx_ngksi` and `*_ksi_value` fields only;
do not include any `*_ksi_tsc` field in inputs, normalised observations,
candidate equations or reports.  `ue_sec_ctx_ngksi` is a UE-internal context
observation, useful for checking UE adoption after a downlink message, but it
is not normally an AMF-visible input.  By default, do not use it as a
replacement for the uplink `registration_ksi_value` when fitting AMF behaviour.
When a concrete user request explicitly selects it, label the result as a
UE-internal observation.  Keep UE-side observations and AMF-facing
Registration/Authentication/SMC KSI values in separate labelled columns.

The current `simple_scalar_v1` stage can only propose and fit `r'=r`, `r'=c`,
`r'=r+k` and `r'=i`.  Its results are heuristic register candidates, not
confirmed AMF implementation facts.  Before a causal claim, correlate a
candidate with the exact AMF and UERANSIM/SUL source revisions.  Guard/ITE
inference, CEGIS and model-DOT rendering are reserved for later tool versions.

## 重复环 schema v3

重复环 ngKSI 分析使用
`D:\state-learning-lab\projects\state-learning-tools\analysis\register_inference\experiments\infer_cycle_ngksi_regions.py`。
输入必须是通过物化契约校验的完整 `statelearner_trace.jsonl`，默认生成 JSON 与 H13 四列 Markdown；
只有用户明确要求时才生成单表 Excel 审计。不得使用 cleaned trace。

候选语言、观察区域、假设性候选协调、相对稳定推断、动态 `T` 偏好、迁移检验和前序反推只以
`state-learning-tools/analysis/register_inference/experiments/AGENTS.md` 为完整算法规范，并由工具仓库的
`docs/mealy/register-inference-workflow.md` 与 `docs/mealy/typed-temporal-register-inference-design.md` 解释。
实验仓库只保存实验特定配置、冻结输入、JSON/Markdown 结果，以及明确请求时的 Excel、可复现命令和 provenance 哈希；
不得在本文件或各记录中复制一套可能发生偏移的算法定义。
