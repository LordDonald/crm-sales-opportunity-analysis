# Data Dictionary

## Purpose

This document summarizes the fields used in the CRM Sales Opportunity Analysis project and how they were interpreted in the final analytical model.

It is based on the source dataset definitions and the validation work completed during Excel EDA and Power BI modelling.

---

## `fact_sales_pipeline`

**Grain:** one row per sales opportunity  
**Validated row count:** 8,800

| Field | Role | Business Meaning / Use | QA Notes |
|---|---|---|---|
| `opportunity_id` | Primary key | Unique identifier for each sales opportunity | 8,800 distinct values; 0 blanks; 0 duplicates |
| `sales_agent` | Foreign key | Sales agent responsible for the opportunity | No blanks; all populated values match `dim_sales_teams` |
| `product` | Foreign key | Product associated with the opportunity | Source mismatch `GTXPro` -> `GTX Pro` corrected in Power Query |
| `account` | Foreign key | Customer account associated with the opportunity | 1,425 blanks, all in open Prospecting/Engaging opportunities; no unmatched populated values |
| `deal_stage` | Status / outcome | Current stage or final outcome of the opportunity | Values used in analysis: Prospecting, Engaging, Won, Lost |
| `engage_date` | Date | Date the opportunity reached the Engaging stage | Blank for Prospecting; populated for Engaging, Won and Lost |
| `close_date` | Date | Date the opportunity closed as Won or Lost | Populated only for closed opportunities; no close date precedes `engage_date` |
| `close_value` | Numeric | Value recorded when the opportunity closes | Positive for Won; 0 for Lost; blank for open opportunities |
| `sales_cycle_days` | Derived numeric | Days from `engage_date` to `close_date` | Calculated for closed opportunities only |

### Derived business rules

**Won Revenue**

```text
Sum of close_value where deal_stage = "Won"
```

**Closed Opportunity**

```text
deal_stage = "Won" or "Lost"
```

**Open Opportunity**

```text
deal_stage = "Prospecting" or "Engaging"
```

**Sales Cycle**

```text
close_date - engage_date
```

The dataset does not contain an opportunity-created date, so `sales_cycle_days` does **not** represent the full lead-to-close lifecycle.

---

## `dim_accounts`

**Grain:** one row per account  
**Validated row count:** 85

| Field | Role | Business Meaning / Use | QA Notes |
|---|---|---|---|
| `account` | Primary key | Unique customer/account name | 85 unique values; no blanks or duplicates |
| `sector` | Attribute | Customer industry sector | Source value `technolgy` standardized to `technology` |
| account attributes | Attributes | Additional company descriptors used during EDA and segmentation | Added to the flattened Excel `sales_analysis` table during EDA |

### Sector categories used in the final analysis

- Retail
- Technology
- Medical
- Software
- Finance
- Marketing
- Entertainment
- Telecommunications
- Services
- Employment

The sector analysis contains **7,375 assigned opportunities** rather than all 8,800 opportunities because **1,425 open opportunities have no assigned account**.

All 4,238 Won opportunities have an account, so closed revenue analysis by account and sector remains complete.

---

## `dim_products`

**Grain:** one row per product  
**Validated row count:** 7

| Field | Role | Business Meaning / Use | QA Notes |
|---|---|---|---|
| `product` | Primary key | Unique product name | 7 unique values; no blanks or duplicates |
| `series` | Attribute | Product family / product series | Used for portfolio-level slicing and analysis |
| reference sales price | Attribute | Reference price associated with the product | Used during product profiling and interpretation |

### Products

| Product | Series | Reference / Observed Value Context |
|---|---|---|
| GTK 500 | GTK | Highest-priced product; reference price approximately $26,768 |
| GTX Basic | GTX | Lower-value GTX product |
| GTX Plus Basic | GTX | Mid-low value GTX product |
| GTX Plus Pro | GTX | High-value GTX product |
| GTX Pro | GTX | High-value, high-revenue product |
| MG Advanced | MG | Higher-value MG product |
| MG Special | MG | Lowest-priced product at $55 |

The source pipeline originally contained `GTXPro` while the product table contained `GTX Pro`. The pipeline value was standardized in Power Query before modelling.

---

## `dim_sales_teams`

