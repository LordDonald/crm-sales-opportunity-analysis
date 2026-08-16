# Insights & Recommendations

## Purpose

This document consolidates the management-facing findings from the CRM Sales Opportunity Analysis project.

The analysis is intentionally separated from the dashboard visuals so that stakeholders can distinguish:

- **what the data shows**
- **what the result may imply**
- **what action is justified**
- **where the available data does not support a stronger conclusion**

The findings below reconcile to the final Power BI report.

---

## 1. Regional Performance

### Finding

West generated the highest Won Revenue at **$3,568,647** and recorded the strongest regional closed-deal win rate at **63.9%**.

However, the regional win rates were close:

- West: **63.9%**
- East: **63.0%**
- Central: **62.6%**

Central handled the highest workload at **3,512 opportunities** and generated the highest Revenue per Agent at approximately **$304,208**.

East recorded the highest Average Won Deal Value at **$2,639**.

### Interpretation

No region leads every performance measure.

West leads total revenue, Central shows the highest workload and revenue productivity per agent, and East closes the highest-value Won deals on average.

### Recommendation

Evaluate regional performance using a balanced view of:

- revenue
- workload
- conversion
- deal value
- productivity per agent

Avoid ranking regions on total revenue alone.

---

## 2. Manager Performance

### Finding

Different managers lead different dimensions of performance.

- **Melvin Marxen:** highest Won Revenue at **$2,251,930**
- **Melvin Marxen:** highest Revenue per Agent at approximately **$375,322**
- **Cara Losch:** highest manager win rate at **64.4%**
- **Rocco Neubert:** highest Average Won Deal Value at **$2,837**

The Central comparison is particularly useful.

Melvin Marxen and Dustin Brinkmann carried similar workloads per agent:

- Melvin: approximately **321.5 opportunities per agent**
- Dustin: approximately **316.6 opportunities per agent**

Their win rates were also close:

- Melvin: **62.2%**
- Dustin: **63.0%**

Despite that, Melvin generated slightly more than twice Dustin's Won Revenue.

The strongest observable difference was Average Won Deal Value:

- Melvin: **$2,553**
- Dustin: **$1,465**

### Interpretation

The revenue gap cannot be explained by workload or win rate alone.

Deal value is a major contributor, but the current dataset does not establish why the average successful-deal values differ.

Possible drivers could include product mix, account allocation, territory or lead quality.

### Recommendation

Before using manager results for performance intervention or reward, compare:

- workload per agent
- product mix
- account allocation
- territory
- sales targets
- lead quality

The current data supports performance comparison, but not a causal judgement about management quality.

---

## 3. Sales-Agent Performance

### Finding

No single sales agent leads every performance measure.

The four leadership positions are:

| Measure | Agent | Result |
|---|---|---:|
| Won Revenue | Darcel Schlecht | $1,153,214 |
| Win Rate | Hayden Neloms | 70.4% |
| Revenue per Opportunity | Reed Clapper | $1,850 |
| Average Won Deal Value | Elease Gluck | $3,615 |

Darcel Schlecht generated the highest Won Revenue from:

- **747 opportunities**
- **349 wins**
- **63.1% win rate**

Darcel also recorded the second-highest Average Won Deal Value at **$3,304**, behind Elease Gluck at **$3,615**.

The volume behind the averages differs sharply:

- Elease: **80 wins**
- Darcel: **349 wins**

### Interpretation

Darcel's result is not only a volume story.

Sustaining a near-leading Average Won Deal Value across more than four times Elease's Won volume is materially different from achieving a marginally higher average over a much smaller number of wins.

The results also demonstrate why a universal "best agent" ranking would be misleading.

### Recommendation

Use a multi-metric framework for:

- coaching
- recognition
- workload review
- performance management

Compare agents using revenue, conversion, deal value, workload and efficiency rather than one metric.

---

## 4. Agents With No Assigned Opportunities

### Finding

**5 of 35 sales agents** had no assigned opportunities.

The unassigned agents are not concentrated under one manager.

### Interpretation

A zero sales result without assigned opportunities is not evidence of poor performance.

The pattern is more consistent with a roster, assignment or CRM process issue than with individual sales underperformance.

### Recommendation

Reconcile the sales-agent roster against:

- active/inactive employment status
- territory assignments
- opportunity-allocation rules
- CRM user status
- role changes

Do this before including the five agents in performance comparisons.

---

## 5. Product Performance

### Finding

Product results show different combinations of volume, conversion and value.

| Product | Opportunities | Won | Win Rate | Won Revenue | Avg Won Deal |
|---|---:|---:|---:|---:|---:|
| GTK 500 | 40 | 15 | 60.0% | $400,612 | $26,707 |
| GTX Basic | 1,866 | 915 | 63.7% | $499,263 | $546 |
| GTX Plus Basic | 1,383 | 653 | 62.1% | $705,275 | $1,080 |
| GTX Plus Pro | 968 | 479 | 64.3% | $2,629,651 | $5,490 |
| GTX Pro | 1,480 | 729 | 63.6% | $3,510,578 | $4,816 |
| MG Advanced | 1,412 | 654 | 60.3% | $2,216,387 | $3,389 |
| MG Special | 1,651 | 793 | 64.8% | $43,768 | $55 |

