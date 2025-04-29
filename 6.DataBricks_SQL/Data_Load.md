## 📥 Data Loading with SQL in Databricks

---

### 🔹 1. Load Data Using `COPY INTO`

> 🧠 The recommended way to load data into **Delta tables** from external files like CSV, JSON, or Parquet.

#### ✅ Syntax:

```sql
COPY INTO target_table
FROM 'path/to/file_or_dir'
FILEFORMAT = 'csv' -- or 'json', 'parquet'
FORMAT_OPTIONS (
  'header' = 'true',
  'inferSchema' = 'true'
);
```

#### 💡 Example:

```sql
COPY INTO default.customers
FROM 'abfss://raw@storageaccount.dfs.core.windows.net/customers/'
FILEFORMAT = 'csv'
FORMAT_OPTIONS (
  'header' = 'true'
);
```

---

### 🔹 2. Load and Create Table Using `CREATE TABLE AS SELECT (CTAS)`

> 📦 Create a new table by selecting data from files or other tables.

### ✅ Example:

```sql
CREATE OR REPLACE TABLE default.transactions
AS
SELECT * FROM read_parquet('dbfs:/mnt/raw/transactions/');
```

---

### 🔹 3. Direct File Query Without Loading (Ad Hoc)

> ⚡ Use when you just need to preview or analyze raw files temporarily.

### ✅ Example:

```sql
SELECT * FROM read_csv('/mnt/data/products.csv')
OPTIONS ('header' = 'true', 'inferSchema' = 'true');
```

---

### 🔹 4. Load Data Into Managed Table from External Table

```sql
CREATE OR REPLACE TABLE curated_orders
AS
SELECT * FROM raw_schema.orders_external
WHERE order_status = 'delivered';
```

---

### 🔹 5. Append Data

```sql
INSERT INTO default.customers
SELECT * FROM read_json('/mnt/incoming/new_customers.json');
```

---

### 🔍 Check Table Content

```sql
SELECT * FROM default.customers;
```

---

### 🧼 Clean Up Table (Optional)

```sql
DROP TABLE IF EXISTS default.customers;
```

---

### 📌 Notes

- `COPY INTO` supports **incremental loads** (skips already loaded files).
- Use **Unity Catalog paths** (`abfss://`, `s3://`, `gs://`) for cloud storage.
- Avoid using `overwrite` mode without understanding implications — it can drop data.

---
### ✅ Features of SQL-based Data Loading in Databricks

| Feature                        | COPY INTO | CTAS (Create Table As Select) | read_files / read_csv |
|-------------------------------|-----------|-------------------------------|------------------------|
| Incremental file loading      | ✅        | ❌                            | ❌                     |
| Supports file-based ingestion | ✅        | ✅                            | ✅                     |
| Automatic schema inference    | ✅        | ✅                            | ✅                     |
| One-time data load            | ✅        | ✅                            | ✅                     |
| Preview data without table    | ❌        | ❌                            | ✅                     |
| Supports overwrite            | ✅        | ✅                            | ❌                     |
| Efficient for pipelines       | ✅        | ✅                            | ⚠️ (for preview only)  |

---
