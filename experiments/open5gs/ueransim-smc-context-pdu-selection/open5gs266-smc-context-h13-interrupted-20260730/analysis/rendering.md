# Rendering record

## `hypothesis_13_smp.svg`

- Purpose: SMP presentation derivative of the latest exported intermediate
  hypothesis. It is analysis output, not original evidence.
- Source: `evidence/hypotheses/hypothesis_13.dot`
- Source SHA-256:
  `465fc8f4e5cba3715ec8745cbc81f9d24f5876fdb37d455f858f02f01815523a`
- Source size: 12,626 bytes; 17 state nodes; 204 transitions.
- Derived DOT: `analysis/derived/hypothesis_13_smp.dot`
- Derived DOT SHA-256:
  `4fe68f3e8836d86c3b77c2c8c4671ff4b437690eeaf2870084764ab66027797f`
- Derived presentation: 17 state nodes; 33 transitions.
- SVG: `analysis/derived/hypothesis_13_smp.svg`
- SVG SHA-256:
  `b994de6d85c0ada52a7c786c47c5cba66509c3560851bab8f7aaa08c36d84c19`
- SVG bytes: 24,089.
- Renderer: `C:\Program Files\Graphviz\bin\dot.EXE` (engine `dot`).

Command:

```powershell
& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  evidence\hypotheses\hypothesis_13.dot `
  --simplify --formats svg --output-dir analysis\derived
```

The simplification writes a separate `_smp.dot`, removes configured
presentation-only edges, and merges compatible transitions. The evidence DOT
is unchanged. A temporary PNG generated from the derived DOT was visually
inspected at full resolution: all 17 states are present and no canvas
clipping was observed. The temporary QA PNG was removed.

## `hypothesis_13_round_refinement_flowchart.svg`

- Purpose: complete H13 round-by-round state-split diagram derived from the
  canonical refinement JSON.
- Source: `evidence/hypotheses/hypothesis_13.dot`
- Source SHA-256:
  `465fc8f4e5cba3715ec8745cbc81f9d24f5876fdb37d455f858f02f01815523a`
- Canonical JSON:
  `analysis/derived/refinement/hypothesis_13_refinement.json`
- JSON SHA-256:
  `60ebe9a49035bffeee5d73fb733129c62e5796b70ec68ea76fe357f1238e082b`
- Editable DOT:
  `analysis/derived/refinement/hypothesis_13_round_refinement_flowchart.dot`
- DOT SHA-256:
  `e42e67387f400d5bc6f30fbce2823d5003e6db833aec4a4ed7176ad4fcb8de23`
- SVG SHA-256:
  `905a9368af71ef0a533add68b6f6dc974b5940b485a11ffe52cef66432e0693b`
- PDF SHA-256:
  `27f882bcbbef3da74844111804583762edc71654f272aad86285d2053cb3f036`
- Effective class counts: 10, 13, 16, 17; convergence confirmed in round 5.

Command:

```powershell
& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\classification\iterative_refinement\analyze_refinement.py `
  --dot evidence\hypotheses\hypothesis_13.dot `
  --output-dir analysis\derived\refinement `
  --basename hypothesis_13 `
  --profile open5gs-nas `
  --formats dot,svg,pdf
```

The SVG was visually inspected through a temporary full-resolution PNG. All
four effective rounds, 17 final singleton classes, family legend, and
split/renumber/unchanged relations are visible without canvas clipping. The
temporary QA image was removed.

## `hypothesis_13_all_binary_backtrace_flowchart.svg`

- Purpose: integrated H13 binary child-class backtrace under the strict entry
  policy.
- Source DOT SHA-256:
  `465fc8f4e5cba3715ec8745cbc81f9d24f5876fdb37d455f858f02f01815523a`
- Refinement JSON:
  `analysis/derived/refinement/hypothesis_13_refinement.json`
- Audit JSON:
  `analysis/derived/iterative_backtrace/hypothesis_13_all_binary_backtrace.json`
- Audit JSON SHA-256:
  `5ca5a7e58cda484e00bd841e7009eac35149621d06750796691adb58e0963db8`
- Editable DOT SHA-256:
  `5cdc6d0c847438147b52a97dd4cff7b333d22ee675645ba8cedd145e7b811c98`
- SVG SHA-256:
  `c62095e41930105842987c26a83234b897b98d4f233134db820e1c59cb6a6e0b`
- PDF SHA-256:
  `7515e86c868b24b2ca2b1e6594a083dcf1176690bea741243fa15e162c6edc86`
- Audit summary: 12 binary pairs, 4 independent entries, 4 covered nodes,
  and 2 terminal initial-key differences.

Command:

