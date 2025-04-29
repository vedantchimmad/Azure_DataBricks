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
## 🧑‍💻 Cloning Delta Lake Tables

Cloning Delta Lake tables allows you to create an **exact replica** of a Delta table's data and metadata. There are two types of clones you can create in Databricks:

1. **Full Clone** (Deep Clone) - Copies both the **data** and **metadata** of the original table.
2. **Shallow Clone** - Copies only the **metadata**, and references the **same data** files as the original table.

---

### ✅ 1. Full Clone (Deep Clone)

A **full clone** creates an exact copy of the **data** and **metadata** of the original table. This involves duplicating all the data files and the schema.

#### 📘 Syntax

```sql
CREATE TABLE new_table
CLONE original_table;
```

#### 🧪 Example

```sql
CREATE TABLE sales_copy
CLONE sales_data;
```

This creates a full deep clone of the `sales_data` table, including both data and metadata.

#### 💡 Key Points:
- **Data copy**: Both data files and metadata are copied.
- **Independent copy**: Changes to the `sales_copy` table do not affect `sales_data`.

---

### ✅ 2. Shallow Clone

A **shallow clone** creates a **new table** that **references** the data files of the original table. This doesn't copy the data but creates a new metadata layer pointing to the same data files.

#### 📘 Syntax

```sql
CREATE TABLE new_table
CLONE original_table SHALLOW;
```

#### 🧪 Example

```sql
CREATE TABLE sales_copy_shallow
CLONE sales_data SHALLOW;
```

This creates a shallow clone of the `sales_data` table, referencing the same data files.

#### 💡 Key Points:
- **No data duplication**: Only the schema is cloned.
- **Faster and more efficient**: No need to copy data.
- **Shared data**: Changes in the data for one table will reflect in the shallow clone since they share the same data files.

---

### ✅ 3. Cloning with Time Travel

You can also create clones of a Delta table at a **specific point in time** using Delta Lake's **time travel** feature.

#### 📘 Syntax

```sql
CREATE TABLE new_table
CLONE original_table AT VERSION <version_number>;
```

Or:

```sql
CREATE TABLE new_table
CLONE original_table TIMESTAMP AS OF '<timestamp>';
```

#### 🧪 Example

```sql
CREATE TABLE sales_copy_at_version
CLONE sales_data AT VERSION 5;
```

This clones the `sales_data` table as it was at **version 5**.

---

### ✅ 4. Benefits of Cloning Delta Tables

| **Feature**                  | **Full Clone**                   | **Shallow Clone**             |
|------------------------------|----------------------------------|-------------------------------|
| **Data Duplication**          | ✅ Yes                            | ❌ No                          |
| **Independent Table**         | ✅ Yes                            | ✅ Yes                         |
| **Performance**               | ❌ Slower                         | ✅ Faster                      |
| **Backup Use Case**           | ✅ Yes                            | ❌ No                          |
| **Storage Efficiency**        | ❌ Less efficient                 | ✅ More efficient              |
| **Point-in-time**             | ✅ Yes (via version/timestamp)    | ✅ Yes (via version/timestamp) |

---

### ✅ 5. Example of Cloning with Time Travel

You can clone a table from a specific **timestamp** or **version** of the data.

#### 🧪 Example

```sql
CREATE TABLE sales_copy_v2
CLONE sales_data TIMESTAMP AS OF '2025-04-01 12:00:00';
```

This creates a clone of the `sales_data` table as it was on **April 1st, 2025, at 12:00 PM**.

---

### 📌 Final Thoughts

- **Full Clone** is suitable when you need a completely independent copy of the data, such as for **backups** or **testing environments**.
- **Shallow Clone** is more efficient and faster, as it does not duplicate the data, but is suitable for **experiments** where you do not want to duplicate data.
- **Versioned Cloning** allows you to capture a snapshot of the table from a specific point in time for **auditing** or **historical analysis**.

---