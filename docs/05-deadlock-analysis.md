# 🧠 SQL Server Deadlock Analysis: Self-Referencing INSERT Pattern

## 📌 Overview

This document outlines the investigation, root cause analysis, and resolution of a recurring SQL Server deadlock issue affecting a production workload.

The deadlocks were traced to concurrent `INSERT...SELECT` operations against the same table, resulting in page-level locking conflicts.

---

## 🧩 Problem Statement

* Intermittent deadlocks occurring during data processing operations
* Impacting insert operations into a core transactional table (`TransactionalTable`)
* Deadlocks observed under moderate concurrency
* Application experienced failed transactions and retries

---

## 🔍 Investigation

### 1️⃣ Deadlock Graph Analysis

Deadlock XML revealed:

* Two concurrent sessions performing `INSERT` operations
* Both sessions targeting the same table
* Locks involved:

  * `S` (Shared) locks during read phase
  * `IX` (Intent Exclusive) locks during insert phase
* Deadlock occurred at **page-level resources**

**Key Finding:**
Both processes held shared locks on different pages and attempted to convert to write locks on each other's pages.

---

### 2️⃣ Index Structure Review

The target table contained:

* A **wide clustered primary key** (multi-column composite key)
* A **nonclustered index** created via Database Tuning Advisor (DTA)

Observations:

* The nonclustered index was heavily utilized for read operations
* Operational statistics showed:

  * High page lock counts
  * Elevated latch wait activity
* Insert operations required maintaining both clustered and nonclustered index structures

---

### 3️⃣ Operational Statistics Analysis

Using:

```sql
sys.dm_db_index_operational_stats
```

Findings:

* Nonclustered index exhibited significantly higher:

  * Page lock activity
  * Latch wait counts
* Indicates contention during insert operations

---

### 4️⃣ Query Pattern Identification

Using Query Store and DMV analysis:

```sql
INSERT INTO TransactionalTable (...)
SELECT ...
FROM TransactionalTable
WHERE <filter conditions>
```

**Key Insight:**
The workload performs **self-referencing inserts**, copying data from the same table into itself.

---

## 💣 Root Cause

The deadlocks were caused by a concurrency pattern involving:

### 🔄 Self-Referencing `INSERT...SELECT`

Each session performed:

1. **Read Phase**

   * Acquired `S` (shared) locks on source pages

2. **Write Phase**

   * Attempted to insert rows
   * Required `IX/X` locks on target pages

### ⚠️ Deadlock Scenario

* Session A locks Page 1 (read), attempts to write Page 2
* Session B locks Page 2 (read), attempts to write Page 1

➡️ Circular dependency results in deadlock

---

## 🛠️ Resolution

### 🥇 Primary Fix: Break Read/Write Dependency

Refactored query using a staging approach:

```sql
SELECT ...
INTO #StagingData
FROM TransactionalTable
WHERE <filter conditions>;

INSERT INTO TransactionalTable (...)
SELECT ...
FROM #StagingData;
```

**Benefits:**

* Eliminates overlapping read/write locks
* Prevents lock conversion deadlocks
* Improves concurrency safety

---

### 🥈 Index-Level Mitigations

#### 1. Adjust Fill Factor

```sql
ALTER INDEX [IX_TransactionalTable_Optimized]
ON dbo.TransactionalTable
REBUILD WITH (FILLFACTOR = 80);
```

* Reduces page splits
* Improves insert distribution

#### 2. Disable Page-Level Locking (Targeted Test)

```sql
ALTER INDEX [IX_TransactionalTable_Optimized]
ON dbo.TransactionalTable
SET (ALLOW_PAGE_LOCKS = OFF);
```

* Forces row-level locking
* Reduces page-level deadlock scenarios

---

### 🥉 Application-Level Mitigation

Implemented retry logic for deadlock handling:

```sql
IF ERROR_NUMBER() = 1205
BEGIN
    -- Retry logic
END
```

---

## 📈 Outcome

* Deadlocks significantly reduced / eliminated
* Improved transaction reliability
* Reduced contention on heavily used index structures
* Increased overall system stability under concurrent load

---

## 🧠 Key Takeaways

* Self-referencing `INSERT...SELECT` patterns are high-risk under concurrency
* Index usage statistics alone do not indicate optimal design
* Page-level locking can be a major contributor to deadlocks
* Query pattern design is often more critical than index tuning
* Combining Query Store + DMVs provides powerful root cause visibility

---

## 🧰 Tools & Techniques Used

* Deadlock XML analysis
* Query Store
* `sys.dm_exec_query_stats`
* `sys.dm_db_index_operational_stats`
* Index usage statistics
* Execution plan analysis

---

## 📎 Notes

* All identifiers and environment-specific details have been sanitized
* This analysis reflects a real-world production scenario adapted for documentation purposes
