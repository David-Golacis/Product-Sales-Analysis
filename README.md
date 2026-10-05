# Product Sales Analysis

Comparing customer revenue, sales volume, and weekly performance across Email, Call, and Email + Call for a six-week Pens and Printers campaign.

**Originally reported:** August 2024 · **Revised:** October 2026  
**Tools:** Python, pandas, NumPy, Matplotlib, Seaborn, and Jupyter notebooks

The revision uses the original dataset. October 2026 is the revision date, not a new observation period.

[Read the analysis notebook](2%20Notebook/Notebook%201.ipynb) · [Browse figures and tables](3%20Reports/) · [View business baselines](3%20Reports/1%20Tables/Table%2009%20Business%20Baselines.csv)

## Business question

Pens and Printers supplies office products to large organisations. This project examines how customer volume, revenue per customer, and total revenue differ across three sales methods, and what those differences suggest for a subsequent campaign.

The analysis supports campaign planning and performance monitoring. It does not establish which method causes higher spending or delivers the highest profit.

## Key findings

| Sales method | Customers | Observed mean revenue per customer | Recorded revenue total | Total including estimates | Missing revenue |
|---|---:|---:|---:|---:|---:|
| Email | 7,466 | 97.13 | 672,317.83 | 725,575.35 | 7.29% |
| Call | 4,962 | 47.60 | 227,563.49 | 236,396.51 | 3.65% |
| Email + Call | 2,572 | 183.65 | 408,256.69 | 473,862.46 | 13.57% |

Revenue is expressed in the dataset's units; a currency has not been established. Observed means exclude missing revenue. Customer counts include all records, and totals including estimates combine recorded amounts with replacements for missing amounts. Values above are rounded for presentation.

- **Email has the largest customer group and revenue total** on both recorded and estimated bases.
- **Email + Call has the highest observed mean revenue in every week**, although it also has the highest missing-revenue percentage.
- **Increasing average spending does not imply increasing total revenue.** Between weeks 5 and 6, overall estimated revenue falls 33.74% and customer count falls 52.29%, while observed mean revenue rises 38.25%.
- **Missing revenue is material:** 1,074 records, or 7.16%, lack a recorded amount. The estimated campaign total is 1,435,834.33, comprising 1,308,138.01 recorded and 127,696.32 imputed.

### Evidence behind the findings

**Campaign revenue: recorded amounts and estimates**

![Recorded revenue and totals including estimates for each sales method](3%20Reports/0%20Figures/Figure%2006%20Recorded%20and%20Estimated%20Revenue%20Totals%20by%20Sales%20Method.png)

Email has the largest total on both bases. The gap between each pair of bars is the imputed addition; the two bars must not be added together. Totals reflect customer volume as well as spending per customer.

**Revenue completeness**

![Percentage of customer records missing revenue within each sales method](3%20Reports/0%20Figures/Figure%2002%20Missing%20Revenue%20by%20Sales%20Method.png)

Email + Call has the highest missing-revenue percentage, at 13.57%. Its observed distributions exclude those records, and its estimated total depends more heavily on replacing missing amounts. This supports prioritising source-data reconciliation.

**Weekly customer volume**

![Weekly customer counts by sales method](3%20Reports/0%20Figures/Figure%2007%20Weekly%20Customer%20Counts%20by%20Sales%20Method.png)

Customer volume and method mix change during the campaign. Overall customer count falls from 3,721 in week 1 to 1,228 in week 6. These are different customer groups, so the lines do not track the same customers over time.

**Weekly observed spending**

![Weekly mean recorded revenue per customer by sales method](3%20Reports/0%20Figures/Figure%2008%20Weekly%20Mean%20Recorded%20Revenue%20by%20Sales%20Method.png)

Email + Call has the highest observed mean in every week. All three methods show a decrease between weeks 2 and 3, so spending growth is not uninterrupted. These means exclude missing revenue and do not establish a causal method effect.

**Weekly estimated totals**

