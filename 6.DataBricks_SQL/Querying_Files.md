## 📄 Querying Files Directly with SQL in Databricks

---

### 🧩 What Is It?

> 🔍 You can run **SQL queries directly on files** (Parquet, CSV, JSON, Delta, etc.) without registering them as formal tables in the metastore.

This is ideal for:
- Exploring raw files before ingestion
- Quick one-time transformations
- Temporary pipelines

---

### 📦 Supported File Formats

| Format   | Extension       |
|----------|-----------------|
| Parquet  | `.parquet`      |
| CSV      | `.csv`          |
| JSON     | `.json`         |
| Delta    | Delta directory |
| Avro     | `.avro`         |

---

### 🔥 Syntax Examples

### 1. Query a Parquet File

```sql
SELECT * FROM parquet.`/mnt/data/sales_data.parquet`
WHERE amount > 100;
```

### 2. Query a CSV File with Options

```sql
SELECT * FROM csv.`/mnt/data/customers.csv`
OPTIONS (
  header = "true",
  inferSchema = "true"
);
```

### 3. Query a JSON File

```sql
SELECT name, age FROM json.`/mnt/data/users.json`;
```

### 4. Query a Delta Directory (Delta Table format on disk)

```sql
SELECT * FROM delta.`/mnt/delta/events`
WHERE event_type = 'purchase';
```

---

### 💡 Notes

- You can use **full SQL expressions**, joins, filters, aggregations, etc.
- Files must be accessible through **Databricks File System paths** (e.g., `/mnt/`, `/dbfs/`).
- Works seamlessly with **structured file formats** like Parquet and Delta.

---

### 🛡️ Security Tip

If querying sensitive files, ensure access is controlled via:
- Unity Catalog (if enabled)
- DBFS or cloud storage access policies (e.g., S3 IAM roles or Azure RBAC)

---
