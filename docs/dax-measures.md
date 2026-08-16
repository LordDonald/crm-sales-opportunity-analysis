# DAX Measures

## Purpose

This document describes the DAX measure layer used in the CRM Sales Opportunity Analysis project.

The Power BI model uses a dedicated `_Measures` table so that analytical logic is kept separate from the fact and dimension tables.

> **Important:** The formulas below document the business logic used in the final report. When the repository is completed, the `dax/measures.dax` file should contain the exact measure definitions exported or copied from the final PBIX so that the public source matches the production model exactly.

---

# Measure Groups

The measure layer is organized into five main areas:

1. Pipeline and opportunity counts
2. Revenue and productivity
3. Sales-cycle analysis
4. Engagement cohorts
5. Performance leaders and revenue concentration

---

# 1. Pipeline and Opportunity Measures

## Total Opportunities

**Business definition:** count of all sales opportunities.

Validated result:

**8,800**

Typical DAX pattern:

```DAX
Total Opportunities =
COUNTROWS ( fact_sales_pipeline )
```

---

## Won Opportunities

**Business definition:** opportunities whose `deal_stage` is Won.

Validated result:

**4,238**

Typical DAX pattern:

```DAX
Won Opportunities =
CALCULATE (
    [Total Opportunities],
    fact_sales_pipeline[deal_stage] = "Won"
)
```

---

## Lost Opportunities

**Business definition:** opportunities whose `deal_stage` is Lost.

Validated result:

**2,473**

Typical DAX pattern:

```DAX
Lost Opportunities =
CALCULATE (
    [Total Opportunities],
    fact_sales_pipeline[deal_stage] = "Lost"
)
```

---

## Closed Opportunities

**Business definition:** all opportunities with a final Won or Lost outcome.

Validated result:

**6,711**

Typical DAX pattern:

```DAX
Closed Opportunities =
[Won Opportunities] + [Lost Opportunities]
```

---

## Prospecting Opportunities

**Business definition:** opportunities currently in Prospecting.

Validated result:

**500**

Typical DAX pattern:

```DAX
Prospecting Opportunities =
CALCULATE (
    [Total Opportunities],
    fact_sales_pipeline[deal_stage] = "Prospecting"
)
```

---

## Engaging Opportunities

**Business definition:** opportunities currently in Engaging.

Validated result:

**1,589**

Typical DAX pattern:

```DAX
Engaging Opportunities =
CALCULATE (
    [Total Opportunities],
    fact_sales_pipeline[deal_stage] = "Engaging"
)
```

---

## Open Opportunities

**Business definition:** all opportunities that have not yet reached a final outcome.

Validated result:

**2,089**

Typical DAX pattern:

```DAX
Open Opportunities =
[Prospecting Opportunities] + [Engaging Opportunities]
```

---

## Closed-Deal Win Rate

**Business definition:** Won opportunities divided by all closed opportunities.

Open Prospecting and Engaging opportunities are intentionally excluded from the denominator.

Validated result:

**63.2%**

Typical DAX pattern:

```DAX
Closed-Deal Win Rate =
DIVIDE (
    [Won Opportunities],
    [Closed Opportunities]
)
```

---

## Closed-Deal Loss Rate

**Business definition:** Lost opportunities divided by all closed opportunities.

Validated result:

**36.8%**

Typical DAX pattern:

```DAX
Closed-Deal Loss Rate =
DIVIDE (
    [Lost Opportunities],
    [Closed Opportunities]
)
```

---

## Open Pipeline Share

**Business definition:** open opportunities divided by the complete opportunity population.

Validated result:

**23.7%**

Typical DAX pattern:

```DAX
Open Pipeline Share =
DIVIDE (
    [Open Opportunities],
    [Total Opportunities]
)
```

---

# 2. Revenue and Productivity Measures

## Won Revenue

**Business definition:** sum of `close_value` for Won opportunities only.

Validated result:

**$10,005,534**

Typical DAX pattern:

```DAX
Won Revenue =
CALCULATE (
    SUM ( fact_sales_pipeline[close_value] ),
    fact_sales_pipeline[deal_stage] = "Won"
)
```

Lost opportunities are recorded with `close_value = 0`, but the measure still explicitly filters to Won so that the business definition remains clear.

---

## Average Won Deal Value

**Business definition:** average value of successful Won opportunities.

Validated result:

**$2,361**

A common measure pattern is:

```DAX
Average Won Deal Value =
DIVIDE (
    [Won Revenue],
    [Won Opportunities]
)
```

This excludes Lost zero values from the business KPI.

---

## Revenue per Opportunity

**Business definition:** Won Revenue divided by total opportunities in the current filter context.

Typical DAX pattern:

```DAX
Revenue per Opportunity =
DIVIDE (
    [Won Revenue],
    [Total Opportunities]
)
```

This measure is used to compare revenue efficiency across agents, products and other segments.

---

## Account Count

**Business definition:** number of distinct accounts visible in the current filter context.

Typical DAX pattern:

```DAX
Account Count =
DISTINCTCOUNT ( dim_accounts[account] )
```

Validated overall dimension count:

**85**

