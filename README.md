# FMCG Domain Data Engineering - Databricks Lakehouse

This repository contains an End-to-End Data Engineering pipeline designed for the FMCG (Fast-Moving Consumer Goods) domain. It leverages **Databricks, PySpark, SQL**, and **Delta Lake** to transform raw sales and product data into actionable business insights.

## 📌 Project Overview

In the FMCG industry, data arrives from fragmented sources (ERP, CRM, flat files). This project automates the ingestion and cleaning of product and order data, ensuring high data quality through rigorous validation and incremental updates.

## 🛠️ Architecture

1. **Bronze layer**: Raw data ingestion. Data is stored in its original format but converted to Delta for performance.

2. **Silver layer**:
   - **Data cleaning**: Using Regex to extract product attributes (e.g., extracting variants from names using ```regexp_extract```
   - **Validation**: Ensuring IDs are strictly numeric using ```rlike```
   - **Deduplication**: Using ```MERGE``` operations (Upserts) to maintain a single version of truth.

3. **Gold layer**: (Optional) Aggregated tables for PowerBI/Tableau reporting (e.g., Sales by Category, Product Performance)

## 💡 Key Features

1. **Advanced Data Extraction**

The pipeline uses specialized regular expressions to clean "noisy" product names.
- Variant Extraction: ```F.regexp_extract(F.col("product_name"), r"\((.*?)\)")``` captures details inside parentheses (like "Pack of 10" or "Blue").
- Numeric Validation: Verifies that ```product_id``` contains only digits before processing.

2. **Idempotent Data Loading (Delta Merge)**
   
The project utilizes the ```UPSERT``` pattern to handle daily data updates without creating duplicates.
```
delta_table.alias("target").merge(
    source = df_source.alias("source"),
    condition = "target.product_code = source.product_code"
)
.whenMatchedUpdateAll()
.whenNotMatchedInsertAll()
.execute()
```

3. **Change Data Feed (CDF)**
   
The Silver tables are created with ```delta.enableChangeDataFeed = true```. This allows downstream Gold tables to capture only the modified rows, significantly reducing compute costs.

## 📑 Results

The results are presented in a dashboard made on Databricks, and display key metrics that can be tweaked.

The dashboard is available [![here](https://dbc-3795226f-6fda.cloud.databricks.com/dashboardsv3/01f13e4962151192bf96bca8d37cda1a/published?o=7474648089256138)]
[![View PDF Report](https://github.com/vinhsphabmixay/FMCG_domain_Databricks/blob/ef50244955e17dd2417010d0883400f03fcdd81a/AtliQon%20BI%20360%202026-04-22%2013_57.png?raw=true)](https://github.com/vinhsphabmixay/FMCG_domain_Databricks/blob/c0080bb34c5c1cfa601746f173a61c10c4d5dd15/AtliQon%20BI%20360%202026-04-22%2013_57.pdf)]
