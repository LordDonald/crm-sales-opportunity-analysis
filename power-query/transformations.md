# Power Query Transformations

## Purpose

This document records the Power Query transformations used in the CRM Sales Opportunity Analysis project.

The raw CSV files were preserved unchanged. All cleaning, standardisation and analysis-ready shaping was performed downstream so that the source remained traceable and the transformation process could be reproduced.

> This file documents the transformation logic and validation results. It is not intended to pretend that reconstructed example M code is the exact source from the final workbook or PBIX.

---

# 1. Source Files

The project began with five CSV files:

- `sales_pipeline.csv`
- `accounts.csv`
- `sales_teams.csv`
- `products.csv`
- `data_dictionary.csv`

Initial row counts:

| Source | Records |
|---|---:|
| Sales pipeline | 8,800 |
| Accounts | 85 |
| Sales teams | 35 |
| Products | 7 |
| Data dictionary | 21 |

All imported row counts matched the source totals.

---

# 2. Raw-Data Preservation

The source CSV files were intentionally left unchanged.

The working principle was:

> **Raw data stays raw; corrections happen in the transformation layer.**

This was important for:

- traceability
- QA
- reproducibility
- comparing original and cleaned values
- preventing undocumented manual edits

---

# 3. Sales Pipeline Cleaning

## Product Name Standardisation

### Issue

The sales pipeline contained:

```text
GTXPro
```

while the product reference table contained:

```text
GTX Pro
```

Affected pipeline rows:

**1,480**

Without standardisation, those opportunities would not match the product dimension.

### Transformation

The pipeline product value was standardised from:

```text
GTXPro
```

to:

```text
GTX Pro
```

### Validation

| Check | Before | After |
|---|---:|---:|
| Pipeline rows | 8,800 | 8,800 |
| Unmatched populated products | 1,480 | 0 |

No records were added or removed.

---

# 4. Account Cleaning

## Sector Name Standardisation

### Issue

The accounts source contained:

```text
technolgy
```

instead of:

```text
technology
```

Affected account rows:

**12**

### Transformation

The sector value was standardised to:

```text
technology
```

### Validation

| Check | Result |
|---|---:|
| Account rows retained | 85 |
| Remaining `technolgy` values | 0 |
| Technology accounts after cleaning | 12 |

---

# 5. Missing Account Values

The pipeline contains **1,425 blank account values**.

Before deciding whether to fill, delete or retain them, the blanks were profiled by deal stage.

| Deal Stage | Total Opps | Blank Accounts | Blank Rate |
|---|---:|---:|---:|
| Prospecting | 500 | 337 | 67.4% |
| Engaging | 1,589 | 1,088 | 68.5% |
| Won | 4,238 | 0 | 0.0% |
| Lost | 2,473 | 0 | 0.0% |

### Decision

The blanks were **retained**.

They occur only in open stages and therefore behave like a legitimate operational/account-assignment condition rather than corrupted historical data.

No placeholder account such as `Unknown` was inserted.

### Analytical consequence

Closed account and sector analysis remains complete because no Won or Lost opportunity is missing an account.

Open-pipeline account and sector analysis is incomplete for those 1,425 opportunities.

---

# 6. Data-Type and Structural Checks

The imported queries were reviewed for appropriate data types.

Key logical types included:

- identifiers / descriptive fields -> text
- `engage_date` -> date
- `close_date` -> date
- `close_value` -> numeric
- price fields -> numeric

The transformation process also preserved the validated grain:

```text
1 row = 1 sales opportunity
```

with:

- 8,800 total opportunity rows
- 8,800 distinct `opportunity_id` values
- 0 duplicate opportunity IDs

---

# 7. Excel EDA Analysis Table

For exploratory analysis in Excel, an analysis-ready query named:

```text
sales_analysis
```

was created.

The pipeline remained the controlling table.

**Join type used:** Left Outer

This ensured all 8,800 sales opportunities were retained even when a related descriptive value was blank.

---

## Sales Team Merge

Join key:

```text
sales_agent
```

Attributes added:

- `manager`
- `regional_office`

Validation:

- all populated sales-agent values matched
- no rows were lost
- final row count remained 8,800

---

## Product Merge

Join key:

```text
product
```

Attributes added:

- `series`
- reference sales price

The merge was performed after correcting `GTXPro` to `GTX Pro`.

Validation:

- all populated products matched
- no rows were lost
- final row count remained 8,800

---

## Account Merge

Join key:

```text
account
```

Attributes added:

- `sector`
- account/company attributes

