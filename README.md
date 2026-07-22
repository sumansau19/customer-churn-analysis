# Customer Churn and Revenue Risk Analysis

![Python](https://img.shields.io/badge/Python-3.x-blue)
![SQLite](https://img.shields.io/badge/Database-SQLite-lightgrey)
![pandas](https://img.shields.io/badge/Library-pandas-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-success)

## Project Overview

This project analyses customer churn for a synthetic OTT subscription business. It combines customer, subscription and support data from a relational SQLite database to identify the customer segments most associated with churn and to quantify the resulting revenue risk.

The analysis focuses on subscription plans, contract types, acquisition sources, complaints, escalations, CSAT, cancellation reasons, customer tenure, monthly charges, CLTV and demographic factors.

## Business Objective

The objective is to answer the following questions:

- What is the overall customer churn rate?
- Which plans and contract types experience the highest churn?
- Which plan–contract combination represents the highest-risk segment?
- How are complaints, escalations and CSAT associated with churn?
- What are the most common cancellation reasons?
- How much monthly and annualised revenue is associated with churned customers?
- How do tenure, monthly charges and CLTV differ between active and churned customers?
- Which customer groups should be prioritised for retention?

## Churn Definition

A customer is classified as **churned** when `cancellation_date` is available. Otherwise, the customer is classified as **active**.

```python
df_db_subscription["churn_flag"] = np.where(
    df_db_subscription["cancellation_date"].notna(),
    1,
    0
)
```

## Dataset

The project uses a synthetic dataset containing 100 customers across three SQLite tables.

| Table | Description | Relationship |
|---|---|---|
| `db_customer` | Customer profile and demographic information | One row per customer |
| `db_subscription` | Subscription, plan, contract, cancellation and revenue information | One row per customer |
| `db_support` | Complaint, escalation, CSAT and support comments | Multiple records may exist per customer |

### Main Columns

- Customer information: `customerid`, `country`, `state`, `gender`, `dob`
- Subscription information: `subscription_start_date`, `subscription_type`, `plan_type`, `contract_type`
- Churn information: `cancellation_date`, `cancellation_reason`, `churn_score`
- Revenue information: `monthly_charges`, `cltv`
- Support information: `complaint_date`, `escalations`, `csat_score`, `comment`

## Tools and Libraries

- SQLite
- SQL
- Python
- pandas
- NumPy
- Matplotlib
- Seaborn
- Jupyter Notebook

## Project Workflow

### 1. Data Extraction

- Connected to the SQLite database.
- Identified all available database tables.
- Loaded each table into a separate pandas DataFrame.

### 2. Initial Data Quality Assessment

- Checked table dimensions.
- Checked duplicate rows and duplicate customer IDs.
- Reviewed data types.
- Reviewed missing values.
- Confirmed that duplicate customer IDs in the support table represented multiple complaint events rather than duplicate customer records.

### 3. Data Cleaning

- Converted date columns to datetime format.
- Standardised inconsistent subscription-source values.
- Removed unused columns.
- Cleaned customer names and categorical values.
- Calculated total complaint count per customer.
- Retained the latest support record for each customer.
- Preserved one row per customer before integration.

### 4. Data Integration

The customer, subscription and consolidated support tables were merged using `customerid`.

The final analytical dataset contains:

- 100 rows
- 22 columns
- One row per customer
- No duplicate customer IDs

### 5. Feature Engineering

The following analytical fields were created:

- `churn_flag`
- `has_complaint`
- `complaint_count`
- `subscription_end_date`
- `tenure_days`
- `tenure_month`
- `Customer Category`
- `csat_category`
- `age`
- `age_group`
- `cancellation_month`

## Key Findings

### Overall Churn

- Total customers: **100**
- Churned customers: **37**
- Active customers: **63**
- Overall churn rate: **37%**
- Retention rate: **63%**

### Plan and Contract Risk

- The **Standard plan** had the highest plan-level churn rate at **48.78%**.
- Monthly-contract customers churned at **50.00%**.
- Annual-contract customers churned at **27.59%**.
- The highest-risk segment was **Standard plan customers on monthly contracts**, with a churn rate of approximately **61.1%**.

### Acquisition Source

- Referral customers had the highest churn rate at **44.44%**.
- Paid customers had a churn rate of **41.18%**.
- Organic customers had the lowest churn rate at **28.21%**.

### Complaints and Support Experience

- Customers with a recorded complaint had a churn rate of **77.3%**.
- Customers without a recorded complaint had a churn rate of **5.4%**.
- Customers whose latest recorded complaint was escalated had a churn rate of **95.0%**.
- Escalated customers had a lower average CSAT score than non-escalated customers.
- Low- and medium-CSAT support customers showed substantially higher churn than high-CSAT customers.

These results show a strong association between poor support experience and churn. They do not by themselves prove that complaints or escalations directly caused churn.

### Cancellation Reasons

- **Switched to competitor** was the most common cancellation reason, reported by **8 customers**.
- It also represented the highest monthly revenue loss by cancellation reason at **119.92**.
- Payment problems and forgotten trial cancellations affected **6 customers each**, suggesting that some churn may be preventable through better billing support and subscription reminders.

### Revenue Impact

- Total monthly value represented in the dataset: **2,069.00**
- Monthly revenue associated with churned customers: **579.63**
- Share of total monthly value associated with churn: **28.01%**
- Estimated annualised revenue loss: **6,955.56**

Annualised revenue loss assumes that the monthly loss continues for 12 months.

### Customer Tenure

- Active-customer average tenure: **67.17 months**
- Churned-customer average tenure: **32.51 months**
- Active-customer median tenure: **67 months**
- Churned-customer median tenure: **37 months**

Churned customers had substantially shorter tenure than active customers, suggesting that churn was more concentrated among relatively newer customers.

### Monthly Charges and ARPU

- Active-customer average monthly charge: **23.64**
- Churned-customer average monthly charge: **15.67**
- Active-customer ARPU: **23.64**

The dataset does not support the assumption that customers with higher monthly charges were more likely to churn.

### Customer Lifetime Value

| Customer Category | Customers | Average CLTV | Total CLTV |
|---|---:|---:|---:|
| Active Customer | 63 | 1,032.62 | 65,055 |
| Churned Customer | 37 | 614.70 | 22,744 |

Active customers had a considerably higher average CLTV than churned customers, showing the long-term value of improving customer retention.

## Business Recommendations

1. Prioritise **Standard monthly-plan customers** for retention campaigns.
2. Encourage suitable monthly customers to move to annual contracts through targeted offers.
3. Introduce an urgent service-recovery process for escalated complaints.
4. Proactively follow up with customers reporting low or medium CSAT.
5. Investigate why customers are switching to competitors, including content, pricing and service-quality factors.
6. Improve payment-failure handling and send reminders before free trials or promotional subscriptions expire.
7. Evaluate Referral and Paid acquisition channels based on retention quality, not only acquisition volume.
8. Treat geographic findings as directional because several states contain very small customer samples.

## Repository Structure

```text
customer-churn-analysis/
│
├── README.md
├── requirements.txt
│
├── data/
│   ├── customer_churn_100_rows.db
│   └── customer_churn_100_rows_final.csv
│
├── notebooks/
│   └── Customer_Churn_Analysis_Final.ipynb
│
├── images/
│   ├── plan_churn.png
│   ├── contract_churn.png
│   ├── plan_contract_churn.png
│   ├── acquisition_churn.png
│   ├── complaint_churn.png
│   ├── escalation_churn.png
│   ├── csat_score_churn.png
│   ├── cancellation_reason.png
│   ├── tenure_distribution_churn.png
│   └── monthly_charges_active_churn_customer.png
│
└── reports/
    └── Customer_Churn_Analysis_Report.pdf
```

## Suggested `requirements.txt`

```text
pandas
numpy
matplotlib
seaborn
jupyter
```

`sqlite3` and `pathlib` are included with Python and normally do not need to be installed separately.

## Project Limitations

- The dataset is synthetic and contains only 100 customers.
- Several demographic and geographic groups have small sample sizes.
- The analysis identifies associations and does not prove causation.
- CSAT and escalation data are available only for customers with support records.
- For customers with multiple support records, the latest complaint details were retained while total complaint count was preserved.
- Annualised revenue loss assumes the monthly loss continues for 12 months.
- The churn score was supplied with the dataset and was not developed as part of this project.
- A proper month-over-month churn rate would require the number of active customers at the beginning of each month.

## Future Enhancements

- Build an interactive Power BI or Tableau dashboard.
- Add cohort-based and month-over-month churn analysis.
- Develop a churn-prediction model using train/test separation.
- Perform survival analysis to estimate time to churn.
- Add customer-level risk segmentation.
- Analyse complaint-resolution time and repeat complaints.
- Evaluate retention-campaign impact and expected revenue recovery.

## Author

**Suman Sau**

Data Analytics Portfolio Project
