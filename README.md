# E-Commerce Data Cleaning & Quality Validation

A Python/pandas data-cleaning and validation pipeline that transforms messy e-commerce order data into a more reliable dataset for downstream analytics.

---

## 💼 Business Problem

E-commerce businesses rely on order data for sales reporting, customer analysis, and operational decision-making. However, raw transaction data can contain missing values, inconsistent categories, invalid transactions, duplicate records, and conflicting information across data sources.

The objective of this project was to transform a messy e-commerce order export into a reliable, analytics-ready dataset while preserving visibility of the underlying data-quality issues.

Rather than simply deleting problematic records, the pipeline follows a:

**Detect → Validate → Correct or Flag → Document → Export**

approach.

This ensures that data-quality issues are visible and reviewable instead of being silently removed.

---

## 🗄️ Data

The project uses two related datasets.

| Dataset               | Records | Description                                                                                                                     |
| --------------------- | ------: | ------------------------------------------------------------------------------------------------------------------------------- |
| `messy_orders.csv`    |  10,000 | E-commerce order transactions containing customer, product, pricing, quantity, date, sales channel, and order-total information |
| `customer_master.csv` |   2,925 | Reference customer records containing name, country, city, plan, segment, and signup date                                       |


### Key Data Challenges

The raw order data contained several issues that could affect downstream analysis:

- Missing order dates
- Inconsistent product category names
- Customer information embedded in a single text field
- Customer IDs not found in the customer master
- Orders dated before customer signup
- Negative unit prices
- Invalid quantities
- Order-total discrepancies
- Reused order IDs

---

## 📊 Results

Out of **10,000 orders, 2,163 (21.6%) were flagged for review**.

### Data Quality Findings

| Issue                           | Rows Flagged | % of Orders |
| ------------------------------- | -----------: | ----------: |
| Order before customer signup    |    **1,545** |  **15.45%** |
| Customer ID not found in master |      **525** |   **5.25%** |
| Order-total mismatch            |       **50** |   **0.50%** |
| Reused order ID                 |       **32** |   **0.32%** |
| Missing order date              |       **24** |   **0.24%** |
| Invalid quantity (≤ 0)          |       **20** |   **0.20%** |
| Negative unit price corrected   |       **18** |   **0.18%** |
| Exact duplicate rows            |        **0** |   **0.00%** |


> **Note:** Issue counts are not additive because a single order can contain multiple data-quality issues.

### Key Finding

**1,545 orders (15.4% of all orders) were dated before the customer's recorded signup date.**

Rather than assuming that all these orders were incorrect and deleting them, the pipeline flags them for investigation.

The finding may indicate an upstream data issue, such as historical orders being backfilled or customer signup information being overwritten. The available data does not provide enough evidence to determine the exact cause.

### Other Findings

**525 order records had customer IDs that could not be matched to the customer master**, representing 145 distinct customer IDs.

**50 orders had a mismatch between the recorded order total and the independently calculated value of `unit_price × quantity`.**

The original `order_total` was retained because the dataset does not contain enough information to determine whether the differences were caused by discounts, fees, taxes, or other adjustments.

**32 rows were associated with reused order IDs**, representing 16 distinct IDs. These were flagged rather than automatically deleted because the affected records were not necessarily exact duplicates.

---


### Outputs

| File                          | Records | Description                                        |
| ----------------------------- | ------: | -------------------------------------------------- |
| `cleaned_orders.csv`          |  10,000 | Full cleaned dataset with validation flags         |
| `data_quality_exceptions.csv` |   2,163 | Records containing one or more data-quality issues |

---

##  Limitations

Some data-quality issues could not be conclusively resolved using the available fields.

- Orders before customer signup were flagged because the available data does not establish whether the dates are incorrect or represent historical/backfilled transactions.
- Order-total mismatches were flagged because the dataset does not contain fields for discounts, taxes, fees, or other adjustments.
- Unmatched customer IDs could not be reliably enriched without another customer data source.
- Reused order IDs could not be conclusively classified as duplicate transactions without additional business rules.

These records were therefore flagged for investigation rather than automatically deleted.

---

##  Next Steps

The cleaned dataset can be used as the foundation for further analytics, including:

1. SQL-based sales and customer analysis
2. Exploratory data analysis with Python
3. Power BI dashboard development
4. Automated data-quality monitoring
5. Development of a reusable ETL pipeline