**Grain:** one row per sales agent  
**Validated row count:** 35

| Field | Role | Business Meaning / Use | QA Notes |
|---|---|---|---|
| `sales_agent` | Primary key | Unique sales agent | 35 unique values; no blanks or duplicates |
| `manager` | Attribute | Manager responsible for the sales agent | 6 managers in the dataset |
| `regional_office` | Attribute | Regional office assigned to the agent | 3 regions: Central, East and West |

### Organisational hierarchy

```text
Regional Office
    -> Manager
        -> Sales Agent
```

### Team structure

- Central: 11 agents
- East: 12 agents
- West: 12 agents
- Total: 35 agents

Five agents have no assigned opportunities in the fact table.

---

## `dim_date`

**Role:** shared date dimension for closing and engagement analysis.

| Field | Role | Business Meaning / Use |
|---|---|---|
| `Date` | Key | Calendar date used to relate the date dimension to the fact table |
| `Year` | Attribute | Calendar year |
| `Quarter` | Attribute | Quarter label |
| `Quarter Number` | Sort / attribute | Numeric quarter order |
| `Year Quarter` | Attribute | Combined year-quarter label |
| `Year Quarter Sort` | Sort | Chronological sort key for year-quarter |
| `Month` | Attribute | Month name |
| `Month Number` | Sort / attribute | Numeric month order |
| `Month Year` | Attribute | Combined month-year label |
| `Month Year Sort` | Sort | Chronological sort key for month-year |

### Relationships

**Active**

```text
dim_date[Date] -> fact_sales_pipeline[close_date]
```

Used for normal closed-deal and revenue reporting.

**Inactive**

```text
dim_date[Date] -> fact_sales_pipeline[engage_date]
```

Activated in engagement-specific DAX measures with `USERELATIONSHIP`.

---

## `_Measures`

The `_Measures` table contains DAX measures only.

### Pipeline measures

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

### Revenue and productivity measures

- Won Revenue
- Average Won Deal Value
- Revenue per Opportunity
- Revenue per Account
- Revenue per Agent
- Opportunities per Account
- Opportunities per Agent
- Account Count
- Sales Agents

### Sales-cycle measures

- Average Sales Cycle Days
- Median Sales Cycle Days
- Won Average Sales Cycle Days
- Won Median Sales Cycle Days
- Lost Average Sales Cycle Days
- Lost Median Sales Cycle Days

### Engagement-cohort measures

- Opportunities Reaching Engaging
- Won Opportunities by Engagement Date
- Lost Opportunities by Engagement Date
- Closed Opportunities by Engagement Date
- Won Revenue by Engagement Date
- Engagement Cohort Win Rate

### Ranking and concentration measures

- Revenue Leader
- Win-Rate Leader
- Efficiency Leader
- Deal-Value Leader
- Largest Account Revenue Share
- Top 5 Account Revenue Share
- Top 10 Account Revenue Share

---

## Important Analytical Definitions

### Closed-Deal Win Rate

The denominator includes only opportunities with final outcomes:

```text
Won Opportunities / (Won Opportunities + Lost Opportunities)
```

Open Prospecting and Engaging opportunities are excluded.

Validated overall result:

**63.2%**

---

### Open Pipeline Share

```text
Open Opportunities / Total Opportunities
```

Validated result:

**23.7%**

---

### Average Won Deal Value

Calculated across Won opportunities only.

Validated result:

**$2,361**

Lost opportunities are recorded with a close value of zero but are excluded from this average.

---

### Account and Sector Analysis

Account and sector analysis of **closed performance** is complete because no Won or Lost opportunity is missing an account.

Account and sector analysis of the **open pipeline** is incomplete because 1,425 open opportunities have no assigned account.

---

### Revenue vs Profitability

The dataset supports revenue analysis only.

It does not contain reliable:

- cost
- gross margin
- discount
- commission
- promotional cost
- cost to serve

Therefore, no revenue-based result should be described as profitability.

---

## Date Coverage

### Engagement dates

**20 October 2016 to 27 December 2017**

Populated engagement dates:

**8,300**

### Close dates

**1 March 2017 to 31 December 2017**

Populated close dates:

**6,711**

The incomplete early periods are treated as coverage limitations rather than genuine zero-performance periods.
