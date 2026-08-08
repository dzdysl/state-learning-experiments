# hypothesis_14_base 基础环路线报告

## 输入与约束

- SMP 目标：`smp.dot`（完整路径和 SHA-256 见 JSON）
- 原始闭环 DOT：`hypothesis_14.dot`（完整路径和 SHA-256 见 JSON）
- 信令约束：`output-only`
- 具体路线：23；序列行：37
- 表格对长路线使用 `→` 分隔；消息对在 `/` 后换行，避免撑宽列。

## 路线与序列

| ID | 类型 | 长度 | 覆盖目标边 | 序列行 | SVG |
|---|---|---:|---|---:|---|
| S002 | base_simple_cover | 2 | E003, E004 | 1–2 | [SVG](base-S002-len02.svg) |
| S003 | base_simple_cover | 2 | E019, E021 | 3–3 | [SVG](base-S003-len02.svg) |
| S004 | base_simple_cover | 2 | E020, E034 | 4–5 | [SVG](base-S004-len02.svg) |
| S005 | base_simple_cover | 2 | E023, E024 | 6–7 | [SVG](base-S005-len02.svg) |
| S006 | base_simple_cover | 2 | E025, E026 | 8–9 | [SVG](base-S006-len02.svg) |
| S008 | base_simple_cover | 3 | E002, E011 | 10–11 | [SVG](base-S008-len03.svg) |
| S009 | base_simple_cover | 3 | E003, E006, E007 | 12–12 | [SVG](base-S009-len03.svg) |
| S010 | base_simple_cover | 3 | E012, E015, E018 | 13–13 | [SVG](base-S010-len03.svg) |
| S012 | base_simple_cover | 3 | E023, E025, E027 | 14–15 | [SVG](base-S012-len03.svg) |
| S017 | base_simple_cover | 4 | E003, E005, E012, E014 | 16–17 | [SVG](base-S017-len04.svg) |
| S018 | base_simple_cover | 5 | E001, E003, E005, E013 | 18–18 | [SVG](base-S018-len05.svg) |
| S022 | base_simple_cover | 5 | E008, E010, E023, E025, E029 | 19–20 | [SVG](base-S022-len05.svg) |
| S036 | base_simple_cover | 9 | E003, E006, E008, E010, E011, E023, E025, E028, E031 | 21–24 | [SVG](base-S036-len09.svg) |
| S037 | base_simple_cover | 9 | E003, E005, E012, E015, E016, E020, E022, E032, E035 | 25–28 | [SVG](base-S037-len09.svg) |
| S039 | base_simple_cover | 9 | E003, E005, E012, E015, E017, E020, E021, E030, E036 | 29–29 | [SVG](base-S039-len09.svg) |
| F001 | base_fallback | 4 | E019, E021, E022, E033 | 30–30 | [SVG](base-F001-len04.svg) |
| F002 | base_fallback | 5 | E003, E006, E007, E008, E009 | 31–31 | [SVG](base-F002-len05.svg) |
| L001 | base_standalone_self_loop | 1 | — | 32–32 | [SVG](base-L001-len01.svg) |
| L002 | base_standalone_self_loop | 1 | — | 33–33 | [SVG](base-L002-len01.svg) |
| L003 | base_standalone_self_loop | 1 | — | 34–34 | [SVG](base-L003-len01.svg) |
| L004 | base_standalone_self_loop | 1 | — | 35–35 | [SVG](base-L004-len01.svg) |
| L005 | base_standalone_self_loop | 1 | — | 36–36 | [SVG](base-L005-len01.svg) |
| L006 | base_standalone_self_loop | 1 | — | 37–37 | [SVG](base-L006-len01.svg) |

## 基础 fallback

- 仅以下残余目标边由复合闭合游走覆盖：E009, E033
