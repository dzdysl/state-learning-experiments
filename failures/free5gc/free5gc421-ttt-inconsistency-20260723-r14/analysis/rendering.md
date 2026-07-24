# Rendering ledger

## hypothesis_13 SVG

- Source: `evidence/hypotheses/hypothesis_13.dot`
- Source SHA-256: `f98140bafeae59a4c1f92d1e1614eecf0100ca5fe794c0ec0d458c91fcaf4b51`
- Output: `analysis/figures/hypothesis_13.svg`
- Output SHA-256: `6de6296f8b0a12a2a77f02c41b5aed94e0e9147570cf78798ce31bedee14cd32`
- Renderer: Graphviz `dot` 13.1.2
- Transformation: none; this is a direct SVG rendering of the immutable evidence DOT.

```powershell
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\evidence\hypotheses\hypothesis_13.dot" `
  --formats svg --output-dir "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\analysis\figures"
```

## hypothesis_13 default SMP SVG

- Source: `evidence/hypotheses/hypothesis_13.dot`
- Source SHA-256: `f98140bafeae59a4c1f92d1e1614eecf0100ca5fe794c0ec0d458c91fcaf4b51`
- Derived DOT: `analysis/derived/hypothesis_13_smp.dot`
- Derived DOT SHA-256: `32087f529d7a3d1d27ba5c3a8605251ba5f5f74ab48d9f7fb69d7986e16e4789`
- Output: `analysis/derived/hypothesis_13_smp.svg`
- Output SHA-256: `d4a6a094616745c2f620044a6ca342970ade955750131435ad877f0fa3148e61`
- Renderer: Graphviz `dot` 13.1.2
- Transformation: default SMP derivative; self loops, transitions to `s0`, edges entering null-action self-loop sinks are removed, and matching source/destination/output transitions are merged. No hard-coded state highlight is applied.

```powershell
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\evidence\hypotheses\hypothesis_13.dot" `
  --simplify --formats svg --output-dir "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\analysis\derived"
```

## hypothesis_13 failure-query trace SMP

- Source: `evidence/hypotheses/hypothesis_13.dot`
- Source SHA-256: `f98140bafeae59a4c1f92d1e1614eecf0100ca5fe794c0ec0d458c91fcaf4b51`
- Simulation: `analysis/derived/hypothesis_13_failure_query_simulation.json`
- Simulation SHA-256: `743dd9e26564de9548a55ca52b48dc12f32c88d6623ab6f97de8f87120f059a0`
- Observed-output comparison: `analysis/derived/hypothesis_13_failure_query_observations.json`, transcribed from `inputs/failure-query.txt`
- Comparison SHA-256: `316522d8ce90c08d597a455a2a0756d50c17e10078fe77519a211dcd0d790667`
- Derived DOT: `analysis/derived/hypothesis_13_failure_query_smp.dot`
- Derived DOT SHA-256: `243d82c121c0eabf2fc3a9c1cb9d27668475ebd15f5a682ff75484c476f30879`
- Outputs: `analysis/derived/hypothesis_13_failure_query_smp.svg` (SHA-256 `41ff86cf05e38f670f501bb1db2ec9bfe50af51c90b4d00e511f9a6780f0a20f`) and `analysis/derived/hypothesis_13_failure_query_smp.pdf` (SHA-256 `288f48805bdf39d1b57ec814300fbced36ceff0bc4027cd538e3ed2804ef45d4`)
- Renderer: Graphviz `dot` 13.1.2 through `render_graphviz.py` with trace highlighting.
- Transformation: SMP analysis derivative. Ordinary self loops, transitions to `s0`, null-sink incoming edges, and mergeable transitions follow the default rules; the nine trace transitions are red, numbered, and retained as explicit exceptions. The red legend records output mismatches at steps 7--9.
- Visual conclusion: the model predicts `null_action` at steps 7--9. Step 7 conflicts with all three observed runs; step 8 matches runs 2--3 but not run 1; step 9 matches run 1 but not runs 2--3. No observed full output sequence equals the H13 prediction.

```powershell
D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\analysis\state_distinction\explain_distinction.py simulate `
  --dot "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\evidence\hypotheses\hypothesis_13.dot" `
  --sequence registrationRequestGUTI identityResponse deregistrationRequest identityResponse registrationRequest registrationRequest identityResponse deregistrationAccept registrationRequest `
  --output-dir "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\analysis\derived" `
  --basename hypothesis_13_failure_query

D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\evidence\hypotheses\hypothesis_13.dot" `
  --simplify --formats svg,pdf `
  --output-dir "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\analysis\derived" `
  --basename hypothesis_13_failure_query `
  --trace-json "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\analysis\derived\hypothesis_13_failure_query_simulation.json" `
  --comparison-json "D:\state-learning-lab\projects\state-learning-experiments\failures\free5gc\free5gc421-ttt-inconsistency-20260723-r14\analysis\derived\hypothesis_13_failure_query_observations.json"
```
