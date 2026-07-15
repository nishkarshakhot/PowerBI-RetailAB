# DAX Measures Reference — PowerBI-RetailAB

Complete list of DAX measures used across **RetailAB Report 1** and **Report 2**, parked in a dedicated `ALL MEASURES` table for easy discovery.

## Core Measures

```dax
Total Sales = SUM(SalesData[Sales])
```
Sums all sales transaction values.

```dax
Total Profits = SUM(SalesData[Profit])
```
Sums all profit values across orders.

```dax
Total Quantity Sold = SUM(SalesData[Quantity])
```
Total units sold across all orders.

```dax
Total Orders = DISTINCTCOUNT(SalesData[OrderId])
```
Unique order count (avoids double-counting multi-line orders).

## Ratio & Percentage Measures

```dax
Profit Margin % = DIVIDE([Total Profits], [Total Sales], 0) * 100
```
Profit as a percentage of sales, with safe-divide to avoid divide-by-zero errors.

```dax
Region % Share =
DIVIDE(
    [Total Sales],
    CALCULATE([Total Sales], ALLEXCEPT(Regions, Regions[Region])),
    0
)
```
Each region's share of total sales, using `ALLEXCEPT` to preserve region context while removing all other filters.

## Time Intelligence

```dax
Same Period Last Year =
CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DateTable[Date]))
```
Prior-year sales for the same calendar period — the base for YoY comparisons.

```dax
YoY Growth % = DIVIDE([CY Sales] - [PY Sales], [PY Sales], 0) * 100
```
Year-over-year growth rate, comparing current-year to prior-year sales.

## Running Totals

```dax
Running Total (Sub-Category) =
CALCULATE(
    [Total Sales],
    FILTER(
        ALL(Products[Sub-Category]),
        Products[Sub-Category] <= MAX(Products[Sub-Category])
    )
)
```
Cumulative sales as Sub-Category is traversed alphabetically/numerically — powers the color-banded running-total visuals on the Cumulative Analysis page.

---

### Techniques demonstrated
- Safe division with `DIVIDE()` to prevent errors on zero denominators
- `CALCULATE` + `ALLEXCEPT` for percentage-of-total patterns
- `SAMEPERIODLASTYEAR` for clean time-intelligence comparisons
- `FILTER` + `ALL` for running-total / cumulative patterns over a text column
