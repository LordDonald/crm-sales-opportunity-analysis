# CRM Sales Opportunity Analysis

## Project Journal

> End-to-end analysis: Excel exploratory analysis, Power Query transformation, Power BI star-schema modelling, DAX, quality assurance and dashboard delivery.
>
> **Validation status:** All figures in this journal reconcile to the final Power BI report.

<details>
<summary><strong>Table of contents</strong></summary>

- [1. Initial Project Reflection](#section-1)
- [2. Project Setup and Source Data](#section-2)
- [3. Understanding the Dataset Structure](#section-3)
- [4. Creating the Working Data Dictionary](#section-4)
- [5. Verifying the Sales-Pipeline Grain](#section-5)
- [6. Verifying the Dimension Tables](#section-6)
- [7. Referential-Integrity Testing](#section-7)
- [8. Investigating the Blank Accounts](#section-8)
- [9. Validating Deal Stages and Dates](#section-9)
- [10. Understanding Close Values and Revenue](#section-10)
- [11. Account-Sector Profiling](#section-11)
- [12. Sales-Team Structure](#section-12)
- [13. Product and Price Profiling](#section-13)
- [14. Date Coverage](#section-14)
- [15. Establishing the Baseline KPIs](#section-15)
- [16. Monthly Performance Analysis](#section-16)
- [17. Investigating the Quarter-End Pattern](#section-17)
- [18. Data Cleaning and Transformation](#section-18)
- [19. Creating the Analysis-Ready Excel Table](#section-19)
- [20. Regional Performance](#section-20)
- [21. Manager Performance](#section-21)
- [22. Sales-Agent Performance](#section-22)
- [23. Product Performance](#section-23)
- [24. Product-Series Performance](#section-24)
- [25. Sector Performance](#section-25)
- [26. Account Revenue Concentration](#section-26)
- [27. Sales-Cycle Analysis](#section-27)
- [28. Sales Cycle by Region](#section-28)
- [29. Sales Cycle by Product](#section-29)
- [30. Building the Power BI Analytical Model](#section-30)
- [31. Building the Date Dimension](#section-31)
- [32. DAX Measure Development](#section-32)
- [33. Power BI QA and Reconciliation](#section-33)
- [34. Designing the Dashboard Architecture](#section-34)
- [35. Executive Overview](#section-35)
- [36. Sales Team Performance Page](#section-36)
- [37. Product & Customer Performance Page](#section-37)
- [38. Pipeline & Sales-Cycle Page](#section-38)
- [39. Slicer and Interaction Design](#section-39)
- [40. Conditional Formatting and Visual Design](#section-40)
- [41. Final Executive Findings](#section-41)
- [42. Pipeline and Process Findings](#section-42)
- [43. Analytical Limitations](#section-43)
- [44. Key Analytical Decisions](#section-44)
- [45. Challenges During the Project](#section-45)
- [46. What I Learned](#section-46)
- [47. Future Enhancements](#section-47)
- [48. Final Project Reflection](#section-48)

</details>

---

<a id="section-1"></a>

## 1. Initial Project Reflection

I chose the CRM Sales Opportunities dataset because it was recommended to me, but I decided to continue with it because I wanted to develop a stronger understanding of how data analysis can support commercial decision-making.

Sales is a core business function, and I wanted this project to be more than an exercise in creating charts. I wanted to understand how a sales pipeline behaves, how revenue is generated, how performance should be compared fairly and what information a Sales Director or Sales Operations manager would actually need from an analytics solution.

I expected revenue to be an important part of the analysis, but I did not want to decide the conclusions before examining the data. My initial areas of interest included overall pipeline performance, team and agent performance, product performance, customer and sector behaviour, sales trends and possible inefficiencies in the sales process.

The likely users of the final solution were a Sales Director, sales managers, Sales Operations managers and product or commercial managers. These stakeholders would need to understand not only how much revenue was generated, but what was driving the result, whether performance differed across teams and products, where opportunities were being lost and where further investigation might be required.

I had limited direct experience analysing sales-pipeline data at the beginning of the project. My approach was therefore to work carefully through the dataset, document my assumptions, validate the structure before calculating KPIs and research or investigate any business concept I did not fully understand.

I deliberately began in Excel rather than Power BI. I wanted to understand the data before trying to present it. Power BI would come later, after I had identified what the data could reliably answer.

I also wanted to treat the project as an end-to-end analytics exercise. That meant documenting my process, analytical decisions, quality checks, findings, limitations and reasoning so that another analyst or stakeholder could follow how I reached the final output.

<a id="section-2"></a>

## 2. Project Setup and Source Data

I began by creating a structured project workspace for the raw data, Excel exploratory analysis, Power BI development, documentation, QA outputs, exports and project notes.

The source dataset contained five CSV files:

- `sales_pipeline.csv`
- `accounts.csv`
- `sales_teams.csv`
- `products.csv`
- `data_dictionary.csv`

I kept the raw files unchanged throughout the project. I wanted to preserve the original source for traceability, so any cleaning or standardisation would be performed in Power Query rather than directly inside the CSV files.

I imported the files into Excel through Power Query. This created repeatable connections between the source data and the analytical workbook rather than relying on manual copy-and-paste processes.

The initial row counts were:

| Table | Records |
| --- | --- |
| Sales pipeline | 8,800 |
| Accounts | 85 |
| Sales teams | 35 |
| Products | 7 |
| Data dictionary | 21 |

All imported record counts matched the expected totals.

<a id="section-3"></a>

## 3. Understanding the Dataset Structure

Before carrying out the main analysis, I created a data inventory to understand the grain and expected role of each table.

The sales_pipeline table appeared to be the central transaction table, with one row expected to represent one sales opportunity.

The other three business tables appeared to provide descriptive attributes:

- sales_teams described each sales agent, manager and regional office.
- products described each product, product series and reference sales price.
- accounts described each customer account, sector and company characteristics.

The expected relationships were:

- Sales pipeline to Sales teams through `sales_agent`
- Sales pipeline to Products through product
- Sales pipeline to Accounts through account

I did not assume these relationships were valid simply because the field names appeared to match. I first needed to confirm that the proposed dimension keys were unique and that the values in the fact table matched the corresponding reference tables.

This was an important early lesson in the project: data modelling begins before the Power BI model view. The grain, keys and relationships need to be understood first.

<a id="section-4"></a>

## 4. Creating the Working Data Dictionary

I created a working data dictionary to document each sales-pipeline field, its expected data type, business meaning, structural role and expected blank-value behaviour.

This step helped me challenge some of my initial assumptions.

For example, I first thought of `engage_date` as the date an opportunity entered the sales funnel. After reviewing the source description, I understood that it specifically represented the date the opportunity reached the Engaging stage.

I documented that:

- `opportunity_id` should uniquely identify each opportunity.
- `sales_agent`, product and account should connect the pipeline to supporting reference tables.
- `deal_stage` describes the current status or final outcome.
- `close_date` should only exist for closed opportunities.
- `close_value` required investigation before being treated as realised revenue.

This prevented me from creating calculations that were technically valid but based on an incorrect business interpretation.

<a id="section-5"></a>

## 5. Verifying the Sales-Pipeline Grain

I tested `opportunity_id` to confirm the grain of the sales-pipeline table. The results were:

- 8,800 total records
- 8,800 distinct opportunity IDs
- 0 blank opportunity IDs
- 0 duplicate opportunity IDs

This confirmed that one row represents one sales opportunity and that `opportunity_id` is a valid primary key.

That validation was important because opportunity counts, conversion rates and revenue-per-opportunity measures could now be calculated without duplicate opportunities inflating the results.

<a id="section-6"></a>

## 6. Verifying the Dimension Tables

I performed similar key checks on the reference tables. The results confirmed:

- 35 unique sales agents
- 7 unique products
- 85 unique accounts
- No blank or duplicate keys in any of the three dimension candidates

This made the tables structurally suitable for the one side of one-to-many relationships in the final Power BI model.

The exercise reinforced that a field should not be accepted as a valid key because it merely appears unique when browsing the data. It should be tested.

<a id="section-7"></a>

## 7. Referential-Integrity Testing

I next tested whether the foreign-key values in the sales pipeline matched their corresponding dimension tables.

The sales-agent results were clean: no blank sales agents and no unmatched populated sales agents.

The account check showed 1,425 blank account values, with no unmatched populated accounts.

The product check identified a significant data-quality issue. A total of 1,480 pipeline records contained `GTXPro`, while the product reference table contained GTX Pro.

Although the names clearly referred to the same product, Power BI and Excel would treat them as different text values. Without correction, these 1,480 records would fail to match the product dimension.

This was one of the first places in the project where referential-integrity testing directly prevented a reporting error.

<a id="section-8"></a>

## 8. Investigating the Blank Accounts

I did not immediately treat the 1,425 blank account values as bad data. I first analysed where the blanks occurred.

| Deal Stage | Opportunities | Blank Accounts | Blank Rate |
| --- | --- | --- | --- |
| Prospecting | 500 | 337 | 67.4% |
| Engaging | 1,589 | 1,088 | 68.5% |
| Won | 4,238 | 0 | 0% |
| Lost | 2,473 | 0 | 0% |

No Won or Lost opportunity was missing an account. Every closed opportunity therefore had an identified customer.

The blanks occurred only in the open stages, which suggested they were reasonable business blanks rather than general data failures. An opportunity may still be early enough in the sales process that the account has not yet been confirmed or assigned.

I therefore decided not to delete the records or invent replacement account values.

However, this still created an analytical limitation: 1,425 open opportunities could not be attributed to an account or sector.

That would later become an important dashboard finding rather than something to hide during cleaning. It now appears as a headline KPI on the Executive Overview page.

<a id="section-9"></a>

## 9. Validating Deal Stages and Dates

I tested whether `deal_stage`, `engage_date` and `close_date` behaved consistently. The results showed:

- Prospecting opportunities did not have an engagement date.
- Engaging opportunities had an engagement date but no close date.
- Won and Lost opportunities had both engagement and close dates.
- No open opportunity had a close date.
- No closed opportunity was missing a close date.
- No close date occurred before its engagement date.

This confirmed that the stage and date fields were structurally consistent.

I therefore established two different analytical date concepts: `close_date` for Won and Lost outcomes, revenue and closing performance, and `engage_date` for analysing when opportunities reached the Engaging stage.

This distinction later became important in the Power BI model because one date table needed to support two different date relationships.

<a id="section-10"></a>

## 10. Understanding Close Values and Revenue

I profiled `close_value` by deal stage. The results showed:

- Prospecting and Engaging opportunities had blank close values.
- All 4,238 Won opportunities had positive close values.
- All 2,473 Lost opportunities had close values of zero.
- No negative values existed.
- Total close value for Won opportunities was $10,005,534.

The overall average of populated close values was approximately $1,491, but that figure included all Lost opportunities as zeros. The average value of a Won opportunity was approximately $2,361.

I therefore defined Won Revenue as the sum of `close_value` where `deal_stage` equals Won.

I retained Lost values of zero because they correctly represented unsuccessful closed deals, but excluded them when calculating the average value of successful deals.

<a id="section-11"></a>

## 11. Account-Sector Profiling

The accounts table contained 10 sectors.

Retail had the highest number of accounts, with 17 accounts representing 20% of the customer base. Employment had the fewest, with 4 accounts representing 4.7%.

I also identified a spelling error. Twelve account records were recorded as `technolgy` instead of technology. The cleaned sector table later confirmed that technology contains exactly 12 accounts, which means the entire sector was affected by the misspelling rather than only part of it.

The unequal sector sizes were analytically important. A sector with more accounts could naturally generate more opportunities and revenue.

I therefore decided that sector analysis should eventually include normalised measures such as Revenue per Account, Opportunities per Account, Win Rate and Average Won Deal Value, rather than relying only on total revenue.

<a id="section-12"></a>

## 12. Sales-Team Structure

The sales-team table contained 3 regional offices, 6 managers and 35 sales agents.

East and West each had 12 agents, while Central had 11. Most managers had 6 agents, while Dustin Brinkmann had 5.

This meant that raw manager or regional totals would not always be directly comparable. Larger teams could naturally receive more opportunities and generate more revenue.

I therefore planned to include normalised measures such as Revenue per Agent, Opportunities per Agent and Wins per Agent alongside total metrics.

<a id="section-13"></a>

## 13. Product and Price Profiling

The product table contained seven products across three series: GTK with 1 product, GTX with 4 products and MG with 2 products.

The lowest-priced product was MG Special at $55. The highest-priced product was GTK 500 at $26,768.

The price differences were large enough that total revenue could not be interpreted without considering both volume and deal value.

A low-priced product could convert extremely well and still produce relatively little revenue, while a high-priced product could generate substantial revenue from only a small number of opportunities.

This became a recurring theme in the later product analysis.

<a id="section-14"></a>

## 14. Date Coverage

The engagement data covered 20 October 2016 to 27 December 2017. The closing data covered 1 March 2017 to 31 December 2017.

There were 8,300 populated engagement dates and 500 blanks, and 6,711 populated close dates and 2,089 blanks.

The 500 blank engagement dates matched Prospecting opportunities. The 2,089 blank close dates matched the open pipeline of 500 Prospecting and 1,589 Engaging opportunities.

The different date ranges meant I needed to be careful with time comparisons. The close-date data did not contain January or February 2017, and the engagement data only contained part of 2016.

I therefore avoided interpreting incomplete periods as genuine performance declines.

<a id="section-15"></a>

## 15. Establishing the Baseline KPIs

After validating the main business fields, I calculated the initial pipeline KPIs.

| Metric | Result |
| --- | --- |
| Total Opportunities | 8,800 |
| Prospecting | 500 |
| Engaging | 1,589 |
| Open Opportunities | 2,089 |
| Won Opportunities | 4,238 |
| Lost Opportunities | 2,473 |
| Closed Opportunities | 6,711 |
| Closed-Deal Win Rate | 63.2% |
| Closed-Deal Loss Rate | 36.8% |
| Open Pipeline Share | 23.7% |
| Won Revenue | $10,005,534 |
| Average Won Deal Value | $2,361 |

I calculated the win rate using only Won and Lost opportunities because open opportunities had not yet reached a final outcome.

These figures established the overall scale and health of the pipeline, but they did not explain the drivers behind the result.

<a id="section-16"></a>

## 16. Monthly Performance Analysis

I analysed closed performance from March to December 2017.

June generated the highest monthly Won Revenue at approximately $1.34M. July generated the lowest at approximately $697K.

June also recorded the highest closed-deal win rate at approximately 82.8%, while April recorded the lowest at approximately 48.6%.

June and March both recorded 531 Won opportunities, but June generated more revenue because its average Won deal value was higher.

This showed that revenue performance depended on both the number of wins and the value of those wins.

A repeated pattern also appeared around March, June, September and December, the quarter-ending months.

<a id="section-17"></a>

## 17. Investigating the Quarter-End Pattern

I grouped the complete quarters by first, second and third month. March was excluded from the grouped comparison because the first quarter was incomplete.

| Quarter Position | Wins | Losses | Win Rate | Revenue | Avg Won Deal |
| --- | --- | --- | --- | --- | --- |
| First Month | 872 | 907 | 49.0% | $2,150,844 | $2,467 |
| Second Month | 1,290 | 1,068 | 54.7% | $3,014,715 | $2,337 |
| Third Month | 1,545 | 382 | 80.2% | $3,705,303 | $2,398 |

The third month of each complete quarter produced the strongest win rate and revenue.

Importantly, the result was not caused by unusually large average deals. The first month actually had a slightly higher average Won deal value.

The quarter-end result therefore appeared to be driven primarily by a larger number of opportunities closing successfully.

I treated this as an observed pattern rather than a causal conclusion.

Possible explanations included sales targets, incentive timing, approval processes, customer budgeting cycles, discounts or changes in product mix, but none of those variables existed in the dataset.

<a id="section-18"></a>

## 18. Data Cleaning and Transformation

### Product standardisation

I corrected the `GTXPro` versus GTX Pro mismatch through a referenced Power Query rather than modifying the raw source.

After standardisation, unmatched product records decreased from 1,480 to 0 and total pipeline records remained 8,800. This confirmed the transformation corrected the relationship without deleting or duplicating records.

### Sector standardisation

I also created a cleaned version of the account table and replaced `technolgy` with technology. All 85 account records remained after the transformation.

Both changes were recorded in a transformation log describing the original issue, evidence, transformation, business reason, tool used and validation result.

<a id="section-19"></a>

## 19. Creating the Analysis-Ready Excel Table

For the remaining Excel EDA, I created an analysis-ready query called `sales_analysis`.

I used Left Outer joins so that all pipeline records would remain while descriptive fields were added from the reference tables. The merges added Manager and Regional Office from Sales Teams, Product Series and Reference Sales Price from Products, and Sector and company characteristics from Accounts.

I validated each merge separately. The final analysis table retained all 8,800 opportunities, and all populated sales-agent, product and account values matched successfully.

The 1,425 account-related blanks remained because they were genuine open-pipeline records without assigned accounts.

I used this flattened table only for exploratory analysis. I did not intend to reproduce the same structure in Power BI because a star schema would provide a cleaner analytical model.

<a id="section-20"></a>

## 20. Regional Performance

I compared the three regions using opportunity volume, Won opportunities, win rate, revenue, average Won deal value, revenue per agent and opportunities per agent.

| Region | Agents | Total Opps | Won | Lost | Win Rate | Revenue | Avg Won Deal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Central | 11 | 3,512 | 1,629 | 975 | 62.6% | $3,346,293 | $2,054 |
| West | 12 | 2,997 | 1,438 | 811 | 63.9% | $3,568,647 | $2,482 |
| East | 12 | 2,291 | 1,171 | 687 | 63.0% | $3,090,594 | $2,639 |
| Total | 35 | 8,800 | 4,238 | 2,473 | 63.2% | $10,005,534 | $2,361 |

West generated the highest Won Revenue at $3,568,647 and had the highest regional win rate at 63.9%.

East recorded the highest Average Won Deal Value at $2,639, showing that its successful deals were generally more valuable even though the region handled the fewest opportunities and generated the lowest total revenue.

Central handled the highest workload at 3,512 opportunities and generated the highest revenue per agent at approximately $304,208, compared with approximately $297,387 in West and $257,550 in East. Central also carried the highest workload per agent at 319.3 opportunities, against 249.8 in West and 190.9 in East.

Central also produced the highest number of Won opportunities at 1,629 despite having only 11 agents compared with 12 in East and West. However, it recorded the lowest average Won deal value at $2,054.

The regional analysis showed that no single region led every measure.

Revenue alone would therefore be an incomplete way of ranking regional performance. Central's high workload could indicate strong productivity, but it could equally represent a capacity concern that the dataset cannot resolve.

<a id="section-21"></a>

## 21. Manager Performance

I analysed the six sales managers using team size, opportunity volume, wins, losses, win rate, revenue, average Won deal value, revenue per agent and opportunities per agent.

| Manager | Region | Agents | Total Opps | Won | Win Rate | Revenue | Avg Won Deal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Melvin Marxen | Central | 6 | 1,929 | 882 | 62.2% | $2,251,930 | $2,553 |
| Dustin Brinkmann | Central | 5 | 1,583 | 747 | 63.0% | $1,094,363 | $1,465 |
| Summer Sewald | West | 6 | 1,701 | 828 | 64.3% | $1,964,750 | $2,373 |
| Celia Rouche | West | 6 | 1,296 | 610 | 63.4% | $1,603,897 | $2,629 |
| Rocco Neubert | East | 6 | 1,327 | 691 | 62.1% | $1,960,545 | $2,837 |
| Cara Losch | East | 6 | 964 | 480 | 64.4% | $1,130,049 | $2,354 |

Melvin Marxen generated the highest Won Revenue at $2,251,930 and the highest Revenue per Agent at approximately $375,322.

Cara Losch recorded the strongest manager win rate at 64.4%. Rocco Neubert recorded the highest Average Won Deal Value at $2,837.

The comparison between Melvin Marxen and Dustin Brinkmann was particularly useful.

Both operate in Central and carry almost identical workloads per agent, approximately 321.5 opportunities for Melvin against 316.6 for Dustin, with similar win rates of 62.2% and 63.0%. Melvin nonetheless generated slightly more than twice Dustin's revenue.

The main difference was successful-deal value. Melvin's average Won deal was $2,553 compared with $1,465 for Dustin.

This showed why manager performance could not be evaluated using win rate alone. Two managers converting at effectively the same rate, with effectively the same workload per agent, produced very different revenue outcomes because of the value of the deals they closed.

The dataset does not explain why the deal values differ. Product allocation, account allocation, territory and lead quality would all need to be examined before the difference could be attributed to management.

<a id="section-22"></a>

## 22. Sales-Agent Performance

I compared active agents across multiple performance measures rather than forcing them into a single composite ranking.

The four leadership positions were:

| Measure | Agent | Result |
| --- | --- | --- |
| Revenue Leader | Darcel Schlecht | $1,153,214 |
| Win-Rate Leader | Hayden Neloms | 70.4% |
| Efficiency Leader | Reed Clapper | $1,850 revenue per opportunity |
| Deal-Value Leader | Elease Gluck | $3,615 average Won deal |

Darcel Schlecht generated the highest Won Revenue at $1,153,214, more than double the next highest agent, from 747 opportunities and 349 wins at a 63.1% win rate. Darcel also recorded the second-highest Average Won Deal Value at $3,304, behind Elease Gluck at $3,615. The volume behind those two figures differs sharply: Elease's average rests on 80 wins, Darcel's on 349. Darcel therefore sustained a near-leading deal value across more than four times the closed-deal volume, which is a stronger result than the rank alone conveys.

The next four agents by revenue were Vicki Laflamme at $478,396, Kary Hendrixson at $454,298, Cassey Cress at $450,489 and Donn Cantrell at $445,860. The gap between first and second place is substantial and is driven largely by volume.

Hayden Neloms recorded the highest win rate at 70.4% across 202 opportunities. The result came from a smaller opportunity population than several other agents, showing why win rate alone could not define the strongest performer.

Elease Gluck recorded the highest Average Won Deal Value at $3,615, followed closely by Rosalina Dieter at $3,269. Reed Clapper recorded the highest Revenue per Opportunity at $1,850, generating $438,336 from 237 opportunities.

No agent ranked first across all measures. This was one of the clearest findings in the project: salesperson performance is multidimensional.

The analysis also identified 5 of the 35 sales agents with no assigned opportunities.

Those five are distributed one per management team rather than concentrated under a single manager. Each of the six managers displays five agents in the performance matrix, and each visible team's won opportunities sum exactly to the manager subtotal, so the missing agent in every team is an unassigned one. Dustin Brinkmann's team of five is fully accounted for, which is consistent with it being the only genuinely smaller team.

I deliberately did not describe those agents as poor performers. Without allocated opportunities, there was no basis for evaluating their sales performance. The even distribution across teams argues against a single-team allocation failure and points instead to something systematic, which makes it a CRM process question rather than a performance question.

<a id="section-23"></a>

## 23. Product Performance

| Product | Total Opps | Won | Win Rate | Revenue | Avg Won Deal |
| --- | --- | --- | --- | --- | --- |
| GTK 500 | 40 | 15 | 60.0% | $400,612 | $26,707 |
| GTX Basic | 1,866 | 915 | 63.7% | $499,263 | $546 |
| GTX Plus Basic | 1,383 | 653 | 62.1% | $705,275 | $1,080 |
| GTX Plus Pro | 968 | 479 | 64.3% | $2,629,651 | $5,490 |
| GTX Pro | 1,480 | 729 | 63.6% | $3,510,578 | $4,816 |
| MG Advanced | 1,412 | 654 | 60.3% | $2,216,387 | $3,389 |
| MG Special | 1,651 | 793 | 64.8% | $43,768 | $55 |
| Total | 8,800 | 4,238 | 63.2% | $10,005,534 | $2,361 |

GTX Pro generated the highest total Won Revenue at $3,510,578. Its result was supported by a combination of strong opportunity volume at 1,480, substantial win volume at 729 and a relatively high average Won deal value of $4,816.

GTX Plus Pro produced the second-highest revenue at $2,629,651 from only 968 opportunities, the third-lowest volume in the catalogue. It combined the second-highest win rate at 64.3% with the highest average Won deal value among the high-volume products at $5,490. This is the clearest example in the catalogue of revenue generated through value and conversion rather than scale.

MG Special recorded the highest product win rate at 64.8% and produced 793 wins. However, it generated only $43,768 in revenue because its Average Won Deal Value was only $55.

GTX Basic had the highest opportunity volume at 1,866 opportunities, but generated only $499,263 because its average deal value was $546.

GTK 500 provided the opposite example. It had only 40 opportunities and 15 wins, but recorded an Average Won Deal Value of $26,707 and Revenue per Opportunity of approximately $10,015. Its high value allowed it to generate $400,612 despite very low opportunity volume, more than GTX Basic achieved from 1,866 opportunities.

The product analysis made it clear that revenue is created through the interaction of volume, conversion and deal value. No one of these measures was sufficient by itself.

<a id="section-24"></a>

## 24. Product-Series Performance

| Series | Products | Opportunities | Won | Revenue | Revenue per Opp |
| --- | --- | --- | --- | --- | --- |
| GTK | 1 | 40 | 15 | $400,612 | $10,015 |
| GTX | 4 | 5,697 | 2,776 | $7,344,767 | $1,289 |
| MG | 2 | 3,063 | 1,447 | $2,260,155 | $738 |

At product-series level, GTX generated the highest total revenue at $7,344,767. It also handled the most opportunities at 5,697 and produced the most wins at 2,776, with the strongest series win rate at 63.4%.

The GTX series benefited from having four products and therefore more scale, but product count alone did not explain the result. Its revenue leadership was supported by both strong opportunity volume and an average Won deal value of approximately $2,646.

GTK produced the highest Revenue per Opportunity at approximately $10,015 because of the very high value of GTK 500, but its 40 opportunities limited total revenue.

MG generated lower revenue per opportunity at approximately $738 because the series included MG Special, which converted opportunities effectively but had very low deal value.

The analysis confirmed that series-level results were useful for portfolio analysis, but individual-product analysis still needed to remain visible because products within the same series behaved very differently. GTX alone spans an average deal value range from $546 to $5,490.

<a id="section-25"></a>

## 25. Sector Performance

| Sector | Accts | Assigned Opps | Won | Win Rate | Revenue | Rev/Account | Opps/Acct |
| --- | --- | --- | --- | --- | --- | --- | --- |
| retail | 17 | 1,397 | 799 | 63.1% | $1,867,528 | $109,855 | 82.2 |
| technology | 12 | 1,165 | 671 | 63.4% | $1,515,487 | $126,291 | 97.1 |
| medical | 12 | 1,051 | 592 | 62.3% | $1,359,595 | $113,300 | 87.6 |
| software | 7 | 757 | 450 | 63.9% | $1,077,934 | $153,991 | 108.1 |
| finance | 8 | 678 | 375 | 61.2% | $950,908 | $118,864 | 84.8 |
| marketing | 8 | 674 | 404 | 64.8% | $922,321 | $115,290 | 84.3 |
| entertainment | 6 | 451 | 260 | 64.7% | $689,007 | $114,835 | 75.2 |
| telecommunications | 6 | 501 | 285 | 62.5% | $653,574 | $108,929 | 83.5 |
| services | 5 | 390 | 223 | 63.4% | $533,006 | $106,601 | 78.0 |
| employment | 4 | 311 | 179 | 62.6% | $436,174 | $109,044 | 77.8 |
| Total | 85 | 7,375 | 4,238 | 63.2% | $10,005,534 | $117,712 | 86.8 |

Retail generated the highest total Won Revenue at $1,867,528. It also had the largest account base at 17 accounts, the highest assigned opportunity volume at 1,397 and the highest number of Won opportunities at 799.

This suggested that Retail's revenue leadership was largely scale-driven. Its win rate of 63.1% is close to the overall average and its revenue per account of $109,855 is below the sector mean of $117,712.

Marketing recorded the strongest sector win rate at 64.8%, marginally ahead of Entertainment at 64.7%. Entertainment recorded the highest Average Won Deal Value at approximately $2,650.

When normalised by account count, Software became particularly interesting. Software produced $153,991 Revenue per Account and 108.1 Opportunities per Account. Both were the highest among the sectors, and both were achieved from only 7 accounts, the third-smallest account base.

This suggested that Software accounts generated especially high commercial activity and revenue relative to the number of customers.

However, the dataset did not provide enough evidence to conclude why. Product mix, customer size, account quality, sales-agent assignment and other variables could all contribute.

Sector-level totals included only 7,375 opportunities because the 1,425 unassigned open opportunities did not have a sector. The Won total of 4,238 is unaffected, because no closed opportunity is missing an account.

I decided these unassigned records should remain visible in the dashboard rather than being hidden, so that users can reconcile sector totals against the full pipeline.

<a id="section-26"></a>

## 26. Account Revenue Concentration

I analysed account-level Won Revenue to determine whether the business depended heavily on a few customers. The five largest accounts were:

| Account | Won Revenue |
| --- | --- |
| Kan-code | $341,455 |
| Konex | $269,245 |
| Condax | $206,410 |
| Cheers | $198,020 |
| Hottechi | $194,957 |

Kan-code generated the highest account revenue at $341,455, representing only 3.4% of total Won Revenue.

The concentration profile was:

| Grouping | Share of Won Revenue |
| --- | --- |
| Largest account | 3.4% |
| Top 5 accounts | 12.1% |
| Top 10 accounts | 20.7% |
| Top 20 accounts | 36.0% |
| Remaining 65 accounts | 64.0% |

This indicated relatively diversified customer revenue rather than dependence on a very small number of accounts.

However, I did not interpret diversification as evidence that every customer relationship was equally important.

Strategic value could also depend on future pipeline, repeat purchasing, customer lifetime value, profitability or contract terms, none of which were available.

<a id="section-27"></a>

## 27. Sales-Cycle Analysis

I calculated sales-cycle duration for closed opportunities as `close_date` minus `engage_date`. This produced sales-cycle values for all 6,711 closed opportunities.

The overall average sales cycle was 48.0 days, while the median was 45 days.

Won opportunities had an average of 51.8 days and a median of 57 days. Lost opportunities had an average of 41.5 days and a median of 14 days.

The difference between the Lost average and median was particularly important. It suggested that many Lost opportunities closed relatively quickly, while a smaller number remained active much longer and pulled the average upward.

I therefore used both average and median rather than relying on the average alone, and displayed both on the dashboard side by side.

I did not conclude that longer opportunities were more likely to win. The duration could also reflect product complexity, approvals, contract negotiations, customer size or other factors not included in the data.

<a id="section-28"></a>

## 28. Sales Cycle by Region

Regional overall average cycle times were relatively close: Central at approximately 48.6 days, West at approximately 48.0 days and East at approximately 47.1 days.

The difference between the longest and shortest region was only around 1.5 days. I therefore did not treat regional cycle length as evidence of a major process issue.

East appeared slightly faster, but cycle time alone was insufficient to describe the region as more efficient because performance also depended on volume, revenue, win rate and deal value.

<a id="section-29"></a>

## 29. Sales Cycle by Product

At product level across all regions, the seven products span a narrow range:

| Product | Avg Cycle Days | Closed Opps |
| --- | --- | --- |
| GTK 500 | 53.7 | 25 |
| GTX Basic | 49.9 | 1,436 |
| GTX Plus Basic | 49.4 | 1,051 |
| MG Special | 48.4 | 1,223 |
| MG Advanced | 47.1 | 1,084 |
| GTX Plus Pro | 46.1 | 745 |
| GTX Pro | 45.7 | 1,147 |
| Total | 48.0 | 6,711 |

GTK 500 had the longest overall average sales cycle at 53.7 days, with Won opportunities averaging approximately 64.1 days. GTX Pro had the shortest at 45.7 days.

Excluding GTK 500, the remaining six products span only 45.7 to 49.9 days, a range of 4.2 days. Sales-cycle duration therefore varies far less by product than revenue, conversion or deal value do.

The regional matrix records average cycle days as follows:

| Product | Central | East | West |
| --- | --- | --- | --- |
| GTK 500 | 14.0 | 39.0 | 56.1 |
| GTX Basic | 53.7 | 47.9 | 47.7 |
| GTX Plus Basic | 48.3 | 48.7 | 51.3 |
| GTX Plus Pro | 43.6 | 48.2 | 46.7 |
| GTX Pro | 47.1 | 41.7 | 48.7 |
| MG Advanced | 48.0 | 48.2 | 45.1 |
| MG Special | 47.9 | 50.4 | 48.0 |

The GTK 500 spread of 14.0 days in Central against 56.1 in West is a sample-size artefact, not a process difference. Reconciling those cells against the product average of 53.7 across 25 closed opportunities implies a split of approximately one deal in Central, one in East and 23 in West, meaning two of the three regional cells rest on a single observation each. Any regional reading of GTK 500 should be disregarded.

Every other product sits between roughly 41 and 54 days in every region.

The analysis did not show a consistent relationship between higher product prices and longer sales cycles. GTX Plus Pro, at an average Won deal value of $5,490, closed faster at 46.1 days than GTX Basic at $546 and 49.9 days. Price alone was therefore not enough to explain sales-cycle duration.

<a id="section-30"></a>

## 30. Building the Power BI Analytical Model

After completing the exploratory analysis in Excel, I moved the project into Power BI. I did not simply import the flattened `sales_analysis` table.

Instead, I created an analytical star schema consisting of:

- `fact_sales_pipeline`
- `dim_accounts`
- `dim_products`
- `dim_sales_teams`
- `dim_date`

The fact table retained the transactional sales-opportunity grain. The dimensions contained the descriptive business attributes used to filter and segment the fact table.

The primary relationships were:

- `dim_sales_teams`[`sales_agent`] to `fact_sales_pipeline`[`sales_agent`]
- `dim_products`[product] to `fact_sales_pipeline`[product]
- `dim_accounts`[account] to `fact_sales_pipeline`[account]

Each relationship used a one-to-many structure from dimension to fact. This produced a cleaner model than loading one large flattened table into Power BI.

<a id="section-31"></a>

## 31. Building the Date Dimension

Because the sales data contained two analytically important dates, I created a dedicated `dim_date` table including Date, Year, Quarter, Quarter Number, Year Quarter, Year Quarter Sort, Month, Month Number, Month Year and Month Year Sort.

The sort columns allowed month and quarter labels to appear chronologically rather than alphabetically.

I created two relationships from `dim_date` to the fact table. The active relationship runs from `dim_date`[Date] to `fact_sales_pipeline`[`close_date`]. The inactive relationship runs from `dim_date`[Date] to `fact_sales_pipeline`[`engage_date`].

The active relationship supported normal closed-deal and revenue analysis. The inactive engagement relationship was activated inside specific DAX measures when I needed to analyse cohorts based on when opportunities reached Engaging.

This was one of the more important modelling decisions in the project because it allowed one consistent date dimension to support two different business timelines.

<a id="section-32"></a>

## 32. DAX Measure Development

I created a dedicated `_Measures` table to centralise the analytical measures. The core measures included:

- Total Opportunities, Prospecting Opportunities, Engaging Opportunities, Open Opportunities, Open Pipeline Share
- Won Opportunities, Lost Opportunities, Closed Opportunities, Closed-Deal Win Rate, Closed-Deal Loss Rate
- Won Revenue, Average Won Deal Value, Revenue per Opportunity
- Account Count, Sales Agents, Revenue per Account, Revenue per Agent, Opportunities per Account, Opportunities per Agent

I also created sales-cycle measures including Average Sales Cycle Days, Median Sales Cycle Days, and the Won and Lost variants of each.

For the engagement analysis, I created measures using the inactive engagement-date relationship, including Opportunities Reaching Engaging, Won and Lost Opportunities by Engagement Date, Closed Opportunities by Engagement Date, Won Revenue by Engagement Date and Engagement Cohort Win Rate.

I also created measures for the agent performance leaders and account-concentration indicators.

The measures were validated against the Excel analysis before being used in the final dashboard.

<a id="section-33"></a>

## 33. Power BI QA and Reconciliation

Before designing the final report pages, I created four separate QA pages in Power BI, validating by Region, Product, Account and Sector, and Date. These occupy pages 1 to 4 of the report file and are hidden from end users, so the published report opens on the Executive Overview.

The purpose was to confirm that the DAX measures reconciled to the Excel EDA under different filter contexts. The regional QA confirmed:

| Region | Total Opps | Won | Lost | Revenue | Win Rate |
| --- | --- | --- | --- | --- | --- |
| Central | 3,512 | 1,629 | 975 | $3,346,293 | 62.6% |
| West | 2,997 | 1,438 | 811 | $3,568,647 | 63.9% |
| East | 2,291 | 1,171 | 687 | $3,090,594 | 63.0% |

The date QA confirmed the two date roles operate independently. Close-date measures begin in March 2017, engagement activity begins in October 2016, and Opportunities Reaching Engaging totals 8,300. Both revenue measures reconcile to $10,005,534.

The overall totals reconciled to 8,800 opportunities, 4,238 Won, 2,473 Lost, 6,711 closed, a 63.2% win rate, $10,005,534 Won Revenue and a $2,361 Average Won Deal Value.

A later product-level check also confirmed that the cycle measure aggregates correctly. Weighting each product's average cycle by its closed-deal count returns 47.97 days, which matches the reported overall figure of 48.0. This ruled out an average-of-averages error in the measure.

This gave me confidence that the Power BI model and measures were reproducing the validated Excel analysis correctly.

<a id="section-34"></a>

## 34. Designing the Dashboard Architecture

The initial question was whether the dashboard needed many separate pages or whether related analytical areas could be combined.

I rejected the idea of separate pages for Region, Manager, Agent, Product, Sector and Account. That would have turned the report into a collection of worksheets rather than a coherent BI product.

I eventually settled on four main analytical pages:

- Executive Overview
- Sales Team Performance
- Product & Customer Performance
- Pipeline & Sales Cycle

I later added two separate insight pages: Insights & Recommendations, and Pipeline, Process & Data Limitations.

Including the four hidden QA pages, the file contains ten pages in total.

This structure allowed the analytical pages to remain visual and interactive while the longer interpretations and recommendations were moved into dedicated insight pages.

This decision was partly driven by feedback that the dashboard should not become visually congested. Rather than placing long paragraphs beside every chart, I kept the analytical pages focused on evidence and moved narrative interpretation to the end of the report. A consistent navigation bar sits below the title block on every page.

<a id="section-35"></a>

## 35. Executive Overview

The Executive Overview was designed as the page a Sales Director could understand within approximately 30 seconds. It is set as the report landing page. The headline KPIs were:

| KPI | Value |
| --- | --- |
| Won Revenue | $10,005,534 |
| Closed-Deal Win Rate | 63.2% |
| Open Pipeline | 2,089 and 23.7% |
| Average Won Deal Value | $2,361 |
| Unassigned Open Opportunities | 1,425 |

The page also included Monthly Won Revenue and Closed-Deal Win Rate from March to December 2017, Pipeline Status showing Won 4,238, Lost 2,473, Engaging 1,589 and Prospecting 500, Won Revenue by Region and Won Revenue by Product.

The page allowed filtering by Close Period, Region, Product and Sector.

The subtitle carries the profitability disclaimer so that it is visible before any other number on the report: revenue performance only, profitability cannot be evaluated because cost and margin data are unavailable.

I placed Unassigned Open Opportunities on the Overview deliberately. It is a data-quality and process issue rather than a performance metric, but it is the kind of thing a Sales Director should see immediately rather than discover four pages later.

The purpose of the page was not to explain every detail but to answer how the sales operation is performing and where to look next.

<a id="section-36"></a>

## 36. Sales Team Performance Page

The Sales Team page was designed around the hierarchy Region to Manager to Sales Agent. The headline KPIs included:

| KPI | Value |
| --- | --- |
| Won Revenue | $10,005,534 |
| Closed-Deal Win Rate | 63.2% |
| Revenue per Agent | $285,872 |
| Opportunities per Agent | 251.4 |

The main matrix allowed users to drill from region into manager and then individual agent, showing total opportunities, Won opportunities, win rate, Won revenue and average Won deal for each level.

A scatter plot compared agent workload with Won Revenue, coloured by region, allowing high revenue to be interpreted in the context of opportunity volume. Darcel Schlecht appears as a clear outlier on this chart at roughly 750 opportunities.

A Top Agents chart showed the five highest Won Revenue performers. I also created separate cards for the leaders in Won Revenue, Win Rate, Revenue Efficiency and Average Won Deal Value.

This avoided presenting one single ranking as the definition of the best sales agent.

The page allowed filtering by Close Period, Region, Manager and Sales Agent.

<a id="section-37"></a>

## 37. Product & Customer Performance Page

The Product & Customer page combined the commercial sides of the dataset. The headline KPIs included:

| KPI | Value |
| --- | --- |
| Won Revenue | $10,005,534 |
| Won Opportunities | 4,238 |
| Closed-Deal Win Rate | 63.2% |
| Average Won Deal Value | $2,361 |

The page included a Product Performance matrix, an Average Won Deal Value vs Opportunity Volume scatter sized by revenue and coloured by series, a Sector Performance matrix including Revenue per Account and Opportunities per Account, Top Accounts by Won Revenue, and revenue concentration cards for the largest account, Top 5 and Top 10.

The page allowed filtering by Close Period, Product Series, Product and Sector, and repeats the profitability disclaimer in the subtitle.

This was important because a high-revenue product or account should not automatically be described as the most profitable.

The scatter plot carries most of the analytical weight on this page. It positions MG Special at high volume and near-zero value, GTK 500 at near-zero volume and very high value, and GTX Pro and GTX Plus Pro in the commercially productive middle. The shape of that chart communicates the volume-value trade-off faster than the matrix does.

<a id="section-38"></a>

## 38. Pipeline & Sales-Cycle Page

The Pipeline & Cycle page focused on pipeline health, closing behaviour and timing. The headline KPIs were:

| KPI | Value |
| --- | --- |
| Open Pipeline | 2,089 and 23.7% |
| Average Sales Cycle | 48.0 days |
| Median Sales Cycle | 45 days |
| Closed Opportunities | 6,711 |

The analytical visuals included Monthly Closing Performance showing Won and Lost opportunities with the win-rate line, Pipeline Status, Sales Cycle by Outcome, Engagement Cohorts, and Average Sales Cycle by Product & Region.

The Sales Cycle by Outcome chart deliberately showed both average and median because the Lost distribution was strongly skewed. Displaying 41.5 against 14 in the same visual makes the skew self-evident.

The Engagement Cohorts chart used the inactive engagement-date relationship and showed opportunities reaching Engaging alongside the eventual cohort win rate, running from October 2016 to December 2017.

This allowed the dashboard to distinguish between when a deal was engaged and when it eventually closed.

The page allowed filtering by Close Period, Region, Product and Deal Stage.

<a id="section-39"></a>

## 39. Slicer and Interaction Design

Not every slicer should filter every visual. This became particularly important for the Close Period slicer.

Open opportunities have no `close_date`, so allowing Close Period to filter open-pipeline measures could incorrectly remove those records.

I therefore reviewed and adjusted visual interactions so that date filters behaved according to the business meaning of each visual.

Similarly, the Engagement Cohorts visual needed to use engagement-date logic rather than being constrained by the active close-date context.

This reinforced an important Power BI lesson: a technically functional slicer interaction is not automatically analytically correct.

<a id="section-40"></a>

## 40. Conditional Formatting and Visual Design

I kept the report styling deliberately simple. The visual design used consistent navigation buttons on every page, a white background, dark text, a single blue analytical accent, limited decorative elements, compact KPI cards and consistent page titles and subtitles.

For the Average Sales Cycle by Product & Region matrix, I applied a subtle blue conditional-formatting gradient rather than aggressive red and green status colours.

Longer sales cycles were not automatically labelled bad because the dataset did not provide enough evidence to make that judgement.

The goal of the heatmap was to draw attention to differences, not assign performance labels that the data could not support.

<a id="section-41"></a>

## 41. Final Executive Findings

### Regional and manager performance

West generated the highest regional Won Revenue at $3,568,647, but regional win rates were close: West 63.9%, East 63.0%, Central 62.6%.

Central handled the highest workload at 3,512 opportunities and generated the highest Revenue per Agent at approximately $304,208.

At manager level, different managers led different measures. Melvin Marxen led Won Revenue and Revenue per Agent, Cara Losch led win rate at 64.4% and Rocco Neubert led Average Won Deal Value at $2,837.

The implication was that regional and manager performance should be evaluated across several measures rather than ranked using one KPI.

### Sales-agent performance

Darcel Schlecht led Won Revenue at $1,153,214. Hayden Neloms led win rate at 70.4%. Elease Gluck led Average Won Deal Value at $3,615. Reed Clapper led Revenue per Opportunity at $1,850.

Five of the 35 agents had no assigned opportunities and therefore could not be fairly evaluated as poor performers.

### Product performance

GTX Pro generated the highest total revenue at $3,510,578. GTX Plus Pro generated the second-highest at $2,629,651 from only 968 opportunities.

MG Special achieved the highest product win rate at 64.8% but only $43,768 in revenue because the product value was $55. GTK 500 generated $400,612 from 40 opportunities.

The product story therefore depended on the interaction between volume, conversion and deal value.

### Customer and sector performance

Retail generated the highest total sector revenue at $1,867,528 because it had the largest account base and highest opportunity volume.

Software generated the strongest Revenue per Account at $153,991 and Opportunities per Account at 108.1 from only 7 accounts.

Account revenue was relatively diversified, with the largest account contributing 3.4% of total Won Revenue and the Top 10 contributing 20.7%.

<a id="section-42"></a>

## 42. Pipeline and Process Findings

### Quarter-end closing pattern

The final month of each complete quarter showed materially stronger win rates: approximately 82.8% in June, 79.2% in September and 78.5% in December.

The pattern was consistent enough to warrant investigation, but the available data could not establish the cause.

### Sales-cycle behaviour

Won opportunities took longer on average than Lost opportunities, 51.8 days against 41.5.

Lost opportunities had a median of only 14 days against an average of 41.5, indicating that most losses resolve quickly while a smaller group remains active much longer.

Long-running Lost opportunities may represent inefficient use of sales resources and could benefit from earlier qualification or disengagement.

### Open pipeline

The open pipeline contained 2,089 opportunities, 23.7% of all opportunities, made up of 1,589 Engaging and 500 Prospecting. Of these, 1,425 did not have an assigned account.

This limited the ability to fully understand the open pipeline by customer or sector.

### Engagement cohorts

A total of 8,300 opportunities reached Engaging. July 2017 represented the largest engagement cohort at approximately 1,198 opportunities.

Later engagement cohorts required maturity-aware interpretation because some opportunities could still remain unresolved. A recent cohort with a low apparent win rate has not necessarily performed badly, it may simply be incomplete.

<a id="section-43"></a>

## 43. Analytical Limitations

### Profitability

The dataset did not include reliable product cost, gross margin, discount, commission, promotional cost or cost-to-serve data.

The project therefore analysed revenue performance, not profitability. A high-revenue product, account or sector cannot automatically be described as the most profitable.

### Full sales-cycle measurement

Sales cycle was calculated from `engage_date` to `close_date`. The dataset did not contain an opportunity creation date or stage-transition timestamps.

This means the reported sales cycle measures only the period from engagement to closure, not the complete lead-to-close lifecycle.

### Small sample sizes

Some highly segmented results were based on small populations. GTK 500 had only 40 total opportunities and 25 closed opportunities. Splitting those records further by region produces the volatile 14.0 to 56.1 day spread visible in the cycle matrix, where two of the three regional cells rest on a single observation.

### Root-cause variables

The dataset did not contain structured loss reasons, campaign attribution, competitor information, detailed salesperson activity history, sales targets, customer intent or discount history.

This means the analysis could identify patterns but could not always explain why they occurred.

<a id="section-44"></a>

## 44. Key Analytical Decisions

Several decisions shaped the quality of the final solution.

- I kept the raw CSV files unchanged and cleaned data in Power Query rather than manually editing the source.
- I validated keys and referential integrity before building the analytical model.
- I treated blank open-stage accounts as a business limitation rather than deleting them, and surfaced them on the Overview page.
- I calculated win rate only across Won and Lost opportunities.
- I defined revenue using Won opportunities only.
- I kept Lost zero close values because they correctly represented unsuccessful deals.
- I separated closing-date analysis from engagement-date analysis.
- I used both average and median sales-cycle measures.
- I avoided describing observed patterns as causal conclusions.
- I compared teams, products and sectors using both totals and normalised measures.
- I used a star schema in Power BI rather than relying on the flattened Excel analysis table.
- I separated analytical pages from narrative insight pages, and hid the QA pages from end users.
- I reviewed slicer interactions based on the business meaning of the measure rather than accepting Power BI's default filtering behaviour.

<a id="section-45"></a>

## 45. Challenges During the Project

One of the main challenges was avoiding premature conclusions. Several measures appeared straightforward until I examined the context.

A high win rate did not necessarily mean high revenue. High revenue did not necessarily mean high productivity. High product revenue did not necessarily mean high profitability. A longer sales cycle did not necessarily mean poor performance. Blank account values were not automatically bad data.

The project forced me to repeatedly ask what a metric actually represents, and what additional information I would need before making a conclusion.

Another challenge was balancing analytical completeness with dashboard usability. The first dashboard layouts became too crowded when I tried to include both charts and long explanations on the same pages.

I ultimately moved the narrative interpretation into two separate insight pages. This improved the report because the analytical pages remained interactive and visual, while the insight pages provided the decision context.

<a id="section-46"></a>

## 46. What I Learned

This project strengthened my understanding of the full analytics workflow.

I learned that exploratory analysis is not only about finding interesting patterns. It is also where the analyst establishes grain, keys, business definitions, data-quality rules, limitations and valid comparisons.

I gained practical experience with referential-integrity testing and saw how a minor text mismatch such as `GTXPro` against GTX Pro could create a major modelling problem affecting 1,480 records.

I also gained a stronger understanding of how totals and normalised measures tell different business stories. Retail led total sector revenue at $1,867,528. Software led revenue per account at $153,991. Both findings were correct, but they answered different questions.

The same applied to agent performance. Darcel led total revenue, Hayden led win rate, Elease led deal value and Reed led efficiency. There was no universal best agent because performance depended on the question being asked.

The project also improved my understanding of Power BI modelling. The use of an active close-date relationship and inactive engagement-date relationship allowed me to analyse two different business timelines using one date dimension.

I also became more conscious of visual interaction design. Default slicer behaviour could produce technically valid but analytically misleading results, particularly when close-date filters were applied to opportunities that had not yet closed.

Most importantly, the project reinforced that a strong dashboard is not the one with the most visuals. It is the one that helps the stakeholder understand the business clearly.

<a id="section-47"></a>

## 47. Future Enhancements

If this were developed further as a production analytics solution, I would expand the source data to include:

- Opportunity creation date and stage-transition timestamps
- Structured reasons for Lost opportunities
- Sales targets and agent tenure
- Lead source and campaign attribution
- Customer acquisition cost
- Product cost and gross margin
- Discount history and commission
- Customer retention and customer lifetime value
- Sales activity history and competitor information
- Contract values and renewal potential

These additions would support deeper analysis of full funnel duration, pipeline ageing, forecast accuracy, sales productivity, profitability, marketing effectiveness, customer value and root causes of Won and Lost outcomes.

I would also consider adding formal pipeline-age bands, minimum-volume thresholds on highly segmented visuals, and exception alerts for opportunities that remain open significantly longer than the normal cycle.

<a id="section-48"></a>

## 48. Final Project Reflection

I started the project wanting to understand how sales data could generate useful business intelligence. The final result went beyond that initial goal.

The project required me to move from raw CSV files through data profiling, validation, cleaning, exploratory analysis, Power Query transformation, data modelling, DAX, QA, visual design and business interpretation.

The most useful part of the project was not identifying the highest revenue number. It was understanding why a single metric was rarely enough.

Revenue needed volume and deal-value context. Win rate needed workload context. Sector revenue needed account-count context. Sales-cycle averages needed medians and sample-size context. Dashboard results needed limitations and business definitions.

The completed Power BI solution allows a stakeholder to begin with overall performance and then move into the drivers behind it:

- Overview explains what is happening.
- Sales Team explains who is driving the result.
- Product & Customer explains where commercial value is coming from.
- Pipeline & Cycle explains how opportunities are progressing and closing.
- Insights explains what management should take from the performance analysis.
- Process & Limitations explains where the data requires caution, operational attention or additional information.

The final project demonstrates not only dashboard development, but the complete analytical process behind the dashboard. That was the main outcome I wanted from the project.