Key contrasts:

- **GTX Pro:** highest Won Revenue at **$3.51M**
- **GTX Plus Pro:** second-highest Won Revenue at **$2.63M** from only **968 opportunities**
- **MG Special:** highest product win rate at **64.8%**, but only **$43.8K** revenue
- **GTK 500:** only **40 opportunities**, but **$26.7K** Average Won Deal Value

### Interpretation

Revenue is produced through the interaction of:

> **Volume × Conversion × Deal Value**

A product can convert well and still generate little revenue if its deal value is low.

A high-value product can produce meaningful revenue from low volume.

### Recommendation

Evaluate products using at least:

- opportunity volume
- Won volume
- closed-deal win rate
- Average Won Deal Value
- Won Revenue
- Revenue per Opportunity

Do not rank products using revenue or conversion alone.

---

## 6. Product-Series Performance

### Finding

The GTX series generated the highest total Won Revenue at **$7,344,767** from:

- **5,697 opportunities**
- **2,776 wins**
- **63.4% win rate**

GTK generated the highest Revenue per Opportunity at approximately **$10,015**, but it contained only 40 opportunities.

MG generated approximately **$738 Revenue per Opportunity**, influenced heavily by the low value of MG Special.

### Interpretation

Series-level results are useful for portfolio analysis, but products within the same series can behave very differently.

GTX alone spans Average Won Deal Values from approximately **$546 to $5,490**.

### Recommendation

Use product series for executive portfolio views, but retain product-level analysis for operational decisions.

---

## 7. Sector Performance

### Finding

Retail generated the highest total sector Won Revenue at **$1,867,528**.

Retail also had:

- the largest account base: **17**
- the highest assigned opportunity volume: **1,397**
- the highest Won volume: **799**

Its win rate of **63.1%** was close to the overall rate, and its Revenue per Account of **$109,855** was below the sector mean of **$117,712**.

Software produced the strongest normalized account results:

- Revenue per Account: **$153,991**
- Opportunities per Account: **108.1**

Software achieved those figures from only **7 accounts**.

Marketing recorded the highest sector win rate at **64.8%**.

### Interpretation

Retail's leadership is primarily scale-driven.

Software generates more commercial activity and revenue relative to the size of its account base.

These are different forms of commercial strength.

### Recommendation

Use both total and normalized metrics when comparing sectors.

Investigate what drives Software's stronger account economics, including:

- product mix
- account size
- account quality
- sales-agent allocation
- territory
- customer demand

---

## 8. Customer Revenue Concentration

### Finding

The five highest-revenue accounts are:

| Account | Won Revenue |
|---|---:|
| Kan-code | $341,455 |
| Konex | $269,245 |
| Condax | $206,410 |
| Cheers | $198,020 |
| Hottechi | $194,957 |

Revenue concentration is:

- Largest account: **3.4%**
- Top 5: **12.1%**
- Top 10: **20.7%**
- Top 20: **36.0%**
- Remaining 65 accounts: **64.0%**

### Interpretation

Won Revenue is relatively diversified across the customer base.

There is no evidence of extreme dependence on one or two customers.

### Recommendation

Continue monitoring concentration while adding future customer-value measures such as:

- retention
- customer lifetime value
- future pipeline
- gross margin
- cost to serve
- contract renewal potential

Revenue concentration alone does not describe strategic customer importance.

---

## 9. Quarter-End Closing Pattern

### Finding

The final month of each complete quarter recorded materially stronger closed-deal win rates:

- June: **82.8%**
- September: **79.2%**
- December: **78.5%**

Across the complete quarters:

| Quarter Position | Wins | Losses | Win Rate | Won Revenue |
|---|---:|---:|---:|---:|
| First month | 872 | 907 | 49.0% | $2,150,844 |
| Second month | 1,290 | 1,068 | 54.7% | $3,014,715 |
| Third month | 1,545 | 382 | 80.2% | $3,705,303 |

The stronger third-month revenue was driven mainly by successful closing volume rather than unusually large average deals.

### Interpretation

The dataset confirms a recurring quarter-end closing pattern.

It does **not** establish the cause.

Possible explanations include:

- sales targets
- incentive timing
- approval processes
- customer budgeting cycles
- discounts
- closing practices

Those variables are unavailable.

### Recommendation

Investigate operational behaviour around quarter end and monitor whether the pattern persists across future periods.

Do not describe the current result as confirmed seasonality or as caused by incentives without additional evidence.

---

## 10. Sales-Cycle Behaviour

### Finding

