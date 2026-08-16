# Data

## Source

This project uses the **CRM Sales Opportunities** sample dataset published through the **Maven Analytics Data Playground**.

The dataset represents B2B sales-pipeline activity for a **fictitious computer-hardware company** and includes information on sales opportunities, customer accounts, products and sales teams.

Maven Analytics identifies the dataset source as **data.world** and lists the dataset license as **Public Domain**.

Because the source is public-domain sample data and represents a fictitious company, the original CSV files are included in this repository to make the analysis reproducible.

---

## Files

| File | Role | Records |
|---|---|---:|
| `sales_pipeline.csv` | Opportunity-level sales pipeline | 8,800 |
| `accounts.csv` | Customer/account reference table | 85 |
| `sales_teams.csv` | Sales-agent, manager and regional-office reference table | 35 |
| `products.csv` | Product and product-series reference table | 7 |
| `data_dictionary.csv` | Source field definitions | 21 |

---

## Data Usage in This Project

The source CSV files in this folder are intentionally kept **unchanged**.

All cleaning and standardisation used in the analysis was performed in **Power Query** so that:

- the original source remains traceable;
- transformations are reproducible;
- raw and cleaned values can be compared;
- data-quality issues remain auditable.

Two source-quality issues were corrected downstream rather than edited in these files:

1. `GTXPro` in `sales_pipeline.csv` was standardised to `GTX Pro` to match the product reference table.
2. `technolgy` in `accounts.csv` was standardised to `technology`.

The raw CSV files therefore preserve the original values exactly as received.

---

## Important Missing-Value Context

The sales pipeline contains **1,425 blank account values**.

These blanks are not distributed randomly:

- Prospecting: 337
- Engaging: 1,088
- Won: 0
- Lost: 0

All blank accounts belong to open opportunities.

They were retained rather than deleted or imputed because they represent an operational/account-assignment limitation rather than missing closed-deal history.

---

## Reproducibility

The public repository includes:

- these raw CSV files;
- the Excel EDA workbook;
- the final Power BI PBIX;
- Power Query transformation documentation;
- DAX documentation;
- QA documentation;
- dashboard screenshots;
- the full project journal.

This allows the analytical results to be traced from the original source data through the final dashboard.

---

## License Note

The **source dataset** is listed by Maven Analytics as **Public Domain**.

That license applies to the source data. The analytical work, documentation, Power BI report and other project-created files should be treated separately under the repository's project license, if one is added.