```powershell
& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\classification\iterative_backtrace\trace_binary_splits.py `
  --dot evidence\hypotheses\hypothesis_13.dot `
  --refinement-json analysis\derived\refinement\hypothesis_13_refinement.json `
  --output-dir analysis\derived\iterative_backtrace `
  --basename hypothesis_13 `
  --entry-policy strict `
  --formats dot,svg,pdf
```

The SVG was visually inspected through a temporary full-resolution PNG. All
four effective split rounds, participating and non-entry parent tables,
terminal key differences, signal legend, and connecting relations are visible
without clipping. The temporary QA image was removed.

## H13 exact minimum cycle/closed-walk cover

- Purpose: exact output-signal-constrained cover of all 33 H13 SMP target
  edges. Simple cycles alone leave E009 and E033 uncovered, so the analyzer
  uses the approved fallback of joining two simple cycles at a shared state to
  form a closed walk. The original H13 graph is used only to supply closure
  transitions after excluding `s2` and the synthetic start.
- SMP target: `analysis/derived/hypothesis_13_smp.dot`
- SMP target SHA-256:
  `4fe68f3e8836d86c3b77c2c8c4671ff4b437690eeaf2870084764ab66027797f`
- Closure source: `evidence/hypotheses/hypothesis_13.dot`
- Closure source SHA-256:
  `465fc8f4e5cba3715ec8745cbc81f9d24f5876fdb37d455f858f02f01815523a`
- Analyzer:
  `D:\state-learning-lab\projects\state-learning-tools\analysis\cycle_cover\analyze_cycle_cover.py`
- Analyzer SHA-256:
  `0bdb14c20ea6a63b1b7fdf3fae1d48fab8470e7a0c87890954ea9d855a12b59c`
- Canonical result:
  `analysis/derived/cycle_cover/hypothesis_13_cycle_cover.json`
- Result JSON SHA-256:
  `48c03c22972d34a1404107cefc6f98b0f013e5dac981c0c43c6d1d20ca0cfe13`
- Human-readable report:
  `analysis/derived/cycle_cover/hypothesis_13_cycle_cover_report.md`
- Report SHA-256:
  `ba67a04baca58ab5f5bd6770cf351fe2096f2a54973039d33bbf3ed9a113b2af`
- Executable sequence output:
  `inputs/hypothesis_13_cycle_cover_repeat10.seq`
- Sequence SHA-256:
  `31a645f38976e61953c20c3ba99dc5af137e93926a2a6844d41f500b4f753f20`
- Sequence construction: rotate each route to its first lowest-numbered
  state, prepend the deterministic shortest access path from `s0`, expand all
  merged inputs, and repeat each concrete route 10 times. The result has 28
  lines.
- Per-route outputs: 14 SVG files under
  `analysis/derived/cycle_cover/cycles/`, totaling 344,506 bytes. Each SVG
  retains the full SMP graph and highlights one selected route; no final DOT
  or PDF files are generated.
- Cycle-output content-tree SHA-256:
  `c3cd03edb13c170e0b3a6c83f9b21021bdce03473c50841a1ff999916ad72e6d`.
  It is computed over files sorted by POSIX relative path, hashing
  `path UTF-8 + NUL + raw SHA-256 digest + LF` for each file.
- Every per-route artifact's byte size and SHA-256 are recorded in
  `hypothesis_13_cycle_cover.json` under
  `selected_cycles[].artifact`.
- Renderer: Graphviz `dot` 13.1.2. The analyzer sets
  `SOURCE_DATE_EPOCH=0` for Graphviz subprocesses so generated metadata and
  downstream artifact hashes remain byte-deterministic.

Command:

```powershell
& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\analysis\cycle_cover\analyze_cycle_cover.py `
  --dot analysis\derived\hypothesis_13_smp.dot `
  --closure-dot evidence\hypotheses\hypothesis_13.dot `
  --exclude-state s2 `
  --required-output authenticationRequest `
  --required-output securityModeCommand `
  --signal-mode output-only `
  --output-dir analysis\derived\cycle_cover `
  --basename hypothesis_13 `
  --formats svg `
  --engine dot `
  --sequence-output inputs\hypothesis_13_cycle_cover_repeat10.seq `
  --sequence-start-state s0 `
  --sequence-repeat-count 10 `
  --sequence-merged-input-policy expand `
  --overwrite
```

The exact result contains 63 candidates and 14 selected routes; its objective
tuple is maximum length 9, route count 14, repeated transition uses 29, and
total length 64. Twelve selected routes are simple directed cycles and two
are composite closed walks. Every figure contains the full SMP (`s0` through
`s16` plus the synthetic start), one distinctly colored selected route, black
non-route SMP edges, and any required same-color dashed closure edge without
canvas clipping. The same command emits 28 validated `.seq` lines; every line
uses only single-space-delimited inputs and returns to its selected route
start after ten repetitions.
