# Rendering record

## `hypothesis_23_smp.svg`

- Purpose: SMP presentation derivative of the latest exported hypothesis for forthcoming state-refinement and AB-cluster work; it is not original model evidence.
- Source: `evidence/hypotheses/hypothesis_23.dot`
- Source SHA-256: `e0ff218edf17bcbc8af9cf5a4a07d530c3ed62ad0ffab45e150e9d475109d6e2`
- Derived DOT: `analysis/derived/hypothesis_23_smp.dot`
- SVG: `analysis/derived/hypothesis_23_smp.svg`
- SVG SHA-256: `5a38c0c6b7c283308075a4ed640c5ceebb2badeb9c12036b476a811365389e69`
- SVG bytes: 44,482
- Renderer: `C:\\Program Files\\Graphviz\\bin\\dot.EXE` (engine `dot`)

Command:

```powershell
& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  evidence\hypotheses\hypothesis_23.dot --simplify --formats svg --output-dir analysis\derived
```

The `--simplify` transformation creates a separate derivative: it removes configured presentation-only edges and merges compatible transitions.  The original DOT remains unchanged under `evidence/hypotheses/`.

## H23 refinement diagrams

- Purpose: complete per-round classification and refinement-provenance presentation derived from the canonical H23 refinement JSON.
- Canonical source: `analysis/refinement/hypothesis_23/hypothesis_23_refinement.json`
- Canonical JSON SHA-256: `48d0f62835f9b6c4481ac8f7ee37a12f45f3b74020cf668e64f4f34472553d37`
- Per-round DOT/SVG outputs: `analysis/refinement/hypothesis_23/rounds/`
- Canonical all-round flowchart DOT: `analysis/refinement/hypothesis_23/hypothesis_23_round_refinement_flowchart.dot` (SHA-256 `1c1093eed0387e5fc8eddb70d15d22c7a909413e9754bbebaf4bd6377eb549e3`)
- Canonical all-round flowchart SVG: `analysis/refinement/hypothesis_23/hypothesis_23_round_refinement_flowchart.svg` (SHA-256 `4400e2979e8c77c154285c1206d1bc792c2ae3630b7094f25413136461705f1b`)
- Canonical all-round flowchart PDF: `analysis/refinement/hypothesis_23/hypothesis_23_round_refinement_flowchart.pdf` (SHA-256 `3c57be0503189ff36c68756befd2cfb68d2f4902ff6cfe48818b3d6d99c69545`)
- Strict binary-backtrace schema-v2 audit JSON: `analysis/refinement/hypothesis_23/backtrace/hypothesis_23_all_binary_backtrace.json` (SHA-256 `c446620e4fae38eae48fde8105201e54365ab54e966832f4a0a9adcdcfeeb012`)
- Integrated binary-backtrace report: `analysis/refinement/hypothesis_23/backtrace/hypothesis_23_all_binary_backtrace_report.md` (SHA-256 `013d498ccc7b33ffb46ced5d78e0369dfd557e0872e89db897623ee710bcbf7e`)
- Binary-backtrace DOT: `analysis/refinement/hypothesis_23/backtrace/hypothesis_23_all_binary_backtrace_flowchart.dot` (SHA-256 `3de37d4a3b49deb1f15f6785c04a0adfb604fd1cefbbce4923d004927d9119f9`)
- Binary-backtrace SVG: `analysis/refinement/hypothesis_23/backtrace/hypothesis_23_all_binary_backtrace_flowchart.svg` (SHA-256 `e04504a74f6d8dd4fc83b7e763cc4523ea6202759a78bc0dc797c983fd3e6aab`)
- Binary-backtrace PDF: `analysis/refinement/hypothesis_23/backtrace/hypothesis_23_all_binary_backtrace_flowchart.pdf` (SHA-256 `7616d9277635deb9d87173ccc0a453a8f259671123fc88cb8d2833a30cd3946d`)
- Renderer: `C:\\Program Files\\Graphviz\\bin\\dot.EXE`

Command:

```powershell
& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\classification\iterative_refinement\analyze_refinement.py `
  --dot evidence\hypotheses\hypothesis_23.dot `
  --refinement-json analysis\refinement\hypothesis_23\hypothesis_23_refinement.json `
  --output-dir analysis\refinement\hypothesis_23 `
  --flowchart-only --formats dot,svg,pdf
```

