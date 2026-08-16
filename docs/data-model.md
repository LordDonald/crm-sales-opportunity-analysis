# Power BI Data Model

## Purpose

This document explains the semantic model used in the CRM Sales Opportunity Analysis project and the modelling decisions that support the final dashboard.

The final Power BI solution uses a **star schema** rather than the flattened Excel analysis table used during exploratory analysis.

---

## Model Overview

The model contains one central fact table, four supporting dimensions and a dedicated measures table.

```text
                   dim_date
                      |
                      | active: close_date
                      | inactive: engage_date
                      |
dim_accounts ---- fact_sales_pipeline ---- dim_products
                      |
                      |
                dim_sales_teams

                   _Measures
```

Main tables:

- `fact_sales_pipeline`
- `dim_accounts`
- `dim_products`
- `dim_sales_teams`
- `dim_date`
- `_Measures`

---

## Fact Table

### `fact_sales_pipeline`

**Grain:** one row per sales opportunity.

Validated row count:

**8,800 opportunities**

Primary identifier:

`opportunity_id`

Important fields include:

- `opportunity_id`
- `sales_agent`
- `product`
- `account`
- `deal_stage`
- `engage_date`
- `close_date`
- `close_value`
- `sales_cycle_days`

The fact table stores the transactional opportunity-level data used by all core measures.

---

## Dimension Tables

### `dim_accounts`

**Grain:** one row per account.

Validated account count:

**85 accounts**

Key:

`account`

The table contains account-level descriptive attributes used for customer and sector analysis.

Important attributes include:

- `account`
- sector
- company characteristics
- account descriptors used for segmentation

The pipeline contains **1,425 blank account values**, all within open Prospecting or Engaging opportunities.

These blanks were retained rather than imputed because they represent unassigned open opportunities rather than unmatched closed accounts.

---

### `dim_products`

**Grain:** one row per product.

Validated product count:

**7 products**

Key:

`product`

Important attributes include:

- `product`
- `series`
- reference sales price

A source mismatch between `GTXPro` and `GTX Pro` affected 1,480 pipeline records and was corrected in Power Query before the final model was built.

---

### `dim_sales_teams`

**Grain:** one row per sales agent.

Validated agent count:

**35 sales agents**

Key:

`sales_agent`

Important attributes include:

- `sales_agent`
- `manager`
- `regional_office`

The hierarchy used throughout the Sales Team page is:

```text
Regional Office
    -> Manager
        -> Sales Agent
```

Five of the 35 agents have no assigned opportunities in the fact table.

---

### `dim_date`

A dedicated date dimension was created because the sales pipeline contains two analytically important date fields:

- `close_date`
- `engage_date`

Important attributes include:

- Date
- Year
- Quarter
- Quarter Number
- Year Quarter
- Year Quarter Sort
- Month
- Month Number
- Month Year
- Month Year Sort

The sort fields ensure chronological display rather than alphabetical sorting.

---

## Relationships

### Sales Team Relationship

```text
dim_sales_teams[sales_agent]
    1
    |
    *
fact_sales_pipeline[sales_agent]
```

Relationship type:

**One-to-many**

Filter direction:

**Dimension to fact**

---

### Product Relationship

```text
dim_products[product]
    1
    |
    *
fact_sales_pipeline[product]
```

Relationship type:

**One-to-many**

Filter direction:

**Dimension to fact**

---

### Account Relationship

```text
dim_accounts[account]
    1
    |
    *
fact_sales_pipeline[account]
```

Relationship type:

**One-to-many**

Filter direction:

**Dimension to fact**

Populated account values reconcile fully after cleaning.

Blank fact-side accounts remain unmatched by design because they represent open opportunities with no assigned account.

---

## Dual Date Relationships

The date dimension supports two different business timelines.

### Active Relationship: Close Date

```text
dim_date[Date]
    1
    |
    *
fact_sales_pipeline[close_date]
```

This is the active relationship.

It drives normal date filtering for:

- Won Revenue
- Won Opportunities
- Lost Opportunities
- Closed Opportunities
- Closed-Deal Win Rate
- monthly closing performance
- closed-deal sales-cycle analysis

The closing data covers:

**1 March 2017 to 31 December 2017**

---

### Inactive Relationship: Engagement Date

```text
dim_date[Date]
    1
    |
    *
fact_sales_pipeline[engage_date]
```

This relationship is inactive.

