# Data Preparation Notes

This document records the actual steps taken to clean, transform, and model the dataset in Power Query and Power BI, in the order they were performed. It exists so the numbers in the README can be traced back to something verifiable, rather than taken on faith.

Source file: `data/product_sales_dataset_final.csv` (200,000 rows, 14 columns)

---

## 1. Column header cleanup

Several column headers imported with leading/trailing whitespace (` Unit_Price `, ` Revenue `, ` Profit `). Trimmed all headers on load so column references are consistent throughout the query.

## 2. Order_Date correction

**Issue found:** roughly 39% of rows (78,649 of 200,000) had `Order_Date` values in a corrupted format. Instead of the expected `MM-DD-YY` text (e.g. `05-25-23`), a large subset of rows appeared as a four-digit-year-looking string (e.g. `2012-11-24`) that did not correspond to any real date in the dataset's actual range.

**Root cause:** cross-checking the two patterns showed the corrupted rows were the clean rows' month and day values misread and repackaged as a false year. In other words, the original `MM-DD-YY` value had its first two digits (the true month) prefixed with "20" to fabricate a four-digit year, while the true day and year were pushed into the wrong positions. This is consistent with the file having been opened and re-saved in a spreadsheet application at some point prior to being sourced, where ambiguous two-digit date tokens got reinterpreted.

**Fix applied:** a custom column was added that detects which pattern each row is in, and reconstructs the correct date accordingly:

```
let
    txt = [Order_Date],
    isMangled = Text.Length(txt) = 10 and Text.At(txt,4) = "-" and Text.Start(txt,2) = "20",
    mm = if isMangled then Text.Middle(txt,2,2) else Text.Start(txt,2),
    dd = if isMangled then Text.Middle(txt,5,2) else Text.Middle(txt,3,2),
    yy = if isMangled then Text.End(txt,2) else Text.End(txt,2)
in
    Date.FromText("20" & yy & "-" & mm & "-" & dd)
```

The original `Order_Date` column was removed and this corrected column took its place under the same name.

**Verification:** after the fix, the `Year` column (derived from this corrected date, see below) was checked and confirmed to contain only 2023 and 2024, with no values outside that range and no null/unparseable dates.

## 3. Calculated date columns

Added directly from the corrected `Order_Date` (using `Add Column > Date`, not text extraction, so the values remain true date-typed fields rather than locale-dependent text parsing):

- `Year` — `Date.Year`
- `Month` — `Date.Month` (numeric, 1 to 12)
- `Quarter` — `Date.QuarterOfYear` (numeric, 1 to 4)
- `Month_Short` — `Date.ToText([Order_Date], "MMM")`, e.g. "Jan," "Feb"

`Month_Short` is text and sorts alphabetically by default. In the model, its **Sort by column** property was set to the numeric `Month` column so it renders in calendar order (Jan through Dec) in every visual, not alphabetical order.

`Year`, `Month`, and `Quarter` were also numeric by default, which meant Power BI defaulted their summarization to Sum. Since these are labels, not quantities, **Summarize by** was changed to **Do not summarize** for all three.

## 4. Row-level profit margin column

Added `Profit_Margin_%` = `[Profit] / [Revenue]` at the row level. This column exists for row-level inspection and spot-checking only. It is **not** used directly in any dashboard visual or headline figure, since summing or averaging a row-level percentage across many rows misrepresents the true aggregate margin (it overweights whichever category or group has the most transaction rows). All margin figures in the dashboard and README come from the DAX measure described below.

## 5. Customer identifier: evaluated, not used

`Customer_Name` alone has significant collisions (e.g. one name recurring 102 times across different cities). A composite key (`Customer_Name & City & State`) was tested to see if it could serve as a usable proxy for a unique customer. It reduced ambiguity considerably (197,000 distinct keys out of 200,000 rows) but also showed that repeat "customers" under this proxy are rare (98.5% of composite keys appear only once), meaning there isn't meaningful repeat-purchase signal in this dataset even with a cleaner key.

This column was **not added to the final model**. It wasn't feeding any chart or measure, and an unused column in the model is harder to justify than a documented limitation. The limitation itself is recorded here and in the README rather than papered over with an unused field.

## 6. Table load

Loaded to the model as `Product_Sales`.

## 7. DAX measures

```DAX
Total Revenue = SUM(Product_Sales[Revenue])

Total Profit = SUM(Product_Sales[Profit])

Profit Margin % = DIVIDE(SUM(Product_Sales[Profit]), SUM(Product_Sales[Revenue]))

Revenue PY = 
CALCULATE(
    [Total Revenue],
    Product_Sales[Year] = MAX(Product_Sales[Year]) - 1
)

Revenue YoY % = 
VAR CurrentYearRevenue = 
    CALCULATE([Total Revenue], Product_Sales[Year] = MAX(Product_Sales[Year]))
RETURN
    DIVIDE(CurrentYearRevenue - [Revenue PY], [Revenue PY])

Regional Electronics Margin = 
CALCULATE(
    [Profit Margin %],
    Product_Sales[Category] = "Electronics"
)
```

**Notes on these measures:**

- `Profit Margin %` is calculated at the aggregate level (sum over sum), not as an average of the row-level `Profit_Margin_%` column. This was verified against source: category-level margins computed directly from the raw CSV (Accessories 34.0%, Clothing & Apparel 32.5%, Home & Furniture 23.5%, Electronics 14.0%) matched the measure's output exactly.
- `Revenue YoY %` originally used `DIVIDE([Total Revenue] - [Revenue PY], [Revenue PY])` directly, without pinning the current year. This produced incorrect results whenever the Year slicer was set to "All," because `[Total Revenue]` would sum both years combined while `[Revenue PY]` stayed locked to the prior year, comparing mismatched periods. The fix was to explicitly lock the current year with a variable (`CurrentYearRevenue`), the same way `Revenue PY` already locks to the prior year, so both sides of the comparison are always single, matching periods regardless of slicer state.
- `Regional Electronics Margin` deliberately overrides the page-level Category slicer with `CALCULATE`, rather than relying on a visual-level filter. This chart is meant to always show the Electronics-specific regional breakdown regardless of what a user selects in the Category slicer elsewhere on the page, since that comparison is a fixed part of the report's core finding, not something meant to be filtered away.

## 8. Verification performed against source data

The following figures were independently recalculated from the raw CSV (outside of Power BI) to confirm the model's output:

| Metric | Verified value |
|---|---|
| Total Revenue | $142,407,744.93 |
| Total Profit | $31,548,608.13 |
| Overall margin | 22.15% |
| Revenue by category | Electronics $57M, Home & Furniture $48M, Clothing & Apparel $27M, Accessories $10M |
| Margin by category | Accessories 34.0%, Clothing & Apparel 32.5%, Home & Furniture 23.5%, Electronics 14.0% |
| Electronics margin by region | East 14.1%, Centre 14.1%, West 14.0%, South 14.0% |
| Revenue, Nov 2023 vs Nov 2024 | $13.37M vs $13.90M |
| Average non-peak month revenue | ~$4.3M (both years) |
| Margin, Oct to Dec vs rest of year | 22.15% vs 22.17% |
| Revenue YoY (2023 to 2024) | +1.27% |

All figures in the README trace back to this table.
