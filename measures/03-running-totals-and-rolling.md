# 03 · Running totals and rolling averages

### Running total (all history up to the current date)
```dax
Revenue Running Total =
VAR _maxDate = MAX ( dim_date[date] )
RETURN
    CALCULATE ( [Total Revenue], dim_date[date] <= _maxDate, REMOVEFILTERS ( dim_date ) )
```
**Example output:** Dec-2025 → **₹3,274,543,154** (sum of 2023 + 2024 + 2025)

### Running total within the year (resets every January)
```dax
Revenue Running Total (Year) =
CALCULATE ( [Total Revenue], DATESYTD ( dim_date[date] ) )
```

### 3-month rolling average
**Purpose:** smooths the Oct-Nov festive spike when you read the trend.
```dax
Revenue 3M Rolling Avg =
VAR _months = DATESINPERIOD ( dim_date[date], MAX ( dim_date[date] ), -3, MONTH )
RETURN DIVIDE ( CALCULATE ( [Total Revenue], _months ), 3 )
```
**Example output:** Dec-2025 → **₹171,050,250** (average of Oct, Nov, Dec 2025)
