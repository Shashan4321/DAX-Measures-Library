# 01 · Base measures

Every other measure builds on these. Define business rules **once** (returns excluded, distinct orders) so every report agrees.

### Total Revenue
**Purpose:** net revenue after discounts, excluding returned lines.
```dax
Total Revenue =
CALCULATE ( SUM ( fact_sales[net_revenue] ), fact_sales[is_returned] = FALSE () )
```
**Example output:** year 2025 → **₹1,584,825,489**

### Orders
**Purpose:** distinct orders with at least one kept line.
```dax
Orders =
CALCULATE ( DISTINCTCOUNT ( fact_sales[order_id] ), fact_sales[is_returned] = FALSE () )
```
**Example output:** year 2025 → **32,951**

### Avg Order Value
**Purpose:** revenue per order. `DIVIDE` returns BLANK instead of an error on zero orders.
```dax
Avg Order Value = DIVIDE ( [Total Revenue], [Orders] )
```
**Example output:** year 2025 → **₹48,096**

### Gross Margin %
```dax
Gross Margin % =
VAR _rev  = [Total Revenue]
VAR _cost = CALCULATE ( SUM ( fact_sales[cost] ), fact_sales[is_returned] = FALSE () )
RETURN DIVIDE ( _rev - _cost, _rev )
```
**Example output:** year 2025 → **30.5%**

### Discount %
**Purpose:** discount given as a share of gross (list-price) sales.
```dax
Discount % =
DIVIDE (
    SUM ( fact_sales[discount_amount] ),
    SUM ( fact_sales[discount_amount] ) + SUM ( fact_sales[net_revenue] )
)
```
**Example output:** year 2025 → **6.3%**

### Return Rate %
```dax
Return Rate % =
DIVIDE (
    CALCULATE ( COUNTROWS ( fact_sales ), fact_sales[is_returned] = TRUE () ),
    COUNTROWS ( fact_sales )
)
```
**Example output:** year 2025 → **1.99%**
