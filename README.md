
# 📊 Azure Databricks End-to-End Lakehouse Project 🚀

This project implements a production-grade ETL pipeline using **Azure Databricks** following the **Medallion Architecture** (Bronze → Silver → Gold). It uses:

- 🔄 **Auto Loader** for streaming & incremental data ingestion  
- 🔥 **Delta Live Tables (DLT)** for declarative transformations  
- 🏢 **Unity Catalog** for governance  
- 🧠 **SCD Type 1** logic for warehouse dimensions  
- 💾 **ADLS Gen2** for layered cloud storage  

---

## 🏗️ Architecture Overview

```plaintext
+---------------------+
|   Source Parquet    |
+---------------------+
          ↓
     Bronze Layer (Raw Ingest via Auto Loader)
          ↓
     Silver Layer (Cleaned DataFrames with schema)
          ↓
     Gold Layer (Fact & Dim tables with SCD logic)
````

---

## 🔹 Bronze Layer — Raw Ingestion

✅ Ingested source data using **Auto Loader**
✅ Parameterized ingestion notebook (`file_name` input widget)
✅ Streaming Delta Tables with checkpointing for **Exactly Once**

📷 **Auto Loader Notebook:**

![Auto Loader Config](screenshots/auto_loader_notebook.png)

---

## 🔸 Silver Layer — Cleaned Data

✅ PySpark transformations
✅ Dropped duplicates, selected columns, schema validation
✅ Tables registered to **Unity Catalog**

📷 **Silver Layer Table in Catalog:**

![Silver Output](screenshots/silver_output_catalog.png)

---

## 🟡 Gold Layer — Business Logic & DWH

✅ Created **FactOrders** and **DimCustomers**
✅ Applied **SCD Type 1** (overwrite on match, no history)
✅ Used `merge()` for UPSERT logic
✅ Created surrogate keys (`monotonically_increasing_id()` + offset)

📷 **SCD Logic using Delta + PySpark:**

![DLT SCD Screenshot](screenshots/dlt_scd.png)

---

## ✅ DLT Table Expectations

Used `@dlt.expect_all_or_drop()` to enforce constraints on streaming tables:

```python
my_rules = {
    "rule1": "product_id IS NOT NULL",
    "rule2": "product_name IS NOT NULL"
}

@dlt.expect_all_or_drop(my_rules)
@dlt.table()
def DimProducts_stage():
    return spark.readStream.table("databricks_catalog.silver.products_silver")
```

---

## 📁 ADLS Gen2 Layered Storage

Used **ABFSS URI** format to access containers:

```python
.option("path", "abfss://gold@datalakemushtaaqe2e.dfs.core.windows.net/FactOrders")
```

Separate containers used for:

* `source/` (raw data)
* `bronze/`, `silver/`, `gold/` (ETL stages)
* `metastore/` (Unity Catalog backing store)

---

## 🧠 Tech Stack

| Tool              | Usage                         |
| ----------------- | ----------------------------- |
| Azure Databricks  | Compute, Notebooks, Jobs      |
| Delta Lake        | Storage Format + ACID         |
| Unity Catalog     | Centralized Schema Governance |
| Azure ADLS Gen2   | Cloud Object Storage          |
| PySpark / SQL     | Transformations               |
| Delta Live Tables | Declarative Streaming & DWH   |
| RocksDB           | Stream State Management       |

---

## 💻 Project Structure

```
AzureLakehouseProject/
│
├── notebooks/
│   ├── bronze_ingestion.ipynb
│   ├── silver_customers.ipynb
│   ├── silver_orders.ipynb
│   ├── gold_dim_customers.ipynb
│   └── gold_fact_orders.ipynb
│
├── screenshots/
│   ├── auto_loader_notebook.png
│   ├── silver_output_catalog.png
│   ├── dlt_scd.png
│
├── README.md
└── .gitignore
```

---

## 🚀 How to Run This Project

1. 🔧 Set up Azure ADLS Gen2 with containers:

   * `source/`, `bronze/`, `silver/`, `gold/`
2. 🔐 Set up **Unity Catalog + External Locations**
3. 📥 Upload source Parquet files to the `source/` container
4. 🔄 Run the **Bronze Auto Loader notebook**
5. 🧹 Run **Silver notebooks** to transform & clean data
6. 🏛️ Run **Gold notebooks** to build fact/dim tables
7. 🔁 Automate via **Databricks Workflows** (parameterized)

---

## 🧩 Extra Notes

* Used **RocksDB** under the hood for structured streaming state store
* Streaming tables and views used inside DLT
* Handles **Initial + Incremental** loading cleanly
* Parameterized ingestion supports looping through datasets using jobs

---


## 👨‍💻 Author

**Mustaaq Ahmed Khan**
Databricks | Azure | F1 Strategy Enthusiast
🔗 [LinkedIn](https://www.linkedin.com/in/mustaaq-ahmed-khan-1b7189245/) | 🧠 Passionate about ETL + Race Data + Real-time Systems

---

⭐ Star this repo if you found it helpful!



