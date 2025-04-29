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