![Weekly estimated revenue totals for Email, Call, and Email plus Call](3%20Reports/0%20Figures/Figure%2009%20Weekly%20Estimated%20Total%20Revenue%20by%20Sales%20Method.png)

Email leads estimated revenue in weeks 1–4; Email + Call leads in weeks 5–6. Comparing this chart with customer counts and observed means shows why increasing average spending can coincide with falling total revenue. Totals include estimates and have no quantified uncertainty intervals.

## Business recommendations

1. **Run a controlled follow-up comparison** of email-only campaigns and email with targeted call follow-up. Reassess the role of Call-only using comparable customer groups and measured costs.
2. **Monitor revenue and customer volume together**, alongside method-specific spending and revenue completeness. The average weekly estimated revenue of 239,305.72 is a historical reference, not an approved target.
3. **Prioritise source-revenue reconciliation**, particularly for Email + Call. Filling estimates does not recover missing source amounts.
4. **Collect the inputs needed for commercial decisions:** eligible contacted customers, unsuccessful contacts, method allocation, attribution windows, staff time, returns, discounts, and relevant costs. These enable conversion, revenue-per-staff-hour, and contribution measures.

## Data and analytical approach

The [source CSV](0%20Data/product_sales.csv) contains 15,000 customer records and eight fields: campaign week, sales method, customer identifier, quantity sold, revenue, customer tenure, website visits, and state. Customer identifiers are unique; weekly comparisons therefore involve different customer groups. The records do not establish an order-level denominator, so the report uses revenue per customer rather than average order value.

### Data schema

The source CSV has eight columns. The types below describe the corrected working dataset in the notebook; CSV files do not preserve pandas data types.

| Column | Meaning | Working type | Validation and treatment |
|---|---|---|---|
| `week` | Campaign week | `int8` | Required whole number from 1 to 6 |
| `sales_method` | Sales approach associated with the customer | `category` | Required; Email, Call, or Email + Call after spelling corrections |
| `customer_id` | Customer identifier | Text identifier | Required, non-blank, and unique; not an order identifier |
| `nb_sold` | Number of items sold in the customer record | `int8` | Required positive whole number; storage bounds checked before conversion |
| `revenue` | Recorded customer revenue in unspecified currency units | `float64` | Non-negative and finite when present; 1,074 missing values retained |
| `years_as_customer` | Customer tenure in years as of 2024 | Nullable `Int8` | Whole number from 0 to 40 when known; invalid values 47 and 63 set to missing |
| `nb_site_visits` | Website visits associated with the customer record | `int8` | Required non-negative whole number; storage bounds checked before conversion |
| `state` | US state | `category` | Required; validated against the 50 state names |

`estimated_revenue` is a derived `float64` column, not a source field. It preserves every recorded amount and supplies group-based estimates only where `revenue` is missing. All 15,000 customer records are retained. The imported source values remain available in `raw_sales_data`.

### Analytical workflow

The notebook:

1. Preserves the imported data and validates the schema, required values, identifiers, numeric bounds, and categories.
2. Standardises two sales-method spelling variants and replaces impossible tenures of 47 and 63 with missing values, retaining both records. The tenure reference year remains 2024, with a maximum of 40 years based on records beginning in 1984.
3. Preserves missing values in `revenue` and creates `estimated_revenue`, using recorded group means by sales method, quantity sold, and week. Broader fallback groups are available when exact groups lack support.
4. Evaluates predictions using a reproducible holdout of approximately 20% of recorded revenues within each method. Errors are also weighted by each method's share of missing records.
5. Calculates observed distributions, estimated totals, weekly changes, and historical business baselines, with reconciliation checks before reporting.

The missing-record-weighted mean absolute error is **1.643** and root mean squared error is **2.113** revenue units. All 1,074 final estimates use the exact three-variable grouping; one estimate has only three supporting recorded revenues.

## Limitations

