CRM Sales Opportunity Analysis

An end-to-end sales analytics project built with Excel, Power Query, Power BI and DAX to analyse a CRM sales pipeline, validate data quality, model commercial performance and translate the results into management-facing insights.

The project covers 8,800 sales opportunities, 35 sales agents, 85 accounts and 7 products.

Portfolio focus: data profiling, QA, Power Query transformation, exploratory analysis, star-schema modelling, DAX, dashboard design and business interpretation.

Business Objective

The project was designed to help sales and commercial stakeholders answer questions such as:

How healthy is the sales pipeline?

Which regions, managers and sales agents are driving results?

Which products generate the most revenue, and why?

Which sectors and accounts contribute the most commercial value?

How long do opportunities take to close?

Are there recurring patterns in closing behaviour?

Where does the available data limit decision-making?

The intended users are a Sales Director, sales managers, Sales Operations managers and product/commercial managers.

Headline Results

KPI

Result

Total Opportunities

8,800

Won Opportunities

4,238

Lost Opportunities

2,473

Closed Opportunities

6,711

Closed-Deal Win Rate

63.2%

Open Opportunities

2,089

Open Pipeline Share

23.7%

Won Revenue

$10,005,534

Average Won Deal Value

$2,361

Unassigned Open Opportunities

1,425

Key Findings

1. Quarter-end closing performance is materially stronger

The final month of each complete quarter recorded substantially higher win rates:

June: 82.8%

September: 79.2%

December: 78.5%

Across the complete quarters, third-month performance produced 1,545 wins, an 80.2% win rate and $3.71M in Won Revenue.

The dataset establishes the pattern but does not establish its cause.

2. Regional performance is multidimensional

West generated the highest Won Revenue at $3.57M and the highest regional win rate at 63.9%.

Central handled the highest workload at 3,512 opportunities and generated the highest Revenue per Agent.

East recorded the highest Average Won Deal Value at $2,639.

No region led every performance measure.

3. Different sales agents lead different metrics

Darcel Schlecht: highest Won Revenue at $1,153,214

Hayden Neloms: highest win rate at 70.4%

Elease Gluck: highest Average Won Deal Value at $3,615

Reed Clapper: highest Revenue per Opportunity at approximately $1,850

Darcel also recorded the second-highest Average Won Deal Value at $3,304, sustained across 349 wins, compared with Elease's leading average across 80 wins.

This is why the dashboard avoids one universal "best agent" ranking.

4. Product revenue is a combination of volume, conversion and deal value

GTX Pro: highest Won Revenue at $3.51M

GTX Plus Pro: second-highest Won Revenue at $2.63M from only 968 opportunities

MG Special: highest product win rate at 64.8%, but only $43.8K revenue because Average Won Deal Value was $55

GTK 500: only 40 opportunities, but an Average Won Deal Value of approximately $26.7K

A product cannot be evaluated using revenue, conversion or opportunity volume in isolation.

5. Sector scale and normalized customer value tell different stories

Retail: highest total sector Won Revenue at $1.87M

Software: highest Revenue per Account at $153,991 and Opportunities per Account at 108.1

Retail leads on total scale, while Software produces stronger normalized account economics.

6. Customer revenue is relatively diversified

Largest account: 3.4% of Won Revenue

Top 5 accounts: 12.1%

Top 10 accounts: 20.7%

Top 20 accounts: 36.0%

The remaining 65 accounts generated approximately 64% of total Won Revenue.

7. Won and Lost opportunities have different sales-cycle profiles

Outcome

Average Cycle

Median Cycle

Won

51.8 days

57 days

Lost

41.5 days

14 days

Overall

48.0 days

45 days

The large difference between the Lost mean and median indicates that many losses resolve quickly while a smaller group of long-running losses pulls the average upward.

8. Open-pipeline account assignment is incomplete

There are 2,089 open opportunities, of which 1,425 have no assigned account.

No Won or Lost opportunity is missing an account, so closed customer-revenue analysis is unaffected. However, account- and sector-level analysis of the current open pipeline is incomplete.

Data Quality and Cleaning

The raw source files were preserved unchanged.

Key QA findings included:

opportunity_id confirmed as unique across all 8,800 pipeline records

no duplicate keys in the account, product or sales-team tables

1,480 pipeline rows used GTXPro while the product dimension used GTX Pro

12 account records used technolgy instead of technology

1,425 account blanks were confined to open opportunities

no populated sales-agent, product or account foreign keys remained unmatched after cleaning

no close date occurred before its engagement date

closed opportunities consistently contained close dates

The product and sector issues were corrected reproducibly in Power Query, not by editing the raw CSV files.

Power BI Data Model

The final reporting model uses a star schema:

dim_accounts       ─┐
dim_products       ─┼──> fact_sales_pipeline
dim_sales_teams    ─┘
          │
       dim_date

Main model tables:

fact_sales_pipeline

