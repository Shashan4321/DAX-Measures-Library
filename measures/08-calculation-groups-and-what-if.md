# 08 · Calculation groups, what-if and dynamic formatting

### Calculation group: Time Intelligence
Write each time calculation once and apply it to **any** measure (revenue, orders, margin...).

| Item | Expression |
|---|---|
| Current | `SELECTEDMEASURE ()` |
| PY | `CALCULATE ( SELECTEDMEASURE (), SAMEPERIODLASTYEAR ( dim_date[date] ) )` |
| YoY | `SELECTEDMEASURE () - CALCULATE ( SELECTEDMEASURE (), SAMEPERIODLASTYEAR ( dim_date[date] ) )` |
| YoY % | `VAR _py = CALCULATE ( SELECTEDMEASURE (), SAMEPERIODLASTYEAR ( dim_date[date] ) ) RETURN DIVIDE ( SELECTEDMEASURE () - _py, _py )` |
| YTD | `CALCULATE ( SELECTEDMEASURE (), DATESYTD ( dim_date[date] ) )` |
| FYTD | `CALCULATE ( SELECTEDMEASURE (), DATESYTD ( dim_date[date], "31/3" ) )` |

Format string expression for `YoY %`: `"+0.0%;-0.0%;0.0%"`. Full TMDL: [sales-intelligence-powerbi](https://github.com/Shashan4321/sales-intelligence-powerbi/blob/main/powerbi/SalesIntelligence.SemanticModel/definition/tables/Time%20Intelligence.tmdl).

### What-if: price increase scenario
Parameter `Price Change % = GENERATESERIES ( -0.10, 0.20, 0.01 )`:
```dax
Revenue Scenario =
[Total Revenue] * ( 1 + SELECTEDVALUE ( 'Price Change %'[Price Change %], 0 ) )
```
**Example output:** +5% on 2025 → **₹1,664,066,763**

### Dynamic measure selector (field parameter alternative)
```dax
Selected KPI =
SWITCH (
    SELECTEDVALUE ( 'KPI Selector'[KPI] ),
    "Revenue", [Total Revenue],
    "Orders", [Orders],
    "AOV", [Avg Order Value],
    "Margin %", [Gross Margin %],
    [Total Revenue]
)
```

### Dynamic format: crore / lakh
```dax
Revenue (Indian format) =
VAR _v = [Total Revenue]
RETURN
    SWITCH (
        TRUE (),
        _v >= 1e7, FORMAT ( _v / 1e7, "₹#,0.00" ) & " Cr",
        _v >= 1e5, FORMAT ( _v / 1e5, "₹#,0.00" ) & " L",
        FORMAT ( _v, "₹#,0" )
    )
```
**Example output:** 2025 → **₹158.48 Cr**
