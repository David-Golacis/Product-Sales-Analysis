# Product Sales Analysis

Comparing customer revenue, sales volume, and weekly performance across Email, Call, and Email + Call for a six-week Pens and Printers campaign.

**Originally reported:** August 2024 · **Revised:** October 2026  
**Tools:** Python, pandas, NumPy, Matplotlib, Seaborn, and Jupyter notebooks

The revision uses the original dataset. October 2026 is the revision date, not a new observation period.

[Read the analysis notebook](1%20Notebook/notebook.ipynb) · [Browse figures and tables](2%20Outputs/) · [View business baselines](2%20Outputs/Table%2009%20Business%20Baselines.csv)

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

![Recorded revenue and totals including estimates for each sales method](2%20Outputs/Figure%2006%20Recorded%20and%20Estimated%20Revenue%20Totals%20by%20Sales%20Method.png)

The gap between each pair of bars is the imputed addition. The two bars must not be added together.

![Weekly estimated revenue totals for Email, Call, and Email plus Call](2%20Outputs/Figure%2009%20Weekly%20Estimated%20Total%20Revenue%20by%20Sales%20Method.png)

Email leads estimated revenue in weeks 1–4; Email + Call leads in weeks 5–6. Changing customer volume and method mix limit explanations of these trends.

## Business recommendations

1. **Run a controlled follow-up comparison** of email-only campaigns and email with targeted call follow-up. Reassess the role of Call-only using comparable customer groups and measured costs.
2. **Monitor revenue and customer volume together**, alongside method-specific spending and revenue completeness. The average weekly estimated revenue of 239,305.72 is a historical reference, not an approved target.
3. **Prioritise source-revenue reconciliation**, particularly for Email + Call. Filling estimates does not recover missing source amounts.
4. **Collect the inputs needed for commercial decisions:** eligible contacted customers, unsuccessful contacts, method allocation, attribution windows, staff time, returns, discounts, and relevant costs. These enable conversion, revenue-per-staff-hour, and contribution measures.

## Data and analytical approach

The [source CSV](0%20Data/product_sales.csv) contains 15,000 customer records and eight fields: campaign week, sales method, customer identifier, quantity sold, revenue, customer tenure, website visits, and state. Customer identifiers are unique; weekly comparisons therefore involve different customer groups. The records do not establish an order-level denominator, so the report uses revenue per customer rather than average order value.

The notebook:

1. Preserves the imported data and validates the schema, required values, identifiers, numeric bounds, and categories.
2. Standardises two sales-method spelling variants and replaces impossible tenures of 47 and 63 with missing values, retaining both records. The tenure reference year remains 2024, with a maximum of 40 years based on records beginning in 1984.
3. Preserves missing values in `revenue` and creates `estimated_revenue`, using recorded group means by sales method, quantity sold, and week. Broader fallback groups are available when exact groups lack support.
4. Evaluates predictions using a reproducible holdout of approximately 20% of recorded revenues within each method. Errors are also weighted by each method's share of missing records.
5. Calculates observed distributions, estimated totals, weekly changes, and historical business baselines, with reconciliation checks before reporting.

The missing-record-weighted mean absolute error is **1.643** and root mean squared error is **2.113** revenue units. All 1,074 final estimates use the exact three-variable grouping; one estimate has only three supporting recorded revenues.

The original dataset provider and redistribution terms are not documented in the current project materials. The CSV is the analysis input; this repository should not be treated as evidence of its original provenance or reuse permissions.

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
│   ├── product_sales.csv
│   └── Product Sales Pivots.xlsx
├── 1 Notebook/
│   └── notebook.ipynb
└── 2 Outputs/
    ├── Figure 01 ... Figure 10 ... .png
    └── Table 01 ... Table 09 ... .csv
```

The Python notebook is the reproducible analytical report. The Excel workbook is supplementary and is not required to execute the notebook. Generated outputs are recreated by the notebook; amend their generating code rather than editing exported results manually.

| Output | Purpose |
|---|---|
| [Table 01: Environment versions](2%20Outputs/Table%2001%20Environment%20Versions.csv) | Records the Python and principal package versions used |
| [Tables 02–03: Prediction validation](2%20Outputs/Table%2002%20Validation%20by%20Method.csv) | Method-level errors and [overall validation scores](2%20Outputs/Table%2003%20Validation%20Scores.csv) |
| [Table 04: Group-level estimation audit](2%20Outputs/Table%2004%20Group-Level%20Estimation%20Audit.csv) | Supporting observations, missing counts, and imputed amounts |
| [Table 05: Method summary](2%20Outputs/Table%2005%20Average%20Revenue%20and%20Customer%20Statistics.csv) | Customer counts, observed statistics, and revenue totals |
| [Tables 06–07: Weekly method results](2%20Outputs/Table%2006%20Weekly%20Sales%20Summary.csv) | Weekly statistics and [week 1–6 changes](2%20Outputs/Table%2007%20Week%201%E2%80%936%20Changes%20by%20Sales%20Method.csv) |
| [Table 08: Weekly business metrics](2%20Outputs/Table%2008%20Weekly%20Business%20Metrics.csv) | Overall revenue, volume, spending, completeness, and growth |
| [Table 09: Business baselines](2%20Outputs/Table%2009%20Business%20Baselines.csv) | 44 historical references, with their basis, period, and unit |

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

4. Open [the notebook](1%20Notebook/notebook.ipynb) in VS Code with notebook support, or in Jupyter. Select the newly created environment as the notebook kernel; activating a terminal environment alone does not change an already selected kernel.
5. In the first code cell, change `project_root` to the absolute path of your local repository. Ensure the `2 Outputs` directory exists.
6. Restart the kernel and run every cell from top to bottom. Execution overwrites the generated figures and tables. Review any validation errors before using the results, then save the executed notebook.

The notebook's **Software environment** section records the Python and principal package versions actually used during execution and exports them to [Table 01](2%20Outputs/Table%2001%20Environment%20Versions.csv). Keep this execution record alongside `environment.yml`: the YAML specifies installation dependencies, while the table documents the environment that produced the results.

## Revision history and contact

**August 2024:** original report.  
**October 2026:** revised validation, tenure handling, missing-revenue estimation, prediction checks, figures, weekly growth calculations, and business recommendations. Revised results supersede the original reported estimates and targets.

Maintained by [David Golacis](https://github.com/David-Golacis). To report a reproducibility issue, [open an issue](https://github.com/David-Golacis/Product-Sales-Analysis/issues) with the affected cell, error message, and environment versions.
