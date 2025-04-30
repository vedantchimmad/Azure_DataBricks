# 🔄 Types of Slowly Changing Dimensions (SCD) in Databricks

Databricks supports multiple types of **Slowly Changing Dimension (SCD)** strategies using **Delta Lake**, **MERGE**, and **SQL logic** to track changes in dimensional data over time.

---

## 🧠 What is SCD?

Slowly Changing Dimensions are used in data warehouses to manage and store **historical data** in dimension tables where data changes slowly but needs to be tracked accurately.

---

## ✅ SCD Type 0: No Changes Allowed

**Frozen dimension** — values never change once inserted.

```text
Use Case: Immutable reference data like Country Codes.
```

---

## ✅ SCD Type 1: Overwrite Old Data (No History)

**Updates the existing record** without keeping history.

```sql
MERGE INTO customer_dim AS target
USING customer_updates AS source
ON target.customer_id = source.customer_id
WHEN MATCHED THEN
  UPDATE SET *
WHEN NOT MATCHED THEN
  INSERT *;
```

🟢 **Use when history is not important** (e.g., typo corrections).

---

## ✅ SCD Type 2: Add New Row for Each Change (Full History)

Tracks changes by **inserting a new row** for each version.

```sql
-- Step 1: Mark current record as inactive
UPDATE customer_dim
SET current_flag = false, end_date = current_date()
WHERE customer_id = '123' AND current_flag = true;

-- Step 2: Insert new active record
INSERT INTO customer_dim (customer_id, name, address, current_flag, start_date, end_date)
VALUES ('123', 'John Doe', 'New Address', true, current_date(), NULL);
```

✅ Requires: `start_date`, `end_date`, and `current_flag`.

🔄 Supports time travel and history tracking with **Delta Lake**.

---

## ✅ SCD Type 3: Add New Columns to Track Recent History

Stores only the **previous value** in a separate column.

```sql
ALTER TABLE customer_dim ADD COLUMNS (previous_address STRING);

UPDATE customer_dim
SET previous_address = address,
    address = 'New Address'
WHERE customer_id = '123';
```

🟢 **Use when only last change is needed**.

---

## ✅ SCD Type 6 (Hybrid of 1, 2, and 3)

Combines:
- Overwrite current record (Type 1)
- Keep previous value (Type 3)
- Maintain full history with new rows (Type 2)

🔁 Most complex, suitable for **advanced analytics** and **audit trails**.

---

## 📌 Summary Table

| Type | Description                       | Tracks History | Additional Columns Needed       |
|------|-----------------------------------|----------------|----------------------------------|
| 0    | No changes allowed                | ❌             | None                             |
| 1    | Overwrites data                   | ❌             | None                             |
| 2    | Inserts new row per change        | ✅             | `start_date`, `end_date`, `flag` |
| 3    | Stores previous value in column   | ✅ (limited)   | `previous_<column>`             |
| 6    | Combines 1, 2, and 3              | ✅             | All of the above                |

---

## ⚙️ Delta Lake + MERGE for SCDs in Databricks

Databricks + Delta Lake provides:
- ACID transactions
- Time travel for historical queries
- Easy implementation of SCD Type 1 and 2 using `MERGE`

---

## 🧠 Best Practices

- Use **Delta tables** with schema evolution.
- Use **MERGE INTO** for efficient updates/inserts.
- Track metadata using `current_flag`, `version`, or timestamps.
- Keep historical data for auditing and machine learning features.

---

# 🏁 Build Reliable, Historical-Aware Warehousing with SCD in Databricks!