- Estimation assumes similar mean revenue for missing and recorded cases within the selected groups. Holdout performance cannot establish that assumption or quantify uncertainty in campaign totals.
- Unequal missingness and changing customer composition affect comparisons. Higher observed spending does not demonstrate a causal sales-method effect.
- Contact denominators, allocation evidence, measured staff time, and costs are unavailable. Conversion superiority, efficiency, profit, and shipment savings cannot be established.
- Findings describe one six-week campaign. Historical baselines require context before being used for future targets.

## Repository guide

```text
Product-Sales-Analysis/
├── README.md
├── environment.yml
├── 0 Data/
│   └── product_sales.csv
├── 1 References/
│   └── Supplementary Sales Pivots.xlsx
├── 2 Notebook/
│   └── Notebook 1.ipynb
└── 3 Reports/
    ├── 0 Figures/
    │   └── Figure 01 ... Figure 10 ... .png
    └── 1 Tables/
        └── Table 01 ... Table 09 ... .csv
```

The Python notebook is the reproducible analytical report. The [Excel workbook](1%20References/Supplementary%20Sales%20Pivots.xlsx) is supplementary and is not required to execute the notebook. Generated outputs are recreated by the notebook; amend their generating code rather than editing exported results manually.

### Figure guide

Five figures are embedded above to explain the main business findings. All ten remain part of the report and are available below; supporting figures are not discarded analyses.

| Figure | Purpose | Presentation |
|---|---|---|
| [01: Customer counts](3%20Reports/0%20Figures/Figure%2001%20Number%20of%20Customers%20per%20Sales%20Method.png) | Shows campaign sample sizes and customer shares; the headline table already summarises the counts. | Linked; notebook |
| [02: Missing revenue](3%20Reports/0%20Figures/Figure%2002%20Missing%20Revenue%20by%20Sales%20Method.png) | Shows unequal completeness and where reconciliation is most needed. | Embedded; notebook |
| [03: Revenue histograms](3%20Reports/0%20Figures/Figure%2003%20Distribution%20of%20Recorded%20Customer%20Revenue%20by%20Sales%20Method.png) | Examines distribution shape using comparable bins and percentages. | Linked; notebook |
| [04: Revenue boxplots](3%20Reports/0%20Figures/Figure%2004%20Distribution%20of%20Recorded%20Customer%20Revenue%20by%20Sales%20Method.png) | Compares medians, means, spread, and potential outliers; complements the histograms. | Linked; notebook |
| [05: Revenue against quantity](3%20Reports/0%20Figures/Figure%2005%20Recorded%20Revenue%20Against%20Quantity%20Sold.png) | Examines an association supporting the estimation variables; does not establish a pricing mechanism. | Linked; notebook |
| [06: Recorded and estimated totals](3%20Reports/0%20Figures/Figure%2006%20Recorded%20and%20Estimated%20Revenue%20Totals%20by%20Sales%20Method.png) | Compares campaign totals and the size of imputed additions. | Embedded; notebook |
| [07: Weekly customer counts](3%20Reports/0%20Figures/Figure%2007%20Weekly%20Customer%20Counts%20by%20Sales%20Method.png) | Provides volume and method-mix context for revenue trends. | Embedded; notebook |
| [08: Weekly observed mean revenue](3%20Reports/0%20Figures/Figure%2008%20Weekly%20Mean%20Recorded%20Revenue%20by%20Sales%20Method.png) | Shows spending among customers with recorded revenue. | Embedded; notebook |
| [09: Weekly estimated totals](3%20Reports/0%20Figures/Figure%2009%20Weekly%20Estimated%20Total%20Revenue%20by%20Sales%20Method.png) | Shows revenue performance by campaign stage, including estimates. | Embedded; notebook |
| [10: Week-over-week revenue growth](3%20Reports/0%20Figures/Figure%2010%20Week-over-Week%20Revenue%20Growth%20by%20Sales%20Method.png) | Quantifies relative changes in observed means and estimated totals; supports the level charts. | Linked; notebook |

