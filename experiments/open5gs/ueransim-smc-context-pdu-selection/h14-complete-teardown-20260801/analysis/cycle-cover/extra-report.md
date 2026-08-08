# hypothesis_14_extra 额外环路线报告

## 输入与约束

- SMP 目标：`smp.dot`（完整路径和 SHA-256 见 JSON）
- 原始闭环 DOT：`hypothesis_14.dot`（完整路径和 SHA-256 见 JSON）
- 信令约束：`output-only`
- 具体路线：46；序列行：70
- 表格对长路线使用 `→` 分隔；消息对在 `/` 后换行，避免撑宽列。

## 路线与序列

| ID | 类型 | 长度 | 覆盖目标边 | 序列行 | SVG |
|---|---|---:|---|---:|---|
| X001 | extra_short_cycle | 3 | E001, E003 | 1–1 | [SVG](extra-X001-len03.svg) |
| X002 | extra_short_cycle | 3 | E002, E011 | 2–3 | [SVG](extra-X002-len03.svg) |
| X003 | extra_short_cycle | 3 | E003, E006, E007 | 4–4 | [SVG](extra-X003-len03.svg) |
| X004 | extra_short_cycle | 3 | E012, E015, E018 | 5–5 | [SVG](extra-X004-len03.svg) |
| X005 | extra_short_cycle | 3 | E020, E021, E035 | 6–6 | [SVG](extra-X005-len03.svg) |
| X006 | extra_short_cycle | 3 | E023, E025, E027 | 7–8 | [SVG](extra-X006-len03.svg) |
| X007 | extra_short_cycle | 4 | E001, E003, E006 | 9–9 | [SVG](extra-X007-len04.svg) |
| X008 | extra_short_cycle | 4 | E001, E003, E005 | 10–10 | [SVG](extra-X008-len04.svg) |
| X009 | extra_short_cycle | 4 | E002, E003, E011 | 11–12 | [SVG](extra-X009-len04.svg) |
| X010 | extra_short_cycle | 4 | E003, E005, E011, E013 | 13–14 | [SVG](extra-X010-len04.svg) |
| X011 | extra_short_cycle | 4 | E003, E005, E012, E014 | 15–16 | [SVG](extra-X011-len04.svg) |
| X012 | extra_short_cycle | 5 | E001, E003, E005, E013 | 17–17 | [SVG](extra-X012-len05.svg) |
| X013 | extra_short_cycle | 5 | E001, E003, E005, E012 | 18–18 | [SVG](extra-X013-len05.svg) |
| X014 | extra_short_cycle | 5 | E002, E003, E006, E011 | 19–20 | [SVG](extra-X014-len05.svg) |
| X015 | extra_short_cycle | 5 | E002, E003, E005, E011 | 21–22 | [SVG](extra-X015-len05.svg) |
| X016 | extra_short_cycle | 5 | E008, E010, E023, E025, E029 | 23–24 | [SVG](extra-X016-len05.svg) |
| I001 | extra_embedded_self_loop | 3 | E001, E003 | 25–25 | [SVG](extra-I001-len03.svg) |
| I002 | extra_embedded_self_loop | 3 | E001, E003 | 26–26 | [SVG](extra-I002-len03.svg) |
| I003 | extra_embedded_self_loop | 3 | E002, E011 | 27–28 | [SVG](extra-I003-len03.svg) |
| I004 | extra_embedded_self_loop | 3 | E002, E011 | 29–30 | [SVG](extra-I004-len03.svg) |
| I005 | extra_embedded_self_loop | 3 | E003, E006, E007 | 31–31 | [SVG](extra-I005-len03.svg) |
| I006 | extra_embedded_self_loop | 3 | E003, E006, E007 | 32–32 | [SVG](extra-I006-len03.svg) |
| I007 | extra_embedded_self_loop | 3 | E020, E021, E035 | 33–33 | [SVG](extra-I007-len03.svg) |
| I008 | extra_embedded_self_loop | 3 | E020, E021, E035 | 34–34 | [SVG](extra-I008-len03.svg) |
| I009 | extra_embedded_self_loop | 3 | E023, E025, E027 | 35–36 | [SVG](extra-I009-len03.svg) |
| I010 | extra_embedded_self_loop | 3 | E023, E025, E027 | 37–38 | [SVG](extra-I010-len03.svg) |
| I011 | extra_embedded_self_loop | 4 | E001, E003, E006 | 39–39 | [SVG](extra-I011-len04.svg) |
| I012 | extra_embedded_self_loop | 4 | E001, E003, E006 | 40–40 | [SVG](extra-I012-len04.svg) |
| I013 | extra_embedded_self_loop | 4 | E001, E003, E005 | 41–41 | [SVG](extra-I013-len04.svg) |
| I014 | extra_embedded_self_loop | 4 | E001, E003, E005 | 42–42 | [SVG](extra-I014-len04.svg) |
| I015 | extra_embedded_self_loop | 4 | E002, E003, E011 | 43–44 | [SVG](extra-I015-len04.svg) |
| I016 | extra_embedded_self_loop | 4 | E002, E003, E011 | 45–46 | [SVG](extra-I016-len04.svg) |
| I017 | extra_embedded_self_loop | 4 | E003, E005, E011, E013 | 47–48 | [SVG](extra-I017-len04.svg) |
| I018 | extra_embedded_self_loop | 4 | E003, E005, E011, E013 | 49–50 | [SVG](extra-I018-len04.svg) |
| I019 | extra_embedded_self_loop | 4 | E003, E005, E012, E014 | 51–52 | [SVG](extra-I019-len04.svg) |
| I020 | extra_embedded_self_loop | 4 | E003, E005, E012, E014 | 53–54 | [SVG](extra-I020-len04.svg) |
| I021 | extra_embedded_self_loop | 5 | E001, E003, E005, E013 | 55–55 | [SVG](extra-I021-len05.svg) |
| I022 | extra_embedded_self_loop | 5 | E001, E003, E005, E013 | 56–56 | [SVG](extra-I022-len05.svg) |
| I023 | extra_embedded_self_loop | 5 | E001, E003, E005, E012 | 57–57 | [SVG](extra-I023-len05.svg) |
| I024 | extra_embedded_self_loop | 5 | E001, E003, E005, E012 | 58–58 | [SVG](extra-I024-len05.svg) |
| I025 | extra_embedded_self_loop | 5 | E002, E003, E006, E011 | 59–60 | [SVG](extra-I025-len05.svg) |
| I026 | extra_embedded_self_loop | 5 | E002, E003, E006, E011 | 61–62 | [SVG](extra-I026-len05.svg) |
| I027 | extra_embedded_self_loop | 5 | E002, E003, E005, E011 | 63–64 | [SVG](extra-I027-len05.svg) |
| I028 | extra_embedded_self_loop | 5 | E002, E003, E005, E011 | 65–66 | [SVG](extra-I028-len05.svg) |
| I029 | extra_embedded_self_loop | 5 | E008, E010, E023, E025, E029 | 67–68 | [SVG](extra-I029-len05.svg) |
| I030 | extra_embedded_self_loop | 5 | E008, E010, E023, E025, E029 | 69–70 | [SVG](extra-I030-len05.svg) |
