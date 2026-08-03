# hypothesis_14_extra 额外环路线报告

## 输入与约束

- SMP 目标：`hypothesis_14_smp.dot`（完整路径和 SHA-256 见 JSON）
- 原始闭环 DOT：`hypothesis_14.dot`（完整路径和 SHA-256 见 JSON）
- 信令约束：`output-only`
- 具体路线：46；序列行：70
- 表格对长路线使用 `→` 分隔；消息对在 `/` 后换行，避免撑宽列。

## 路线与序列

| ID | 类型 | 长度 | 覆盖目标边 | 序列行 | SVG |
|---|---|---:|---|---:|---|
| X001 | extra_short_cycle | 3 | E001, E003 | 1–1 | [SVG](cycles/hypothesis_14_extra_X001_len03.svg) |
| X002 | extra_short_cycle | 3 | E002, E011 | 2–3 | [SVG](cycles/hypothesis_14_extra_X002_len03.svg) |
| X003 | extra_short_cycle | 3 | E003, E006, E007 | 4–4 | [SVG](cycles/hypothesis_14_extra_X003_len03.svg) |
| X004 | extra_short_cycle | 3 | E012, E015, E018 | 5–5 | [SVG](cycles/hypothesis_14_extra_X004_len03.svg) |
| X005 | extra_short_cycle | 3 | E020, E021, E035 | 6–6 | [SVG](cycles/hypothesis_14_extra_X005_len03.svg) |
| X006 | extra_short_cycle | 3 | E023, E025, E027 | 7–8 | [SVG](cycles/hypothesis_14_extra_X006_len03.svg) |
| X007 | extra_short_cycle | 4 | E001, E003, E006 | 9–9 | [SVG](cycles/hypothesis_14_extra_X007_len04.svg) |
| X008 | extra_short_cycle | 4 | E001, E003, E005 | 10–10 | [SVG](cycles/hypothesis_14_extra_X008_len04.svg) |
| X009 | extra_short_cycle | 4 | E002, E003, E011 | 11–12 | [SVG](cycles/hypothesis_14_extra_X009_len04.svg) |
| X010 | extra_short_cycle | 4 | E003, E005, E011, E013 | 13–14 | [SVG](cycles/hypothesis_14_extra_X010_len04.svg) |
| X011 | extra_short_cycle | 4 | E003, E005, E012, E014 | 15–16 | [SVG](cycles/hypothesis_14_extra_X011_len04.svg) |
| X012 | extra_short_cycle | 5 | E001, E003, E005, E013 | 17–17 | [SVG](cycles/hypothesis_14_extra_X012_len05.svg) |
| X013 | extra_short_cycle | 5 | E001, E003, E005, E012 | 18–18 | [SVG](cycles/hypothesis_14_extra_X013_len05.svg) |
| X014 | extra_short_cycle | 5 | E002, E003, E006, E011 | 19–20 | [SVG](cycles/hypothesis_14_extra_X014_len05.svg) |
| X015 | extra_short_cycle | 5 | E002, E003, E005, E011 | 21–22 | [SVG](cycles/hypothesis_14_extra_X015_len05.svg) |
| X016 | extra_short_cycle | 5 | E008, E010, E023, E025, E029 | 23–24 | [SVG](cycles/hypothesis_14_extra_X016_len05.svg) |
| I001 | extra_embedded_self_loop | 3 | E001, E003 | 25–25 | [SVG](cycles/hypothesis_14_extra_I001_len03.svg) |
| I002 | extra_embedded_self_loop | 3 | E001, E003 | 26–26 | [SVG](cycles/hypothesis_14_extra_I002_len03.svg) |
| I003 | extra_embedded_self_loop | 3 | E002, E011 | 27–28 | [SVG](cycles/hypothesis_14_extra_I003_len03.svg) |
| I004 | extra_embedded_self_loop | 3 | E002, E011 | 29–30 | [SVG](cycles/hypothesis_14_extra_I004_len03.svg) |
| I005 | extra_embedded_self_loop | 3 | E003, E006, E007 | 31–31 | [SVG](cycles/hypothesis_14_extra_I005_len03.svg) |
| I006 | extra_embedded_self_loop | 3 | E003, E006, E007 | 32–32 | [SVG](cycles/hypothesis_14_extra_I006_len03.svg) |
| I007 | extra_embedded_self_loop | 3 | E020, E021, E035 | 33–33 | [SVG](cycles/hypothesis_14_extra_I007_len03.svg) |
| I008 | extra_embedded_self_loop | 3 | E020, E021, E035 | 34–34 | [SVG](cycles/hypothesis_14_extra_I008_len03.svg) |
| I009 | extra_embedded_self_loop | 3 | E023, E025, E027 | 35–36 | [SVG](cycles/hypothesis_14_extra_I009_len03.svg) |
| I010 | extra_embedded_self_loop | 3 | E023, E025, E027 | 37–38 | [SVG](cycles/hypothesis_14_extra_I010_len03.svg) |
| I011 | extra_embedded_self_loop | 4 | E001, E003, E006 | 39–39 | [SVG](cycles/hypothesis_14_extra_I011_len04.svg) |
| I012 | extra_embedded_self_loop | 4 | E001, E003, E006 | 40–40 | [SVG](cycles/hypothesis_14_extra_I012_len04.svg) |
| I013 | extra_embedded_self_loop | 4 | E001, E003, E005 | 41–41 | [SVG](cycles/hypothesis_14_extra_I013_len04.svg) |
| I014 | extra_embedded_self_loop | 4 | E001, E003, E005 | 42–42 | [SVG](cycles/hypothesis_14_extra_I014_len04.svg) |
| I015 | extra_embedded_self_loop | 4 | E002, E003, E011 | 43–44 | [SVG](cycles/hypothesis_14_extra_I015_len04.svg) |
| I016 | extra_embedded_self_loop | 4 | E002, E003, E011 | 45–46 | [SVG](cycles/hypothesis_14_extra_I016_len04.svg) |
| I017 | extra_embedded_self_loop | 4 | E003, E005, E011, E013 | 47–48 | [SVG](cycles/hypothesis_14_extra_I017_len04.svg) |
| I018 | extra_embedded_self_loop | 4 | E003, E005, E011, E013 | 49–50 | [SVG](cycles/hypothesis_14_extra_I018_len04.svg) |
| I019 | extra_embedded_self_loop | 4 | E003, E005, E012, E014 | 51–52 | [SVG](cycles/hypothesis_14_extra_I019_len04.svg) |
| I020 | extra_embedded_self_loop | 4 | E003, E005, E012, E014 | 53–54 | [SVG](cycles/hypothesis_14_extra_I020_len04.svg) |
| I021 | extra_embedded_self_loop | 5 | E001, E003, E005, E013 | 55–55 | [SVG](cycles/hypothesis_14_extra_I021_len05.svg) |
| I022 | extra_embedded_self_loop | 5 | E001, E003, E005, E013 | 56–56 | [SVG](cycles/hypothesis_14_extra_I022_len05.svg) |
| I023 | extra_embedded_self_loop | 5 | E001, E003, E005, E012 | 57–57 | [SVG](cycles/hypothesis_14_extra_I023_len05.svg) |
| I024 | extra_embedded_self_loop | 5 | E001, E003, E005, E012 | 58–58 | [SVG](cycles/hypothesis_14_extra_I024_len05.svg) |
| I025 | extra_embedded_self_loop | 5 | E002, E003, E006, E011 | 59–60 | [SVG](cycles/hypothesis_14_extra_I025_len05.svg) |
| I026 | extra_embedded_self_loop | 5 | E002, E003, E006, E011 | 61–62 | [SVG](cycles/hypothesis_14_extra_I026_len05.svg) |
| I027 | extra_embedded_self_loop | 5 | E002, E003, E005, E011 | 63–64 | [SVG](cycles/hypothesis_14_extra_I027_len05.svg) |
| I028 | extra_embedded_self_loop | 5 | E002, E003, E005, E011 | 65–66 | [SVG](cycles/hypothesis_14_extra_I028_len05.svg) |
| I029 | extra_embedded_self_loop | 5 | E008, E010, E023, E025, E029 | 67–68 | [SVG](cycles/hypothesis_14_extra_I029_len05.svg) |
| I030 | extra_embedded_self_loop | 5 | E008, E010, E023, E025, E029 | 69–70 | [SVG](cycles/hypothesis_14_extra_I030_len05.svg) |