---

## Sales Agents

**Business definition:** number of distinct agents in the sales-team dimension.

Typical DAX pattern:

```DAX
Sales Agents =
DISTINCTCOUNT ( dim_sales_teams[sales_agent] )
```

Validated dimension count:

**35**

---

## Revenue per Account

**Business definition:** Won Revenue divided by the number of accounts in the current context.

Typical DAX pattern:

```DAX
Revenue per Account =
DIVIDE (
    [Won Revenue],
    [Account Count]
)
```

This was particularly important in sector analysis because sectors contain different numbers of accounts.

---

## Revenue per Agent

**Business definition:** Won Revenue divided by the number of sales agents in the current context.

Validated overall result:

**$285,872**

Typical DAX pattern:

```DAX
Revenue per Agent =
DIVIDE (
    [Won Revenue],
    [Sales Agents]
)
```

---

## Opportunities per Account

**Business definition:** assigned opportunities divided by account count.

The sector analysis contains 7,375 assigned opportunities because 1,425 open opportunities have no account.

Typical business logic:

```DAX
Opportunities per Account =
DIVIDE (
    [Assigned Opportunities],
    [Account Count]
)
```

---

## Opportunities per Agent

**Business definition:** total opportunities divided by agent count.

Validated overall result:

**251.4**

Typical DAX pattern:

```DAX
Opportunities per Agent =
DIVIDE (
    [Total Opportunities],
    [Sales Agents]
)
```

---

## Assigned Opportunities

**Business definition:** opportunities with a populated account value.

Validated result:

**7,375**

Typical DAX pattern:

```DAX
Assigned Opportunities =
CALCULATE (
    [Total Opportunities],
    NOT ISBLANK ( fact_sales_pipeline[account] )
)
```

---

# 3. Sales-Cycle Measures

Sales cycle is defined as:

```text
engage_date -> close_date
```

The source dataset does not include an opportunity-created date, so this is not the complete lead-to-close lifecycle.

The fact table contains a derived `sales_cycle_days` value for closed opportunities.

---

## Average Sales Cycle Days

Validated result:

**48.0 days**

Typical DAX pattern:

```DAX
Average Sales Cycle Days =
AVERAGE ( fact_sales_pipeline[sales_cycle_days] )
```

Because open opportunities have no closed sales-cycle value, they are naturally excluded.

---

## Median Sales Cycle Days

Validated result:

**45 days**

Typical DAX pattern:

```DAX
Median Sales Cycle Days =
MEDIAN ( fact_sales_pipeline[sales_cycle_days] )
```

Median was included because the Lost-opportunity distribution is strongly skewed.

---

## Won Average Sales Cycle Days

Validated result:

**51.8 days**

Typical DAX pattern:

```DAX
Won Average Sales Cycle Days =
CALCULATE (
    [Average Sales Cycle Days],
    fact_sales_pipeline[deal_stage] = "Won"
)
```

---

## Won Median Sales Cycle Days

Validated result:

**57 days**

Typical DAX pattern:

```DAX
Won Median Sales Cycle Days =
CALCULATE (
    [Median Sales Cycle Days],
    fact_sales_pipeline[deal_stage] = "Won"
)
```

---

## Lost Average Sales Cycle Days

Validated result:

**41.5 days**

Typical DAX pattern:

```DAX
Lost Average Sales Cycle Days =
CALCULATE (
    [Average Sales Cycle Days],
    fact_sales_pipeline[deal_stage] = "Lost"
)
```

---

## Lost Median Sales Cycle Days

Validated result:

**14 days**

Typical DAX pattern:

```DAX
Lost Median Sales Cycle Days =
CALCULATE (
    [Median Sales Cycle Days],
    fact_sales_pipeline[deal_stage] = "Lost"
)
```

---

# 4. Engagement-Cohort Measures

The date model has:

- an active relationship from `dim_date[Date]` to `fact_sales_pipeline[close_date]`
- an inactive relationship from `dim_date[Date]` to `fact_sales_pipeline[engage_date]`

Engagement measures activate the inactive relationship with `USERELATIONSHIP`.

---

## Opportunities Reaching Engaging

**Business definition:** opportunities with an engagement date, evaluated by engagement date.

Validated total:

**8,300**

Typical DAX pattern:

```DAX
Opportunities Reaching Engaging =
CALCULATE (
    [Total Opportunities],
    USERELATIONSHIP (
        dim_date[Date],
        fact_sales_pipeline[engage_date]
    )
)
```

The 500 Prospecting opportunities do not have an engagement date and are excluded.

---

## Won Opportunities by Engagement Date

Typical DAX pattern:

```DAX
Won Opportunities by Engagement Date =
CALCULATE (
    [Won Opportunities],
    USERELATIONSHIP (
        dim_date[Date],
        fact_sales_pipeline[engage_date]
    )
)
```

---

## Lost Opportunities by Engagement Date

Typical DAX pattern:

```DAX
Lost Opportunities by Engagement Date =
CALCULATE (
    [Lost Opportunities],
    USERELATIONSHIP (
        dim_date[Date],
        fact_sales_pipeline[engage_date]
    )
)
```

