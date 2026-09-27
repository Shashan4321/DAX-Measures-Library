# 04 · Ranking, Top N, Pareto and share of parent

### Category Rank
```dax
Category Rank =
IF (
    HASONEVALUE ( dim_product[category] ),
    RANKX ( ALL ( dim_product[category] ), [Total Revenue],, DESC, DENSE )
)
```
**Example output (2025):** Electronics **1** · Home & Kitchen **2** · Sports **3** · Fashion **4** · Grocery **5**

### Dynamic Top N (with a what-if parameter)
Create the parameter table `Top N = GENERATESERIES ( 1, 10, 1 )`, add a slicer, then:
```dax
Top N Revenue =
VAR _n = SELECTEDVALUE ( 'Top N'[Top N], 5 )
RETURN IF ( [Category Rank] <= _n, [Total Revenue] )
```
**Example output:** N = 3, 2025 → shows Electronics, Home & Kitchen, Sports only

### Pareto: cumulative share
```dax
Cumulative Share % =
VAR _cur = [Total Revenue]
VAR _all = CALCULATE ( [Total Revenue], ALLSELECTED ( dim_product[category] ) )
VAR _cum =
    SUMX (
        FILTER (
            ALLSELECTED ( dim_product[category] ),
            [Total Revenue] >= _cur
        ),
        [Total Revenue]
    )
RETURN DIVIDE ( _cum, _all )
```
**Example output (2025):** Electronics **68.4%** → + Home & Kitchen **83.3%**. Two categories make up more than 80% of revenue.

### Share of parent (for a Country → State → City → Store drill-down)
```dax
Revenue Share of Parent =
VAR _parent =
    SWITCH (
        TRUE (),
        ISINSCOPE ( dim_store[store_name] ), CALCULATE ( [Total Revenue], ALLSELECTED ( dim_store[store_name] ) ),
        ISINSCOPE ( dim_store[city] ),       CALCULATE ( [Total Revenue], ALLSELECTED ( dim_store[city] ) ),
        ISINSCOPE ( dim_store[state] ),      CALCULATE ( [Total Revenue], ALLSELECTED ( dim_store[state] ) ),
        CALCULATE ( [Total Revenue], ALLSELECTED ( dim_store[country] ) )
    )
RETURN DIVIDE ( [Total Revenue], _parent )
```
**Example output (2025):** Haryana = **19.5%** of India