| Outcome | Average Cycle | Median Cycle |
|---|---:|---:|
| Won | 51.8 days | 57 days |
| Lost | 41.5 days | 14 days |
| Overall | 48.0 days | 45 days |

Lost opportunities have a much lower median than average.

### Interpretation

Many Lost opportunities resolve quickly, while a smaller number remain active much longer and pull the mean upward.

The data does not prove that a longer sales cycle causes an opportunity to be Won.

### Recommendation

Review long-running Lost opportunities separately.

Potential operational questions include:

- Could some have been disqualified earlier?
- Were they stalled due to approvals or negotiations?
- Were they inflating pipeline forecasts?
- Were sales resources being spent on low-probability opportunities for too long?

Structured loss reasons and stage-transition history would be required for stronger answers.

---

## 11. Sales Cycle by Region and Product

### Finding

Regional average cycle times are close:

- Central: **48.6 days**
- West: **48.0 days**
- East: **47.1 days**

Excluding GTK 500, product-level average cycle times span only:

**45.7 to 49.9 days**

a range of just:

**4.2 days**

GTK 500 has an overall average of **53.7 days**, but only **25 closed opportunities**.

Its regional split is approximately:

- Central: 1
- East: 1
- West: 23

### Interpretation

Sales-cycle duration varies relatively little by region and by product once the small GTK 500 population is treated appropriately.

The extreme GTK 500 regional cells are sample-size artefacts rather than reliable process differences.

The data also does not support a simple relationship between higher product prices and longer cycle duration.

### Recommendation

Avoid operational conclusions from low-volume segments.

Introduce minimum-volume thresholds or explicit sample-size indicators in future production reporting.

---

## 12. Open Pipeline

### Finding

The open pipeline contains:

- **2,089 opportunities**
- **23.7% of all opportunities**
- **1,589 Engaging**
- **500 Prospecting**

### Interpretation

Almost one-quarter of the opportunity population remains unresolved.

This represents a meaningful future workload and forecasting population.

### Recommendation

Monitor:

- pipeline ageing
- stage progression
- stalled opportunities
- opportunity ownership
- forecast status

Opportunity creation dates and stage-transition timestamps would make this substantially stronger.

---

## 13. Unassigned Open Opportunities

### Finding

**1,425 open opportunities have no assigned account.**

No Won or Lost opportunity is missing an account.

### Interpretation

Historical closed customer and sector analysis remains complete.

However, current open-pipeline analysis cannot fully describe which accounts or sectors own the unresolved opportunity population.

### Recommendation

Make account-assignment completeness a CRM operational KPI.

Investigate:

- when account assignment should occur
- whether the blank rate is expected at Prospecting
- how quickly Engaging opportunities should acquire an account
- whether assignment delays affect forecasting or sales ownership

---

## 14. Engagement Cohorts

### Finding

A total of **8,300 opportunities** reached Engaging.

July 2017 was the largest engagement cohort at approximately **1,198 opportunities**.

### Interpretation

Later engagement cohorts can still contain unresolved opportunities.

Their apparent win rates are therefore not always directly comparable with mature earlier cohorts.

A recent cohort with a lower current win rate has not necessarily performed worse; it may simply be incomplete.

### Recommendation

For future production reporting:

- add cohort maturity indicators
- compare cohorts after a standard maturation period
- distinguish current conversion from eventual conversion

---

# Analytical Limitations

## Profitability

The dataset does not contain reliable:

- product cost
- gross margin
- discount
- commission
- promotional cost
- cost to serve

Therefore:

> **Revenue performance is not profitability performance.**

No product, account, agent or sector should be called "most profitable" from this dataset.

---

## Full Funnel Duration

Sales cycle is measured from:

`engage_date -> close_date`

The dataset does not contain opportunity creation dates or complete stage-transition timestamps.

The reported cycle therefore does not measure the complete lead-to-close process.

---

## Root-Cause Variables

The source does not include structured:

- reasons for loss
- campaign attribution
- competitor information
- salesperson activity history
- incentive data
- targets
- customer intent
- discount history

The project can establish **what happened** much more confidently than **why it happened**.

---

# Management Priorities Suggested by the Analysis

If this were an operational business review, the highest-priority follow-up areas would be:

1. **Investigate the recurring quarter-end closing pattern**
2. **Improve account assignment across the open pipeline**
3. **Review long-running opportunities that eventually become Lost**
4. **Use multi-metric performance assessment for sales agents and managers**
5. **Investigate why Software accounts generate stronger normalized commercial activity**
6. **Add profitability data before making product or customer profit decisions**
7. **Add lifecycle and loss-reason fields for stronger root-cause analysis**

---

# Final Interpretation

The strongest finding from the project is not that one region, manager, agent or product is universally best.

Instead, the data shows that commercial performance has several dimensions:

- scale
- conversion
- deal value
- workload
- efficiency
- customer concentration
- sales-cycle behaviour

The final dashboard was therefore designed to let stakeholders move between those dimensions rather than force them into a single ranking.
