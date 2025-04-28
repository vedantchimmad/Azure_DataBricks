## 📚 Data Objects in Databricks

---

### 🧩 What Are Data Objects?

> 🔥 **Data Objects** in Databricks are fundamental building blocks that represent structured, semi-structured, or unstructured data and its organization inside the platform.

These objects allow users to **store**, **query**, **analyze**, and **manage** data efficiently in a Lakehouse architecture.

---

### 🛠️ Types of Data Objects

| Object                | Description                                                             | Example                                                                            |
|:----------------------|:------------------------------------------------------------------------|:-----------------------------------------------------------------------------------|
| **Database (Schema)** | Logical grouping of tables and views. Like a folder in a filesystem.    | `CREATE DATABASE marketing_data;`                                                  |
| **Table**             | Persistent storage for structured datasets (Delta, Parquet, etc.).      | `CREATE TABLE customers (id INT, name STRING);`                                    |
| **View**              | Virtual tables generated from SQL queries; no data is stored.           | `CREATE VIEW active_customers AS SELECT * FROM customers WHERE status = 'active';` |
| **Temporary View**    | Session-based view that disappears when session ends.                   | `CREATE OR REPLACE TEMP VIEW temp_sales AS SELECT * FROM sales;`                   |
| **Materialized View** | A view that stores the query result, improving read performance.        | Used in reporting and dashboards.                                                  |
| **Function (UDF)**    | Custom reusable function (User-Defined Function) for transformations.   | `CREATE FUNCTION double_price(p FLOAT) RETURNS FLOAT RETURN p*2;`                  |
| **Delta Table**       | Table with ACID transactions, versioning, and time travel capabilities. | `CREATE TABLE orders USING DELTA LOCATION '/mnt/orders';`                          |
| **External Table**    | Table referencing data stored outside Databricks (e.g., S3, Azure).     | External Parquet/CSV/Delta tables.                                                 |
| **Stream Table**      | Table that ingests and processes real-time streaming data.              | Real-time clickstream processing.                                                  |

---

### 🚀 Visual Diagram: How Data Objects Fit Together

🖼️ Conceptual View:

```
Database (Schema)
 ├── Table (Managed or External)
 ├── View (Virtual table from SQL queries)
 ├── Function (Reusable transformation logic)
 └── Stream Table (Real-time updates)
```

Clusters are used to **compute and operate** on these Data Objects.

### 🧠 Important Concepts

| Concept             | Explanation                                                                      |
|:--------------------|:---------------------------------------------------------------------------------|
| **Managed Table**   | Databricks manages both metadata and data storage.                               |
| **External Table**  | Databricks manages only metadata; data resides externally (e.g., S3).            |
| **Delta Format**    | Default for new Databricks tables, supports ACID, schema evolution, time travel. |
| **Views vs Tables** | Views are logical; Tables are physical.                                          |
| **Functions**       | Help modularize and reuse transformations across queries.                        |

---

### 🔥 SQL Examples

```sql
-- Create a database
CREATE DATABASE hr_data;

-- Create a managed table
CREATE TABLE hr_data.employees (
    id INT,
    name STRING,
    salary DOUBLE
);

-- Create a view
CREATE VIEW hr_data.high_salary_employees AS
SELECT * FROM hr_data.employees WHERE salary > 100000;

-- Create a user-defined function
CREATE FUNCTION hr_data.raise_salary(salary DOUBLE)
RETURNS DOUBLE
RETURN salary * 1.10;
```

---

### 📦 Where Data Objects Are Stored

| Storage              | Example                                                                                              |
|:---------------------|:-----------------------------------------------------------------------------------------------------|
| **Managed Storage**  | Delta tables stored in Databricks-managed storage locations.                                         |
| **External Storage** | Tables referencing files in external cloud storage (e.g., AWS S3, Azure Blob, Google Cloud Storage). |

---