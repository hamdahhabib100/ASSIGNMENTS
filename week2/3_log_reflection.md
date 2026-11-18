# 3 — Cleaning log and reflection

## Source and baseline
The supplied file contains 97 rows and 6 columns. Its `data_label` states: “ILLUSTRATIVE TEACHING DATA - NOT ACTUAL PBS OBSERVATIONS.” It must not be described as official PBS observations.

SHA-256 of the supplied CSV: `5818dad3807487d4e37f764e5d19cbaa152edd78fe4db182eb28b087c8709d35`

## Data-quality findings
The inspection identified:
1. One missing `price` value for Pulse C, City 2, dated 2026-01-01.
2. One exact duplicate record (Staple A, City 1, 2026-01-08, price 99.5).
3. One anomalous item label, `oil b`, on 2026-02-05 for City 1 with price 108.5. It should be flagged, not automatically changed: the surrounding pattern suggests a possible mislabel, but the CSV alone cannot prove the intended item.
4. Two prices that need source verification: `1,250` (Staple A, City 2, 2026-01-15) and `9999` (Sweetener D, City 1, 2026-01-22).
5. The `price` column is read as text because it contains a comma-formatted value and a missing entry.

## AI-code court: proposed cleaning actions

| Proposed action | Verdict | Evidence / reason |
|---|---|---|
| Parse `date` as datetime | KEEP | Dates follow a consistent YYYY-MM-DD pattern; datetime enables validation and sorting. |
| Convert price text to numeric after removing commas | MODIFY | Convert `1,250` to numeric 1250, but preserve the original `price` text in `price_raw` and flag the value for review. |
| Drop exact duplicate rows | KEEP | One exact duplicate is present; removing one copy prevents double counting. |
| Fill missing price with the mean | REJECT | No source evidence supports an estimated price. Keep it missing and report it. |
| Replace all prices above 1000 automatically | REJECT | A threshold is only a review flag, not proof of the correct value. Do not guess a replacement. |
| Delete all unusually high prices | REJECT | Deletion would erase potentially valid or erroneous observations without verification. Retain and flag both. |
| Change `oil b` to `Oil B` | REJECT | The value 108.5 does not fit the Oil B price series and the intended label cannot be proven from this file. Preserve and flag it. |
| Drop the `unit` and `data_label` columns | REJECT | They provide important context, especially the illustrative-data warning. |

## Cleaning policy used
- Preserve the original CSV unchanged.
- Remove one exact duplicate only.
- Convert dates to datetime.
- Keep the original price text in `price_raw`; parse a numeric `price_num` for analysis.
- Preserve the anomalous `oil b` label and flag it for verification.
- Leave the missing price missing.
- Flag 1250 and 9999 for manual verification; do not replace or delete them.
- Keep all six source fields or their preserved equivalents.

## Before/after evidence
Run `2_work.ipynb`. It prints row counts, missing counts, duplicate counts, price summaries, flagged records and expected date-item-city coverage. Expected coverage is 12 dates × 4 items × 2 cities = 96 unique combinations. After removing one exact duplicate, 96 rows remain. One item label is anomalous, so the observed keys include one unexpected label and one expected combination is absent. The missing price remains missing, and the two unusual numeric prices remain flagged.

## Reflection :
The most important difference between cautious cleaning and cosmetic cleaning is that a tidy-looking dataset is not necessarily a truthful dataset. A missing price should not be filled without evidence. An unusual value should be investigated rather than automatically deleted or corrected. The duplicate can be removed because it is an exact repeated record. The `oil b` label should not be silently normalized: its associated price appears inconsistent with the Oil B series, and the source alone cannot confirm the intended item.

AI was useful for suggesting checks and structuring code, but its suggestions still needed judgment. In particular, automatically imputing the missing value or deleting high prices would have hidden uncertainty. Without the audit, I could have produced a cleaner-looking table while losing information or making unsupported assumptions.

**Before submission:** Replace the reflection above with your own honest account of which decisions you personally made, what you verified by running the notebook, and what you learned.
