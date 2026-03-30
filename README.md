# Canada Sales Dashboard — Power BI

> **Work in progress** — core data model and DAX layer are complete; report visuals and final UX polish are actively being developed.

A end-to-end Power BI project built to demonstrate proficiency in data modelling, DAX, and business intelligence storytelling. The dashboard surfaces sales performance across Canadian regions, salesperson territories, and product categories — all denominated in CAD.

---

## Overview

| Area | Detail |
|---|---|
| **Tool** | Power BI Desktop (.pbix) |
| **Currency** | Canadian Dollar (CAD) |
| **Geography** | 10 cities across 5 provinces + Atlantic zone |
| **Date Range** | 2023 – 2024 |
| **Sales Team** | 10 reps across 4 regional teams |

---

## Skills Demonstrated

### Data Modelling
- Star schema design with a central `fact_sales` table and five dimension tables (`dim_customer`, `dim_date`, `dim_product`, `dim_region`, `dim_salesperson`)
- Proper one-to-many relationships and cardinality management
- Clean separation of facts and attributes

### Power Query (M)
- Data ingestion and transformation from CSV sources
- Column typing, renaming, and normalisation
- Reusable query structure for maintainability

### DAX
- **Time intelligence:** YTD, MTD, QTD, and same-period-last-year comparisons using `TOTALYTD`, `TOTALMTD`, `TOTALQTD`, and `SAMEPERIODLASTYEAR`
- **Profitability:** Gross Profit, Gross Margin %, and YoY growth
- **Volume:** Order count, units sold, average order value, average discount
- **Customer analytics:** Active customers, revenue per customer, new customer identification using `FILTER` + `CALCULATE`
- **Sales performance:** Quota attainment %, revenue vs. target, dense rank by revenue using `RANKX` + `ALL`
- **Running totals:** Cumulative revenue via `FILTER` on `ALL(dim_date)`
- **Dynamic titles:** Measure-driven report headings that update with slicer context

### Report Design (In Progress)
- KPI cards with conditional formatting
- Time-series trend lines
- Provincial heat map / filled map visual
- Salesperson leaderboard with quota attainment bars
- Product category drill-through

---

## Data Model

```
dim_date ──────┐
dim_customer ──┤
dim_product ───┼──► fact_sales
dim_region ────┤
dim_salesperson┘
```

**fact_sales** — order-level grain: `order_id`, `order_date`, `customer_id`, `product_id`, `salesperson_id`, `region_id`, `quantity`, `unit_price`, `discount_pct`, `revenue`, `cost`, `gross_profit`, `order_status`

---

## Key Measures (excerpt)

```dax
-- Year-over-year revenue growth
Revenue YoY % =
DIVIDE([Total Revenue] - [Revenue LY], [Revenue LY], 0)

-- Quota attainment per salesperson
Quota Attainment % =
DIVIDE([Total Revenue], SUM(dim_salesperson[annual_quota]), 0)

-- Dense rank across all reps, ignoring filter context
Rank by Revenue =
RANKX(ALL(dim_salesperson[full_name]), [Total Revenue], , DESC, DENSE)
```

Full measure library: [`Data/DAX_Measures.txt`](Data/DAX_Measures.txt)

---

## Repository Structure

```
├── Sales.pbix               # Power BI report file
└── Data/
    ├── fact_sales.csv
    ├── dim_customer.csv
    ├── dim_date.csv
    ├── dim_product.csv
    ├── dim_region.csv
    ├── dim_salesperson.csv
    └── DAX_Measures.txt     # All DAX measures, annotated
```

---

## Roadmap

- [ ] Finalize report page layout and visual polish
- [ ] Add executive summary / overview page
- [ ] Implement drill-through to product and customer detail pages
- [ ] Publish to Power BI Service and embed live link
- [ ] Add row-level security (RLS) by territory

---

## How to Open

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free)
2. Clone or download this repository
3. Open `Sales.pbix`
4. If prompted, update the data source path to the local `Data/` folder
