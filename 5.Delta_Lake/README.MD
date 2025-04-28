## Delta Lake

---

### What is Delta lake..?

Delta Lake is an open-source storage layer, built on top of existing cloud storage(data lake)

> 🧠 **Note** :
> * Delta Lake is just a storage layer and a transaction log format (with .parquet files plus _delta_log/ folder for tracking changes)
> * Delta Lake is the default table format in DataBricks
### Main features of Delta Lake:

* ACID transactions: Ensures data integrity (Atomicity, Consistency, Isolation, Durability) when multiple users or jobs are reading and writing data.

* Schema enforcement: Prevents bad or unexpected data from corrupting your tables.

* Time travel: You can query old versions of your data easily (for audits, rollbacks, etc.).

* Scalable metadata handling: Handles millions of files efficiently (better than traditional Hive tables).

* Unified batch and streaming: You can run batch and streaming workloads on the same data.

### 🧩 What are Metadata Files in Delta Lake?

In Delta Lake, **metadata files** track:
- Table schema (columns, types)
- Table partitioning
- File-level operations (like adds, removes, updates)
- Transaction history (versions)

Metadata files **live inside** a hidden folder called **`_delta_log/`** located alongside the table data.

---

### 📂 What Is Inside `_delta_log/`?

| File Type                                                      | Description                                                                        | Format  |
|:---------------------------------------------------------------|:-----------------------------------------------------------------------------------|:--------|
| **JSON files** (`*.json`)                                      | Transaction logs recording incremental changes (one JSON file per commit/version). | JSON    |
| **Checkpoint files** (`*.checkpoint.parquet`)                  | Compressed snapshot of table state at a point in time to speed up reads.           | Parquet |
| **Delta table protocol files** (`_delta_log/_last_checkpoint`) | Metadata to quickly locate the latest checkpoint file.                             | JSON    |

---

### 🔥 Example of Metadata Files Structure

```
/path/to/table/
├── _delta_log/
│   ├── 00000000000000000000.json
│   ├── 00000000000000000001.json
│   ├── 00000000000000000002.json
│   ├── 00000000000000000003.checkpoint.parquet
│   └── _last_checkpoint
├── part-00000-*.parquet
├── part-00001-*.parquet
└── ...
```

---

### What is data lake..?
It's just like raw storage layer