Validation:

- all populated account values matched
- 1,425 account-related blanks remained by design
- no rows were lost
- final row count remained 8,800

---

# 8. Sales-Cycle Derivation

Sales-cycle duration was defined as:

```text
close_date - engage_date
```

for closed opportunities.

This produced `sales_cycle_days`.

### Important limitation

The dataset does not contain an opportunity-created date.

Therefore:

```text
sales_cycle_days
```

measures:

```text
Engaging -> Close
```

not the full:

```text
Opportunity Creation -> Close
```

The resulting field is valid for Won/Lost sales-cycle analysis but should not be described as total lead-to-close duration.

---

# 9. Date Logic

Two dates are analytically important:

- `close_date`
- `engage_date`

Rather than flattening both into one time concept, they were kept separate.

In Power BI:

- `close_date` became the active relationship to `dim_date`
- `engage_date` became an inactive relationship used through DAX when engagement-cohort analysis was required

This prevented the transformation layer from forcing two different business events into one date definition.

---

# 10. Power BI Model Shaping

The final Power BI model did not use the flattened Excel `sales_analysis` table as the semantic model.

Instead, the transformed sources were shaped into:

- `fact_sales_pipeline`
- `dim_accounts`
- `dim_products`
- `dim_sales_teams`
- `dim_date`

plus a dedicated:

- `_Measures`

table.

This preserved a clear star schema.

---

# 11. Fact Table

## `fact_sales_pipeline`

Grain:

```text
1 row per opportunity
```

Important fields retained:

- `opportunity_id`
- `sales_agent`
- `product`
- `account`
- `deal_stage`
- `engage_date`
- `close_date`
- `close_value`
- `sales_cycle_days`

Validated row count:

**8,800**

---

# 12. Dimension Tables

## `dim_accounts`

Validated rows:

**85**

Key:

```text
account
```

Used for:

- account analysis
- sector analysis
- customer concentration

---

## `dim_products`

Validated rows:

**7**

Key:

```text
product
```

Used for:

- product analysis
- product-series analysis
- price/value context

---

## `dim_sales_teams`

Validated rows:

**35**

Key:

```text
sales_agent
```

Used for the hierarchy:

```text
Regional Office
    -> Manager
        -> Sales Agent
```

---

# 13. Transformation Validation Principles

Every transformation was followed by reconciliation.

The checks included:

1. row counts before and after transformations
2. distinct key counts
3. duplicate-key checks
4. blank-key checks
5. unmatched foreign-key checks
6. stage/date consistency
7. total-revenue reconciliation
8. final Power BI totals against Excel EDA

Key final reconciliation values:

| Metric | Result |
|---|---:|
| Total Opportunities | 8,800 |
| Won Opportunities | 4,238 |
| Lost Opportunities | 2,473 |
| Closed Opportunities | 6,711 |
| Open Opportunities | 2,089 |
| Won Revenue | $10,005,534 |
| Closed-Deal Win Rate | 63.2% |
| Average Won Deal Value | $2,361 |

---

# 14. Transformation Log Summary

| Area | Source Issue / Need | Transformation | Validation |
|---|---|---|---|
| Product | `GTXPro` did not match `GTX Pro` | Standardised product name | 1,480 unmatched -> 0 |
| Accounts | `technolgy` misspelling | Standardised to `technology` | 85 rows retained |
| Missing accounts | 1,425 blanks | Retained, not imputed | All blanks confined to open pipeline |
| EDA enrichment | Descriptive fields spread across tables | Left Outer merges into `sales_analysis` | 8,800 rows retained |
| Sales cycle | No direct duration field for analysis | Derived `close_date - engage_date` | 6,711 closed opportunities populated |
| Power BI model | Flat EDA table not ideal for reporting | Shaped into star schema | Final model reconciled to Excel EDA |

---

# 15. Why the Transformations Were Kept Minimal

The project intentionally avoided unnecessary transformations.

For example:

- blank open-stage accounts were not replaced with fabricated values
- raw Lost close values of zero were retained
- source files were not manually edited
- incomplete date periods were not filled with synthetic activity
- no causal variables were manufactured from unavailable data

The aim was to improve consistency and model usability without changing the underlying business meaning of the source.

---

# 16. Reproducibility Note

The public repository includes:

- original source CSV files in `data/`
- the Excel EDA workbook in `excel/`
- the final Power BI file in `powerbi/`
- this transformation documentation in `power-query/`
- QA and model documentation in `docs/`

Together, these files provide a traceable path from the original data to the final dashboard.