The all-round flowchart uses the H33 full-round table layout.  It preserves the
complete `16, 20, 25, 27` class partitions, aligns the `A, D, N, NG, S, R, X`
families by final descendants, and contains `20, 25, 27` uniquely parented
relations across the three round transitions.  `--flowchart-only` leaves the
canonical JSON and Markdown report unchanged.

Binary-backtrace command:

```powershell
& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\classification\iterative_backtrace\trace_binary_splits.py `
  --dot evidence\hypotheses\hypothesis_23.dot `
  --refinement-json analysis\refinement\hypothesis_23\hypothesis_23_refinement.json `
  --output-dir analysis\refinement\hypothesis_23\backtrace `
  --entry-policy strict --formats dot,svg,pdf
```

The per-round DOT files contain presentation-level partition and provenance edges, rather than Mealy transitions, so they are rendered directly with Graphviz to preserve every semantic edge.  Their state membership and signature-difference labels are derived solely from the canonical refinement JSON; they are analytical derivatives, not raw DOT evidence.

## H23 single-entry backtrace overlays

- Purpose: place one strict independent-entry backtrace over the existing SMP
  SVG without changing its layout, labels, arrows, viewBox, or base bytes.
- Original DOT SHA-256: `e0ff218edf17bcbc8af9cf5a4a07d530c3ed62ad0ffab45e150e9d475109d6e2`
- SMP DOT SHA-256: `66628d3d6779477f964edc1667e900b89db094825499018021eabd117caf1bf6`
- SMP SVG SHA-256: `5a38c0c6b7c283308075a4ed640c5ceebb2badeb9c12036b476a811365389e69`
- Backtrace schema-v2 JSON SHA-256: `c446620e4fae38eae48fde8105201e54365ab54e966832f4a0a9adcdcfeeb012`
- B01 SVG/PDF/audit JSON SHA-256:
  `0566749241fb40f23d645bc52a2b3aa8f9100042bf6d227164b67b513ce36759`,
  `c8023875cd69b17dd0513c90e4a4c4f92ff0377d0e89b3ccb6ba4b574c56bece`,
  `861fb9532306519be05eccf941dbc6f6032d28859ee4e996a2fc44df69590d7d`.
- B02 SVG/PDF/audit JSON SHA-256:
  `d07bf1471bdec41f3b5a1c9491d5cfb73896a3513515c3e671bab94bccca0657`,
  `7cd9429054753ec4722c8208f45da12f24f8547bcbbaf44b725a55e751252fb6`,
  `ca1ed8d83bfb6c35fec70cf83136205b04f38005ff4419406fa8679320fa6b66`.

Commands:

```powershell
& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_backtrace_overlay\render_backtrace_overlay.py `
  --dot evidence\hypotheses\hypothesis_23.dot `
  --base-dot analysis\derived\hypothesis_23_smp.dot `
  --base-svg analysis\derived\hypothesis_23_smp.svg `
  --backtrace-json analysis\refinement\hypothesis_23\backtrace\hypothesis_23_all_binary_backtrace.json `
  --entry B01 --output-dir analysis\derived\backtrace-paths --formats svg,pdf

& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_backtrace_overlay\render_backtrace_overlay.py `
  --dot evidence\hypotheses\hypothesis_23.dot `
  --base-dot analysis\derived\hypothesis_23_smp.dot `
  --base-svg analysis\derived\hypothesis_23_smp.svg `
  --backtrace-json analysis\refinement\hypothesis_23\backtrace\hypothesis_23_all_binary_backtrace.json `
  --entry B02 --output-dir analysis\derived\backtrace-paths --formats svg,pdf
```

Both entries contain eight concrete transitions. B01 restores the non-sink
`s12 -> s12` self-loop; B02 restores `s8 -> s0` from the reverse base-edge
geometry and the non-sink `s27 -> s27` self-loop. Removing the marked overlay
fragment restores the SMP SVG byte-for-byte. Inkscape 1.4.4 exports each final
SVG to a one-page `4888 x 1193 pt` PDF; rendered PNG inspection found no new
clipping or canvas changes.
