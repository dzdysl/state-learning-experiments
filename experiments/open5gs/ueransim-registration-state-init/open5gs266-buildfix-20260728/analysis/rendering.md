# Rendering record

## `hypothesis_20_smp.svg`

- Purpose: SMP presentation derivative of the latest exported intermediate
  hypothesis. It is analysis output, not original evidence.
- Source: `evidence/hypotheses/hypothesis_20.dot`
- Source SHA-256:
  `175afb542e3fd8401cd529bc6b7c611faddc5fbaf84a809a715b4d045db00bff`
- Source size: 19,460 bytes; 26 state nodes; 312 transitions.
- Derived DOT: `analysis/derived/hypothesis_20_smp.dot`
- Derived DOT SHA-256:
  `f3e6244a0fa1aec1dfa83a004733a14c8386a4e6d059b92657e1ee245823907a`
- Derived presentation: 26 state nodes; 68 transitions.
- SVG: `analysis/derived/hypothesis_20_smp.svg`
- SVG SHA-256:
  `9c94817bcb0925bc345eca1d1eed75a4bc2323e758635918d3718804ac26584f`
- SVG bytes: 46,278.
- Renderer: `C:\Program Files\Graphviz\bin\dot.EXE` (engine `dot`).

Command:

```powershell
& D:\anaconda3\python.exe D:\state-learning-lab\projects\state-learning-tools\rendering\mealy_to_pdf\render_graphviz.py `
  evidence\hypotheses\hypothesis_20.dot `
  --simplify --formats svg --output-dir analysis\derived
```

The simplification writes a separate `_smp.dot`, removes the configured
presentation-only edges, and merges compatible transitions. The evidence DOT
is unchanged. A temporary PNG generated from the derived DOT was visually
inspected at full resolution: all 26 states are present, no canvas clipping was
observed, and the graph remains intentionally wide and best viewed by zooming.
The temporary QA PNG is not part of the record.
