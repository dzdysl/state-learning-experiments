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
  --output-dir <record>\analysis\derived\register_inference
```

Keep the resulting `prepared.json`, `candidates.json` and `fit.json` under the
record's `analysis/derived/register_inference/`.  Record the command, tool
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
