# Data Quality & QA

## Purpose

This document records the validation, cleaning and reconciliation steps used in the CRM Sales Opportunity Analysis project.

The goal was to confirm that the source data was structurally reliable before analysis, preserve traceability by leaving raw files unchanged, and verify that the final Power BI model reproduced the validated Excel results.

---

## Source Tables

| Source | Records |
|---|---:|
| Sales pipeline | 8,800 |
| Accounts | 85 |
| Sales teams | 35 |
| Products | 7 |
| Data dictionary | 21 |

All imported row counts matched the expected source totals.

---

## Grain and Primary-Key Validation

### Sales pipeline

`opportunity_id` was tested to confirm the grain of the fact table.

- 8,800 total records
- 8,800 distinct opportunity IDs
- 0 blank opportunity IDs
- 0 duplicate opportunity IDs

**Conclusion:** one row represents one sales opportunity.

### Dimension candidates

The proposed dimension keys were also validated.

| Dimension | Key | Distinct Records | Blank Keys | Duplicate Keys |
|---|---|---:|---:|---:|
| Sales teams | `sales_agent` | 35 | 0 | 0 |
| Products | `product` | 7 | 0 | 0 |
| Accounts | `account` | 85 | 0 | 0 |

**Conclusion:** all three tables were structurally suitable for the one side of one-to-many relationships.

---

## Referential-Integrity Testing

### Sales agents

- No blank `sales_agent` values in the pipeline
- No unmatched populated sales-agent values

### Products

A naming mismatch was identified:

- Pipeline value: `GTXPro`
- Product dimension value: `GTX Pro`
- Affected pipeline records: **1,480**

Without correction, those records would fail to match the product dimension.

### Accounts

- **1,425 blank account values**
- 0 unmatched populated account values

The blank accounts were investigated by deal stage before being classified.

| Deal Stage | Opportunities | Blank Accounts | Blank Rate |
|---|---:|---:|---:|
| Prospecting | 500 | 337 | 67.4% |
| Engaging | 1,589 | 1,088 | 68.5% |
| Won | 4,238 | 0 | 0.0% |
| Lost | 2,473 | 0 | 0.0% |

All 1,425 blank accounts occur in open stages.

**Conclusion:** these were retained as legitimate business blanks rather than deleted or imputed. Closed account-level analysis is unaffected, but the open pipeline cannot be fully attributed by account or sector.

---

## Date and Stage Validation

The relationship between `deal_stage`, `engage_date` and `close_date` was checked.

Validation results:

- Prospecting opportunities have no engagement date
- Engaging opportunities have an engagement date but no close date
- Won and Lost opportunities have both engagement and close dates
- Open opportunities do not have close dates
- Closed opportunities are not missing close dates
- No close date occurs before its engagement date

This supported two separate analytical date roles:

- `close_date` for revenue and closed-deal performance
- `engage_date` for engagement cohort analysis

---

## Close-Value Validation

`close_value` was profiled by stage.

- Prospecting and Engaging: blank
- Won: positive values
- Lost: zero
- Negative close values: none

All **4,238 Won opportunities** had positive close values.

All **2,473 Lost opportunities** had a close value of zero.

Total close value for Won opportunities reconciled to:

**$10,005,534**

This established the revenue rule used throughout the project:

> **Won Revenue = sum of `close_value` where `deal_stage = "Won"`**

Lost zeros were retained because they correctly represent unsuccessful closed deals, but they are excluded from Average Won Deal Value.

---

## Cleaning Transformations

Raw CSV files were not edited directly.

Cleaning was performed through referenced Power Query transformations.

### Product standardisation

**Issue:** `GTXPro` in the pipeline did not match `GTX Pro` in the product table.

**Transformation:** replace `GTXPro` with `GTX Pro`.

**Validation:**

- Unmatched product records before: 1,480
- Unmatched product records after: 0
- Pipeline row count after transformation: 8,800

### Sector standardisation

**Issue:** 12 account records used `technolgy` instead of `technology`.

**Transformation:** replace `technolgy` with `technology`.

**Validation:**

- Misspelled value after transformation: 0
- Account row count after transformation: 85
- Technology account count after cleaning: 12

---

## Analysis-Ready Excel Table QA

A flattened Excel analysis table, `sales_analysis`, was created with Left Outer joins.

Attributes added:

- Manager
- Regional Office
- Product Series
- Reference Sales Price
- Sector
- Account attributes

Each merge was validated separately.

Final result:

- 8,800 opportunities retained
- All populated sales-agent values matched
- All populated product values matched
- All populated account values matched
- 1,425 account-related blanks retained as expected

The flattened table was used for exploratory analysis only. The Power BI model later used a star schema.

---

## Baseline KPI Reconciliation

The following baseline figures were established in Excel and later reproduced in Power BI.

| KPI | Validated Result |
|---|---:|
| Total Opportunities | 8,800 |
| Prospecting Opportunities | 500 |
| Engaging Opportunities | 1,589 |
| Open Opportunities | 2,089 |
| Won Opportunities | 4,238 |
| Lost Opportunities | 2,473 |
| Closed Opportunities | 6,711 |
| Closed-Deal Win Rate | 63.2% |
| Closed-Deal Loss Rate | 36.8% |
| Open Pipeline Share | 23.7% |
| Won Revenue | $10,005,534 |
| Average Won Deal Value | $2,361 |

---

## Power BI QA Pages

Four dedicated QA pages were retained inside the PBIX and hidden from stakeholder navigation:

1. **QA - Regional**
2. **QA - Products**
3. **QA - Accounts**
4. **QA - Date**

