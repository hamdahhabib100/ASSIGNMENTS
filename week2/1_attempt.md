# 1 — Initial data inspection notes

> **Transparency note:** These observations were prepared with AI assistance after the file had already been discussed in ChatGPT. They are not represented as an independent pre-AI attempt. If the assignment requires a genuine pre-AI record, do not backdate these notes; explain the timing honestly to your instructor.

## File identity
- File: `teaching_prices_messy.csv`
- Rows and columns: 97 rows × 6 columns
- SHA-256: `5818dad3807487d4e37f764e5d19cbaa152edd78fe4db182eb28b087c8709d35`

## Inspection findings

| Potential issue | Check used | What the inspection showed | Planned decision and reason |
|---|---|---|---|
| Missing values | Count missing values in each column and inspect the affected row. | One `price` value is missing: Pulse C, City 2, dated 2026-01-01. | Retain it as missing. Do not invent a price or fill it with an average without source evidence. |
| Duplicate records | Compare complete rows for exact duplicates. | One exact duplicate was found: Staple A, City 1, 2026-01-08, price 99.5. | Remove one copy only, because the full record is repeated exactly; keep an audit note. |
| Price stored as text / formatting | Inspect the `price` data type and values containing commas or blanks. | `price` is read as text; the value `1,250` uses a thousands separator and one value is blank. | Preserve the original text and create a separate numeric analysis column after removing commas. Do not overwrite the source value. |
| Unusually large prices | Convert parseable prices to numbers and inspect unusually high values. | Two values need verification: `1,250` for Staple A, City 2, 2026-01-15, and `9999` for Sweetener D, City 1, 2026-01-22. | Flag both for source checking. Do not automatically correct or delete them because they may be errors or valid observations. |
| Inconsistent item spelling/case | List distinct item labels and inspect labels that do not match the expected names. | The label `oil b` appears once, on 2026-02-05 for City 1, with price 108.5. | Preserve and flag it. Do not change it to `Oil B` automatically because the price pattern makes the intended item uncertain. |
| Date and expected record coverage | Parse dates, count unique dates, cities and items, and compare combinations with the expected structure. | The expected structure is 12 dates × 4 items × 2 cities = 96 combinations. The file has 97 rows because one row is duplicated; after removing that duplicate, 96 rows remain. The anomalous `oil b` label means one expected item-city-date combination is not represented under its expected label. | Check coverage after removing the exact duplicate. Keep the unusual label visible and report the missing expected combination rather than silently relabelling it. |

## Planned decision principles
- Never invent a missing price.
- Do not delete an unusual price just because it looks surprising; flag it and seek source verification.
- Remove only exact duplicate rows when the duplicate is confirmed.
- Normalize only harmless formatting inconsistencies, while preserving the original source value.
- Keep an audit trail of every change.
- Treat the file as illustrative teaching data, not official PBS observations, because its `data_label` explicitly says it is not actual PBS data.
