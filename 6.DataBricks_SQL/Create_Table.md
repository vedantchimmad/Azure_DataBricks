## 🧱 Creating Tables in Databricks (SQL)

---

### 🧩 What Is a Table?

A **table** in Databricks is a structured data object managed through the **metastore**, which allows you to **store**, **query**, and **manage** data with SQL.

There are two types:
- ✅ **Managed Table** – Data and metadata managed by Databricks
- 🌐 **External Table** – Metadata in metastore, data stored externally

---

### 🛠️ Syntax: Basic Table Creation

#### 🧪 1. Managed Table (Default)

```sql
CREATE TABLE employees (
  id INT,
  name STRING,
  role STRING,
  salary FLOAT
);
```

- Data stored in the default managed location (`dbfs:/user/hive/warehouse/`).
- Dropping the table deletes the data.

---

#### 🌐 2. External Table

```sql
CREATE TABLE external_sales (
  sale_id STRING,
  amount FLOAT,
  region STRING
)
USING PARQUET
LOCATION '/mnt/data/sales/';
```

- Table metadata stored in metastore.
- Data stored in the external path (`/mnt/data/sales/`).
- Dropping the table does **not** delete the files.

---

#### 💎 3. Delta Table

```sql
CREATE TABLE transactions (
  txn_id STRING,
  amount DOUBLE,
  status STRING
)
USING DELTA;
```

Delta tables support:
- ACID transactions
- Time travel
- Schema evolution

---

#### 🪵 4. Table from File (One-liner)

```sql
CREATE TABLE flights
USING CSV
OPTIONS (
  path "/mnt/data/flights.csv",
  header "true",
  inferSchema "true"
);
```

---

#### 🛠️ 5. Create Table As Select (CTAS)

```sql
CREATE TABLE high_earners
USING DELTA
AS
SELECT * FROM employees WHERE salary > 100000;
```

---

### 🧠 Tips

| Tip         | Description                                                  |
|-------------|--------------------------------------------------------------|
| `USING`     | Specifies file format (DELTA, PARQUET, CSV, etc.)            |
| `LOCATION`  | Used for **external tables**                                 |
| `OPTIONS`   | Add extra file-specific options (headers, schema, delimiter) |
| `AS SELECT` | Use to populate table from a query (CTAS)                    |

---
### 🔍 Check Your Tables

```sql
-- Show all tables in current schema
SHOW TABLES;

-- View table schema
DESCRIBE TABLE transactions;
```

---
## 📘 Complete SQL Syntax Guide for Table Creation in Databricks

---

### ✅ 1. `CREATE TABLE`

Creates a new table. Fails if the table already exists.

```sql
CREATE TABLE table_name (
  column1 TYPE,
  column2 TYPE
);
```

---

### ❗ 2. `CREATE TABLE IF NOT EXISTS`

Creates a table only if it doesn't already exist.

```sql
CREATE TABLE IF NOT EXISTS table_name (
  column1 TYPE,
  column2 TYPE
);
```

> 🔐 Safe for pipelines to avoid accidental failures if table already exists.

---

### 🔁 3. `CREATE OR REPLACE TABLE`

- Replaces the table if it exists.
- Drops existing table and recreates it with new schema and data.

```sql
CREATE OR REPLACE TABLE table_name (
  column1 TYPE,
  column2 TYPE
);
```

> ⚠️ Existing data and schema will be **overwritten**.

---

### 🧠 4. `REPLACE TABLE`

- Same as `CREATE OR REPLACE`, but you must include `USING`.

```sql
REPLACE TABLE table_name
USING DELTA
AS SELECT * FROM source_table;
```

> 🧹 Will wipe and replace the table contents and schema.

---

### 🛠️ 5. `CREATE TABLE USING` (File Format)

Specify the file format (DELTA, PARQUET, CSV, etc.) for the table.

```sql
CREATE TABLE my_table (
  id INT,
  name STRING
)
USING DELTA;
```

---

### 🌐 6. `CREATE TABLE ... LOCATION` (External Table)

Define the physical storage path.

```sql
CREATE TABLE ext_table (
  id INT,
  data STRING
)
USING PARQUET
LOCATION '/mnt/data/my_table';
```

---

### 💾 7. `CREATE TABLE AS SELECT (CTAS)`

Create and populate a table from a query.

```sql
CREATE TABLE top_customers
USING DELTA
AS
SELECT * FROM customers WHERE spend > 1000;
```

---

### 👀 8. `CREATE TEMPORARY VIEW`

Session-scoped, not stored in metastore.

```sql
CREATE OR REPLACE TEMP VIEW temp_sales AS
SELECT * FROM sales WHERE region = 'US';
```

---

### 📌 Syntax Comparison Table

| Syntax                       | Behavior                                |
|------------------------------|-----------------------------------------|
| `CREATE TABLE`               | Fails if table exists                   |
| `CREATE TABLE IF NOT EXISTS` | Skips creation if table exists          |
| `CREATE OR REPLACE TABLE`    | Drops and recreates table               |
| `REPLACE TABLE`              | Same as above, format must be specified |
| `CREATE TABLE ... USING`     | Specifies format (DELTA, PARQUET, etc.) |
| `CREATE TABLE ... LOCATION`  | External table definition               |
| `CREATE TABLE AS SELECT`     | Creates and loads data from a query     |
| `CREATE TEMP VIEW`           | Creates session-limited view            |

---

### 🧪 Example: All-in-One

```sql
CREATE OR REPLACE TABLE transactions
USING DELTA
LOCATION '/mnt/data/transactions'
AS
SELECT id, amount FROM raw_data WHERE status = 'complete';
```