dim_accounts

dim_products

dim_sales_teams

dim_date

_Measures

Date modelling

dim_date has:

an active relationship to fact_sales_pipeline[close_date]

an inactive relationship to fact_sales_pipeline[engage_date]

Engagement-cohort measures activate the second relationship with DAX when required.

Dashboard Structure

The stakeholder-facing Power BI report contains six pages:

Overview - headline KPIs, monthly performance, pipeline status, regional and product revenue

Sales Team - region/manager/agent hierarchy, workload, productivity and agent leaders

Product & Customer - product, sector, account and revenue-concentration analysis

Pipeline & Cycle - open pipeline, closing behaviour, sales cycle and engagement cohorts

Insights - commercial and sales-team interpretations with recommended actions

Process & Limitations - pipeline/process findings and analytical limitations

Four additional QA pages are retained inside the PBIX and hidden from stakeholder navigation:

QA - Regional

QA - Products

QA - Accounts

QA - Date

The Overview page is configured as the report landing page.

Dashboard Screenshots

Screenshots will be stored in the images/ directory when the repository is packaged.

Executive Overview

images/executive-overview.png

Sales Team Performance

images/sales-team.png

Product & Customer Performance

images/product-customer.png

Pipeline & Sales Cycle

images/pipeline-sales-cycle.png

Insights

images/insights.png

Process & Limitations

images/process-limitations.png

Selected DAX Areas

The model includes measures for:

opportunity and stage counts

closed-deal win and loss rates

open-pipeline share

Won Revenue

Average Won Deal Value

Revenue per Opportunity

Revenue per Agent

Revenue per Account

Opportunities per Agent

Opportunities per Account

average and median sales-cycle duration

engagement cohorts using USERELATIONSHIP

agent performance leaders

largest-account, Top 5 and Top 10 revenue concentration

The final repository will include selected measure definitions in dax/measures.dax.

QA Approach

Power BI measures were reconciled to the Excel EDA before final report design.

The final baseline reconciles to:

8,800 opportunities

4,238 Won

2,473 Lost

6,711 closed

63.2% closed-deal win rate

$10,005,534 Won Revenue

$2,361 Average Won Deal Value

Visual interactions were also tested explicitly.

For example, the Close Period slicer does not control open-pipeline visuals that contain records with blank close_date, preventing open opportunities from disappearing incorrectly under date filtering.

Analytical Limitations

Profitability cannot be evaluated

The dataset does not contain reliable cost, gross margin, discount, commission or cost-to-serve fields.

The project therefore evaluates revenue performance rather than profitability.

Sales cycle starts at engagement

Sales-cycle duration is calculated from:

engage_date -> close_date

An opportunity-creation date is unavailable, so the measure does not represent the full lead-to-close lifecycle.

Some highly segmented results have small samples

GTK 500 has only 25 closed opportunities. Its regional split is approximately:

Central: 1

East: 1

West: 23

The extreme Central and East cycle values should therefore not be interpreted as stable regional process differences.

Root-cause variables are limited

The source does not contain structured loss reasons, campaign attribution, competitor information, detailed activity history, incentive data or customer intent.

The analysis can establish what happened more confidently than why it happened.

Tools Used

Excel - profiling, QA and exploratory analysis

Power Query - repeatable data cleaning and transformation

Power BI - semantic modelling and dashboard development

DAX - KPI, cohort, sales-cycle, productivity and concentration measures

Repository Structure

crm-sales-opportunity-analysis/
│
├── README.md
│
├── docs/
│   ├── project-journal.md
│   ├── data-dictionary.md
│   ├── data-quality-and-qa.md
│   ├── data-model.md
│   ├── dax-measures.md
│   └── insights-and-recommendations.md
│
├── images/
│   ├── executive-overview.png
│   ├── sales-team.png
│   ├── product-customer.png
│   ├── pipeline-sales-cycle.png
│   ├── insights.png
│   ├── process-limitations.png
│   └── data-model.png
│
├── powerbi/
│   └── CRM-Sales-Opportunity-Analysis.pbix
│
├── excel/
│   └── CRM-Sales-EDA.xlsx
│
├── dax/
│   └── measures.dax
│
├── power-query/
│   └── transformations.md
│
└── data/
    └── README.md

What I Learned

The strongest lesson from the project was that a single KPI rarely tells the full business story.

Revenue needs volume and deal-value context.
Win rate needs workload context.
Sector totals need account-count context.
Sales-cycle averages need median and sample-size context.
Dashboard metrics need the correct filter context.

The final solution therefore focuses not only on calculating metrics, but on establishing what those metrics can legitimately support as business evidence.

Full Project Journal

A detailed chronological project journal documents the complete workflow, including:

source-data assessment

QA methodology

data cleaning decisions

Excel EDA

Power BI modelling

DAX development

dashboard architecture

analytical findings

limitations

design decisions

project reflection

See: docs/project-journal.md