It is activated only inside engagement-specific DAX measures using `USERELATIONSHIP`.

It supports:

- Opportunities Reaching Engaging
- Won Opportunities by Engagement Date
- Lost Opportunities by Engagement Date
- Closed Opportunities by Engagement Date
- Won Revenue by Engagement Date
- Engagement Cohort Win Rate

The engagement data covers:

**20 October 2016 to 27 December 2017**

---

## Why the Close-Date Relationship Is Active

Most headline report metrics are based on completed commercial outcomes.

Examples include:

- Won Revenue
- Won Opportunities
- Lost Opportunities
- Closed-Deal Win Rate
- monthly closing trends

For those metrics, `close_date` is the natural reporting date.

Making it active therefore keeps normal date filtering intuitive across the main dashboard.

---

## Why the Engagement-Date Relationship Is Inactive

An opportunity can reach Engaging on one date and close much later.

Using the active close-date relationship for engagement cohorts would answer the wrong question.

The inactive relationship allows the model to switch date context only when the measure explicitly requires it.

This avoids duplicating the date dimension while preserving both business timelines.

---

## Measures Table

The `_Measures` table contains analytical measures only and is not used as a business dimension.

This keeps DAX definitions centrally organised.

Major measure groups include:

### Pipeline

- Total Opportunities
- Prospecting Opportunities
- Engaging Opportunities
- Open Opportunities
- Open Pipeline Share
- Won Opportunities
- Lost Opportunities
- Closed Opportunities
- Closed-Deal Win Rate
- Closed-Deal Loss Rate

### Revenue and Productivity

- Won Revenue
- Average Won Deal Value
- Revenue per Opportunity
- Revenue per Account
- Revenue per Agent
- Opportunities per Account
- Opportunities per Agent
- Account Count
- Sales Agents

### Sales Cycle

- Average Sales Cycle Days
- Median Sales Cycle Days
- Won Average Sales Cycle Days
- Won Median Sales Cycle Days
- Lost Average Sales Cycle Days
- Lost Median Sales Cycle Days

### Engagement Cohorts

- Opportunities Reaching Engaging
- Won Opportunities by Engagement Date
- Lost Opportunities by Engagement Date
- Closed Opportunities by Engagement Date
- Won Revenue by Engagement Date
- Engagement Cohort Win Rate

### Ranking and Concentration

- Revenue Leader
- Win-Rate Leader
- Efficiency Leader
- Deal-Value Leader
- Largest Account Revenue Share
- Top 5 Account Revenue Share
- Top 10 Account Revenue Share

---

## Why a Star Schema Was Used

During Excel EDA, a flattened `sales_analysis` table was useful because it made exploratory comparisons easy.

The final Power BI model intentionally moved away from that structure.

The star schema was preferred because it:

- preserves a clear fact-table grain
- reduces repeated descriptive attributes
- supports reusable dimensions
- makes filter propagation easier to reason about
- simplifies DAX
- improves maintainability
- makes QA easier
- better reflects production BI modelling practice

---

## Model QA

The final semantic model reconciles to the validated Excel analysis.

Core model totals:

| Metric | Result |
|---|---:|
| Total Opportunities | 8,800 |
| Won Opportunities | 4,238 |
| Lost Opportunities | 2,473 |
| Closed Opportunities | 6,711 |
| Closed-Deal Win Rate | 63.2% |
| Won Revenue | $10,005,534 |
| Average Won Deal Value | $2,361 |
| Open Opportunities | 2,089 |
| Opportunities Reaching Engaging | 8,300 |

Product, region, account and date-level QA were also performed in dedicated hidden Power BI pages.

---

## Interaction Implication of the Model

The active date relationship means a normal Close Period slicer naturally filters records by `close_date`.

That behaviour is correct for closed-deal visuals but not for every metric.

Open opportunities have no `close_date`.

As a result, Close Period interaction was deliberately disabled for visuals intended to represent the full open pipeline.

Likewise, engagement-cohort measures explicitly activate the inactive engagement relationship rather than inheriting the active close-date context.

This was a key modelling and report-design principle:

> Filter context should follow the business meaning of the metric, not simply the default relationship behaviour.

---

## Final Model Design Principle

The final model separates:

- transactions from descriptive attributes
- closing-date analysis from engagement-date analysis
- measures from dimensions
- stakeholder reporting from QA logic

The result is a compact semantic model that supports the six stakeholder-facing report pages while remaining traceable to the validated Excel analysis.