These pages occupy the first four pages of the PBIX. The stakeholder-facing report opens on **Overview**.

---

## Regional QA

The regional Power BI results reconciled to the Excel EDA.

| Region | Total Opps | Won | Lost | Win Rate | Won Revenue |
|---|---:|---:|---:|---:|---:|
| Central | 3,512 | 1,629 | 975 | 62.6% | $3,346,293 |
| West | 2,997 | 1,438 | 811 | 63.9% | $3,568,647 |
| East | 2,291 | 1,171 | 687 | 63.0% | $3,090,594 |
| **Total** | **8,800** | **4,238** | **2,473** | **63.2%** | **$10,005,534** |

---

## Product QA

The final product totals reconciled to the validated pipeline.

| Product | Total Opps | Won | Win Rate | Won Revenue | Avg Won Deal |
|---|---:|---:|---:|---:|---:|
| GTK 500 | 40 | 15 | 60.0% | $400,612 | $26,707 |
| GTX Basic | 1,866 | 915 | 63.7% | $499,263 | $546 |
| GTX Plus Basic | 1,383 | 653 | 62.1% | $705,275 | $1,080 |
| GTX Plus Pro | 968 | 479 | 64.3% | $2,629,651 | $5,490 |
| GTX Pro | 1,480 | 729 | 63.6% | $3,510,578 | $4,816 |
| MG Advanced | 1,412 | 654 | 60.3% | $2,216,387 | $3,389 |
| MG Special | 1,651 | 793 | 64.8% | $43,768 | $55 |
| **Total** | **8,800** | **4,238** | **63.2%** | **$10,005,534** | **$2,361** |

---

## Account and Sector QA

The account and sector analysis contains **7,375 assigned opportunities** rather than all 8,800 opportunities.

The difference is exactly the **1,425 open opportunities with no assigned account**.

All 4,238 Won opportunities are assigned to an account, so closed revenue analysis remains complete.

Revenue concentration also reconciled to:

- Largest account: 3.4%
- Top 5 accounts: 12.1%
- Top 10 accounts: 20.7%
- Top 20 accounts: 36.0%

---

## Date QA

The model contains two date roles:

### Active relationship

`dim_date[Date]` -> `fact_sales_pipeline[close_date]`

### Inactive relationship

`dim_date[Date]` -> `fact_sales_pipeline[engage_date]`

Date QA confirmed:

- close-date analysis begins in March 2017
- engagement activity begins in October 2016
- Opportunities Reaching Engaging totals **8,300**
- engagement-date revenue measures reconcile to the validated totals when evaluated over the complete population

---

## Sales-Cycle QA

Sales cycle is calculated from:

`engage_date -> close_date`

for closed opportunities only.

Validated results:

| Outcome | Closed Opps | Avg Cycle | Median Cycle |
|---|---:|---:|---:|
| Won | 4,238 | 51.8 days | 57 days |
| Lost | 2,473 | 41.5 days | 14 days |
| **Overall** | **6,711** | **48.0 days** | **45 days** |

### Product-level aggregation check

The product-level cycle measure was also tested for correct aggregation.

Weighted by closed-opportunity count, the seven product averages return approximately **47.97 days**, which rounds to the reported overall average of **48.0 days**.

This ruled out an average-of-averages error.

---

## Small-Sample Validation: GTK 500

GTK 500 has only **25 closed opportunities**.

Its regional cycle matrix shows:

| Region | Avg Cycle Days |
|---|---:|
| Central | 14.0 |
| East | 39.0 |
| West | 56.1 |

Reconciling the regional values to the overall GTK 500 average of 53.7 days implies an approximate regional split of:

- Central: 1 closed opportunity
- East: 1
- West: 23

The Central and East cells therefore rest on single observations and should not be interpreted as stable regional process differences.

---

## Slicer and Interaction QA

Visual interactions were tested explicitly rather than relying on Power BI defaults.

### Close Period and open-pipeline visuals

Open opportunities have no `close_date`.

The Close Period slicer was therefore prevented from incorrectly filtering visuals intended to represent the full/open pipeline, including:

- Open Pipeline KPI
- Pipeline Status
- Unassigned Open Opportunities where applicable

### Engagement cohorts

The Engagement Cohorts visual uses engagement-date logic and should not be constrained by the active close-date relationship.

Specific measures activate the inactive engagement relationship through DAX.

### Sales Cycle by Outcome

The Won/Lost comparison is designed to preserve both outcomes for comparison rather than collapse under a stage selection that would remove one side of the analysis.

---

## QA Principles Applied

The project followed several QA principles throughout:

1. **Preserve the raw source**
2. **Validate grain before analysis**
3. **Test dimension keys before modelling relationships**
4. **Test referential integrity before trusting joins**
5. **Investigate blanks before classifying them as errors**
6. **Reconcile row counts after every transformation**
7. **Reconcile Power BI measures against the validated Excel EDA**
8. **Test filter context and visual interactions explicitly**
9. **Use sample size when interpreting segmented results**
10. **Separate validated facts from analytical interpretation**

---

## Final QA Status

The final Power BI report reconciles to the validated Excel analysis.

Core totals:

- **8,800 opportunities**
- **4,238 Won**
- **2,473 Lost**
- **6,711 closed**
- **63.2% closed-deal win rate**
- **$10,005,534 Won Revenue**
- **$2,361 Average Won Deal Value**
- **2,089 open opportunities**
- **1,425 unassigned open opportunities**
- **48.0-day average closed sales cycle**
- **8,300 opportunities reaching Engaging**

No unresolved reconciliation issue remained at project completion.
