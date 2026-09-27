# 06 · Semi-additive measures (stock, balances)

Stock and balances must **not** be summed over time. Show the value at the last period in context.

### Closing Stock Value (latest month in context)
```dax
Closing Stock Value =
VAR _last = MAX ( fact_inventory_monthly[year_month] )
RETURN
    CALCULATE (
        SUM ( fact_inventory_monthly[closing_value] ),
        fact_inventory_monthly[year_month] = _last
    )
```

### Closing balance with a date table (LASTNONBLANK pattern)
```dax
Closing Balance =
CALCULATE (
    SUM ( fact_balance[amount] ),
    LASTNONBLANK ( dim_date[date], CALCULATE ( SUM ( fact_balance[amount] ) ) )
)
```

Used in: [fabric-lakehouse-end-to-end](https://github.com/Shashan4321/fabric-lakehouse-end-to-end) (closing stock ₹81.47 crore at the latest month).
