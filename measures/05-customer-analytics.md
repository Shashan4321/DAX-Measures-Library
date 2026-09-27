# 05 · Customer analytics: new, returning, active

### Active Customers
```dax
Active Customers =
CALCULATE ( DISTINCTCOUNT ( fact_sales[customer_key] ), fact_sales[is_returned] = FALSE () )
```
**Example output:** 2025 → **3,193**

### New Customers (first-ever purchase in the selected period)
```dax
New Customers =
VAR _start = MIN ( dim_date[date] )
VAR _end   = MAX ( dim_date[date] )
RETURN
    COUNTROWS (
        FILTER (
            VALUES ( fact_sales[customer_key] ),
            VAR _first = CALCULATE ( MIN ( dim_date[date] ), REMOVEFILTERS ( dim_date ) )
            RETURN _first >= _start && _first <= _end
        )
    )
```
**Example output:** 2025 → **1,614**

### Returning Customers
```dax
Returning Customers = [Active Customers] - [New Customers]
```
**Example output:** 2025 → **1,579** (1,614 + 1,579 = 3,193 active ✔)
