## 🔄 Table-Valued Functions (TVFs) in Databricks

---

### 📚 What Are TVFs?

> 🧠 A **Table-Valued Function (TVF)** is a **custom SQL function that returns a table** (not a scalar value).

You can think of it like a **SQL macro** or **query shortcut** — it's reusable and parameterized.

---

### ✅ Why Use TVFs?

| Benefit             | Description                              |
|---------------------|------------------------------------------|
| 🔁 Reusability      | Avoid repeating the same query logic     |
| 🎯 Parameterization | Accept input parameters (like filters)   |
| 🔒 Governance       | Encapsulate logic for auditing and reuse |
| ⚙️ Composability    | Use in joins, subqueries, or views       |

---

### 🛠️ Syntax: Create a TVF

```sql
CREATE FUNCTION function_name(param1 TYPE, param2 TYPE)
RETURNS TABLE
RETURN
  SELECT ... FROM ... WHERE ...;
```

---

### 💡 Example 1: Filtered Sales Data Function

```sql
CREATE FUNCTION get_sales_by_region(region STRING)
RETURNS TABLE
RETURN
  SELECT * FROM sales WHERE region = region;
```

### ✅ Usage:

```sql
SELECT * FROM get_sales_by_region('west');
```

---

### 💡 Example 2: Time-Filtered Delta Query

```sql
CREATE FUNCTION active_users_since(ts TIMESTAMP)
RETURNS TABLE
RETURN
  SELECT user_id, last_active
  FROM users
  WHERE last_active >= ts;
```

### ✅ Usage:

```sql
SELECT * FROM active_users_since('2024-01-01');
```

---

### 🔁 TVF with Joins

```sql
CREATE FUNCTION top_products(region STRING)
RETURNS TABLE
RETURN
  SELECT p.product_id, p.name, SUM(s.amount) AS revenue
  FROM sales s
  JOIN products p ON s.product_id = p.product_id
  WHERE s.region = region
  GROUP BY p.product_id, p.name;
```

```sql
SELECT * FROM top_products('east') ORDER BY revenue DESC;
```

---

### 🧪 How It's Different from Views

| Feature            | View   | TVF   |
|--------------------|--------|-------|
| Parameters         | ❌ No   | ✅ Yes |
| Returns Table      | ✅      | ✅     |
| Reusable           | ✅      | ✅     |
| Dynamic Logic      | ❌      | ✅     |
| Good for Pipelines | ✅      | ✅     |

---
### 📌 Notes

- Available in **Databricks SQL** (Unity Catalog-enabled workspace recommended).
- Can be used in **views**, **joins**, **subqueries**, and **materialized views**.
- **TVFs can’t use INSERT/UPDATE/DELETE** — only return SELECT results.

---

### 🔍 Check Existing Functions

```sql
SHOW USER FUNCTIONS;
DESCRIBE FUNCTION get_sales_by_region;
```
---

## 📚 Built-in Table-Valued Functions (TVFs) in Databricks SQL

---

### 🧩 What Are TVFs?

> 🧠 Table-Valued Functions (TVFs) return a **virtual table** you can query directly in SQL — unlike scalar functions, which return a single value.

Built-in TVFs simplify file access, schema introspection, and structured data querying without requiring manual table creation.

---

### 📦 Common Built-in TVFs

| TVF                         | Purpose                                           | Example                                                     |
|-----------------------------|---------------------------------------------------|-------------------------------------------------------------|
| `read_files()`              | Query files (CSV, JSON, Parquet, etc.) as a table | `SELECT * FROM read_files('/mnt/data/').OPTIONS(...)`       |
| `read_parquet()`            | Shortcut to query Parquet files                   | `SELECT * FROM read_parquet('/mnt/data/file.parquet')`      |
| `read_csv()`                | Shortcut for reading CSV files with options       | `SELECT * FROM read_csv('/mnt/data/file.csv') OPTIONS(...)` |
| `read_json()`               | Read JSON files as tables                         | `SELECT * FROM read_json('/mnt/data/file.json')`            |
| `describe_history()`        | View Delta table version history                  | `SELECT * FROM describe_history('delta_table')`             |
| `describe_detail()`         | View table-level metadata                         | `SELECT * FROM describe_detail('delta_table')`              |
| `system.builtin.catalog()`  | Lists all catalogs                                | `SELECT * FROM system.builtin.catalog()`                    |
| `system.builtin.schemata()` | Lists all schemas                                 | `SELECT * FROM system.builtin.schemata()`                   |
| `system.builtin.tables()`   | Lists all tables in a schema                      | `SELECT * FROM system.builtin.tables()`                     |

---

### 🔍 Example 1: Read a CSV File Without Creating a Table

```sql
SELECT * FROM read_csv('dbfs:/mnt/raw/customers.csv')
OPTIONS (
  header = 'true',
  inferSchema = 'true'
);
```

---

### 🧠 Example 2: View Delta Table History

```sql
SELECT * FROM describe_history('main.sales.transactions');
```

> Shows all commits (timestamp, user, operation, notebook, version, etc.)

---

### 🗂️ Metadata TVFs (Unity Catalog)

If you're using **Unity Catalog**, you can also use:

```sql
SELECT * FROM system.information_schema.tables;
SELECT * FROM system.information_schema.columns;
```

---
## 🚧 Limitations

- Only available in **Databricks SQL Warehouses**
- Not directly usable in PySpark
- Some functions require **Unity Catalog enabled workspace**

---
## 📂 Where Are Table-Valued Functions (TVFs) Stored in Databricks?

---

### 🔹 Built-in TVFs

Built-in TVFs like `read_files()` and `describe_history()` are **predefined by Databricks** and **not stored** in your catalogs or schemas.

### ✅ Example

```sql
-- Using a built-in TVF
SELECT * FROM read_csv('/mnt/data/file.csv')
OPTIONS (header='true', inferSchema='true');
```

- 📍 **Storage**: Internal Databricks system
- 🔒 **Permissions**: Always accessible to SQL Warehouse users
- 🧠 **Not shown in SHOW FUNCTIONS**

---

### 🔸 User-Defined TVFs

User-defined TVFs are **stored in the Unity Catalog**, within a specified schema.

### ✅ Example

```sql
-- Create a custom table-valued function
CREATE FUNCTION my_catalog.sales_schema.get_top_customers(limit INT)
RETURNS TABLE
RETURN
  SELECT * FROM customers ORDER BY spend DESC LIMIT limit;
```

### 🔍 Query the TVF

```sql
SELECT * FROM my_catalog.sales_schema.get_top_customers(10);
```

---

### 📁 Storage Details

| Type              | Stored In            | Defined By   | Visible Using                |
|-------------------|----------------------|--------------|------------------------------|
| Built-in TVFs     | System (Databricks)  | Databricks   | ❌ Not visible                |
| User-defined TVFs | Unity Catalog schema | You          | `SHOW FUNCTIONS IN <schema>` |

---

### 🛡️ Permission Example

```sql
GRANT EXECUTE ON FUNCTION my_catalog.sales_schema.get_top_customers TO finance_team;
```

---

### 🔧 Inspect Existing Functions

```sql
SHOW FUNCTIONS IN my_catalog.sales_schema;
DESCRIBE FUNCTION my_catalog.sales_schema.get_top_customers;
```

---

