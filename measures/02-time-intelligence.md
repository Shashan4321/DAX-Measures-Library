# 02 · Time intelligence: YoY, MoM, YTD, QTD, MTD, Indian FY

**Requirements:** `dim_date` marked as a date table, with a continuous `date` column and a relationship to the fact.

### Revenue PY
```dax
Revenue PY = CALCULATE ( [Total Revenue], SAMEPERIODLASTYEAR ( dim_date[date] ) )
```
**Example output:** year 2025 → **₹1,199,656,427** (the 2024 value)

### Revenue YoY %
**Purpose:** growth vs last year; BLANK (not -100% or +∞) when there is no prior year.
```dax
Revenue YoY % =
VAR _cur = [Total Revenue]
VAR _py  = [Revenue PY]
RETURN IF ( NOT ISBLANK ( _py ), DIVIDE ( _cur - _py, _py ) )
```
**Example output:** 2025 → **+32.1%** · Oct-2025 → **+22.3%** · 2023 → *blank*

### Revenue PM and MoM %
```dax
Revenue PM = CALCULATE ( [Total Revenue], DATEADD ( dim_date[date], -1, MONTH ) )

Revenue MoM % = DIVIDE ( [Total Revenue] - [Revenue PM], [Revenue PM] )
```
**Example output:** Oct-2025 → **+57.4%** (festive season) · Nov-2025 → **-2.1%**

### YTD / QTD / MTD
```dax
Revenue YTD = TOTALYTD ( [Total Revenue], dim_date[date] )
Revenue QTD = TOTALQTD ( [Total Revenue], dim_date[date] )
Revenue MTD = TOTALMTD ( [Total Revenue], dim_date[date] )
```
**Example output:** Revenue YTD at 30-Jun-2025 → **₹694,674,148**

### YTD vs same period last year
```dax
Revenue YTD PY = CALCULATE ( [Revenue YTD], SAMEPERIODLASTYEAR ( dim_date[date] ) )

Revenue YTD YoY % = DIVIDE ( [Revenue YTD] - [Revenue YTD PY], [Revenue YTD PY] )
```
**Example output:** at 30-Jun-2025 → **+43.7%** (₹694.7M vs ₹483.4M)

### Indian financial year to date (April-March)
**Purpose:** the third argument sets the year-end date.
```dax
Revenue FYTD = TOTALYTD ( [Total Revenue], dim_date[date], "31/3" )
```
**Example output:** FY2025 (Apr-24..Mar-25) → **₹1,306,245,651** · FY2026 to 31-Dec-2025 → **₹1,252,326,398**

> Tip: `"31/3"` is parsed with the model's culture. In a model with `en-US` culture, use `"3/31"`.