### Exported tables

| Output | Purpose |
|---|---|
| [Table 01: Environment versions](3%20Reports/1%20Tables/Table%2001%20Environment%20Versions.csv) | Records the Python and principal package versions used |
| [Tables 02–03: Prediction validation](3%20Reports/1%20Tables/Table%2002%20Validation%20by%20Method.csv) | Method-level errors and [overall validation scores](3%20Reports/1%20Tables/Table%2003%20Validation%20Scores.csv) |
| [Table 04: Group-level estimation audit](3%20Reports/1%20Tables/Table%2004%20Group-Level%20Estimation%20Audit.csv) | Supporting observations, missing counts, and imputed amounts |
| [Table 05: Method summary](3%20Reports/1%20Tables/Table%2005%20Average%20Revenue%20and%20Customer%20Statistics.csv) | Customer counts, observed statistics, and revenue totals |
| [Tables 06–07: Weekly method results](3%20Reports/1%20Tables/Table%2006%20Weekly%20Sales%20Summary.csv) | Weekly statistics and [week 1–6 changes](3%20Reports/1%20Tables/Table%2007%20Week%201%E2%80%936%20Changes%20by%20Sales%20Method.csv) |
| [Table 08: Weekly business metrics](3%20Reports/1%20Tables/Table%2008%20Weekly%20Business%20Metrics.csv) | Overall revenue, volume, spending, completeness, and growth |
| [Table 09: Business baselines](3%20Reports/1%20Tables/Table%2009%20Business%20Baselines.csv) | 44 historical references, with their basis, period, and unit |

**Export conventions:** columns ending in `_fraction` contain fractions: `0.10` means 10%. Percentage columns contain percentage values: `10.0` means 10%. Business-baseline values use their accompanying `unit`. Undefined statistics and growth rates remain blank in CSV exports; they are not zero. Exports retain calculation precision.

## Reproduce the analysis

The [Conda environment specification](environment.yml) records the dependencies needed to run the notebook. The project author successfully recreated it as `product-sales-check` and ran the notebook on Windows in October 2026. Compatibility with macOS and Linux has not been tested.

1. Install Miniconda or Anaconda if Conda is not already available, then clone or download this repository.
2. Open Anaconda Prompt or a terminal with Conda available. Navigate to your local repository folder, which contains `environment.yml`.
3. Create and activate the environment:

   ```shell
   conda env create --file environment.yml
   conda activate product-sales
   ```

   If an environment named `product-sales` already exists and you want a separate installation, use a different name:

   ```shell
   conda env create --name product-sales-check --file environment.yml
   conda activate product-sales-check
   ```

4. Open [the notebook](2%20Notebook/Notebook%201.ipynb) in VS Code with notebook support, or in Jupyter. Select the newly created environment as the notebook kernel; activating a terminal environment alone does not change an already selected kernel.
5. In the first code cell, change `project_root` to the absolute path of your local repository. Ensure both `3 Reports/0 Figures` and `3 Reports/1 Tables` exist.
6. Restart the kernel and run every cell from top to bottom. Execution overwrites the generated figures and tables. Review any validation errors before using the results, then save the executed notebook.

The notebook's **Software environment** section records the Python and principal package versions actually used during execution and exports them to [Table 01](3%20Reports/1%20Tables/Table%2001%20Environment%20Versions.csv). Keep this execution record alongside `environment.yml`: the YAML specifies installation dependencies, while the table documents the environment that produced the results.

## Revision history and contact

**August 2024:** original report.  
**October 2026:** revised validation, tenure handling, missing-revenue estimation, prediction checks, figures, weekly growth calculations, and business recommendations. Revised results supersede the original reported estimates and targets.

Maintained by [David Golacis](https://github.com/David-Golacis). To report a reproducibility issue, [open an issue](https://github.com/David-Golacis/Product-Sales-Analysis/issues) with the affected cell, error message, and environment versions.