---

## Closed Opportunities by Engagement Date

Typical DAX pattern:

```DAX
Closed Opportunities by Engagement Date =
CALCULATE (
    [Closed Opportunities],
    USERELATIONSHIP (
        dim_date[Date],
        fact_sales_pipeline[engage_date]
    )
)
```

---

## Won Revenue by Engagement Date

Typical DAX pattern:

```DAX
Won Revenue by Engagement Date =
CALCULATE (
    [Won Revenue],
    USERELATIONSHIP (
        dim_date[Date],
        fact_sales_pipeline[engage_date]
    )
)
```

---

## Engagement Cohort Win Rate

**Business definition:** eventual Won opportunities divided by the closed opportunities originating from each engagement cohort.

Typical DAX logic:

```DAX
Engagement Cohort Win Rate =
DIVIDE (
    [Won Opportunities by Engagement Date],
    [Closed Opportunities by Engagement Date]
)
```

Later cohorts may be less mature because some opportunities remain Engaging.

---

# 5. Agent Leadership Measures

The Sales Team page uses separate leadership measures instead of one composite ranking.

The leaders in the final report are:

| Measure | Agent | Result |
|---|---|---:|
| Won Revenue | Darcel Schlecht | $1,153,214 |
| Closed-Deal Win Rate | Hayden Neloms | 70.4% |
| Revenue per Opportunity | Reed Clapper | $1,850 |
| Average Won Deal Value | Elease Gluck | $3,615 |

The win-rate leader measure uses a minimum closed-opportunity threshold so that very small populations do not automatically win the ranking.

The project used a threshold of:

**50 closed opportunities**

This is a design choice rather than a universal business rule.

---

## Revenue Leader

Business logic:

1. Evaluate Won Revenue by sales agent.
2. Return the agent with the highest value in the current filter context.

The result dynamically changes under relevant slicers.

---

## Win-Rate Leader

Business logic:

1. Evaluate Closed-Deal Win Rate by agent.
2. Exclude agents with fewer than 50 closed opportunities.
3. Return the agent with the highest remaining rate.

This reduces the risk of a very small sample dominating the ranking.

---

## Efficiency Leader

Business logic:

1. Evaluate Revenue per Opportunity by agent.
2. Return the highest result.

Validated leader:

**Reed Clapper — $1,850 per opportunity**

---

## Deal-Value Leader

Business logic:

1. Evaluate Average Won Deal Value by agent.
2. Return the highest result.

Validated leader:

**Elease Gluck — $3,615**

---

# 6. Account Revenue Concentration Measures

Revenue concentration is calculated against the visible account population.

The final report uses `ALLSELECTED(dim_accounts[account])` logic so that concentration responds to report filters while ranking across the accounts that remain visible after those filters.

---

## Largest Account Revenue Share

Validated overall result:

**3.4%**

Conceptual logic:

```DAX
Largest Account Revenue Share =
DIVIDE (
    [Revenue of Highest-Revenue Visible Account],
    [Won Revenue Across Visible Accounts]
)
```

---

## Top 5 Account Revenue Share

Validated overall result:

**12.1%**

Conceptual logic:

1. Rank visible accounts by Won Revenue.
2. Sum the top five.
3. Divide by Won Revenue across all visible accounts.

---

## Top 10 Account Revenue Share

Validated overall result:

**20.7%**

The same pattern is used with the top ten visible accounts.

The Excel EDA also validated:

**Top 20 = 36.0%**

---

# Filter-Context Design

Several measures required deliberate interaction design outside the DAX itself.

## Close Period and Open Pipeline

The active date relationship uses `close_date`.

Open opportunities have no `close_date`.

Therefore, visuals representing the current/full open pipeline should not be allowed to disappear under a Close Period filter.

The report explicitly disables Close Period interaction with the relevant open-pipeline visuals.

---

## Engagement Cohorts

Engagement measures use `engage_date` through `USERELATIONSHIP`.

They are intentionally separated from the normal close-date timeline.

---

## Sales Cycle by Outcome

The comparison is designed to preserve both Won and Lost outcomes side by side.

The Deal Stage slicer does not collapse the comparison to a single outcome.

---

# Validation

The DAX layer reconciles to the Excel EDA.

Validated baseline totals include:

| Measure | Result |
|---|---:|
| Total Opportunities | 8,800 |
| Won Opportunities | 4,238 |
| Lost Opportunities | 2,473 |
| Closed Opportunities | 6,711 |
| Closed-Deal Win Rate | 63.2% |
| Open Opportunities | 2,089 |
| Open Pipeline Share | 23.7% |
| Won Revenue | $10,005,534 |
| Average Won Deal Value | $2,361 |
| Average Sales Cycle | 48.0 days |
| Median Sales Cycle | 45 days |
| Opportunities Reaching Engaging | 8,300 |

---

# Exact-Formula Source File

This document explains the analytical logic.

The repository should also include:

```text
dax/measures.dax
```

That file should contain the exact DAX copied from the final Power BI model.

Keeping the two files separate provides both:

- a readable explanation for reviewers
- a source-style measure file for technical inspection
