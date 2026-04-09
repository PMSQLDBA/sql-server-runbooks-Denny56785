# 📊 SQL Server Statistics Maintenance Optimization: TempDB Contention Reduction

## 📌 Overview

This document outlines the investigation and optimization of SQL Server statistics maintenance processes that were causing elevated TempDB disk latency during scheduled maintenance windows.

The solution focuses on replacing blanket statistics updates with a targeted, table-driven approach to improve efficiency and reduce system impact.

---

## 🧩 Problem Statement

* High TempDB disk latency during statistics maintenance windows
* Long-running maintenance jobs impacting overall system performance
* Blanket statistics updates applied across all tables regardless of usage
* Inefficient maintenance on low-impact or infrequently modified tables

---

## 🔍 Investigation

### 1️⃣ TempDB Pressure Analysis

Observed consistent TempDB latency spikes during early morning maintenance windows.

**Key Finding:**
Spikes aligned directly with scheduled statistics update jobs, particularly during FULLSCAN operations.

---

### 2️⃣ Statistics Maintenance Review

Existing maintenance approach:

* Broad `UPDATE STATISTICS` across all user tables
* No prioritization based on table size or modification activity
* FULLSCAN applied to large tables during monthly maintenance

**Key Finding:**
High-cost operations were being applied uniformly, regardless of actual need.

---

### 3️⃣ Heavy Table Identification

Developed a scoring model to classify tables based on:

* Table size
* Row count
* Number of statistics
* Index count
* Modification activity

Tables were categorized into:

* VERY HEAVY
* HEAVY
* MODERATE
* LIGHT

**Key Insight:**
A small subset of tables accounted for the majority of maintenance cost and TempDB usage.

---

## 💣 Root Cause

The issue was driven by a combination of:

* Blanket statistics maintenance across all tables
* FULLSCAN operations on large tables
* Lack of prioritization or targeting
* Concurrent maintenance workload increasing TempDB contention

---

## 🛠️ Solution

### 🥇 Targeted Statistics Maintenance Strategy

Replaced blanket updates with a curated, table-driven approach:

* Focus on VERY HEAVY and HEAVY tables
* Select MODERATE tables based on modification activity
* Exclude low-impact tables from frequent maintenance

---

### 🥈 Phased Execution Model

Implemented a two-pass strategy:

**First Pass**
* Targets MODERATE / LIGHT tables
* Faster execution
* Reduces initial system load

**Second Pass**
* Targets VERY HEAVY / HEAVY tables
* Handles high-cost operations separately
* Improves control and troubleshooting

---

### 🥉 Daily vs Monthly Maintenance

**Daily**
```sql
UPDATE STATISTICS [schema].[table] WITH SAMPLE 10 PERCENT;
```

* Lower overhead
* Maintains stats freshness for active tables

**Monthly**
```sql
UPDATE STATISTICS [schema].[table] WITH FULLSCAN;
```

* Higher accuracy
* Restricted to high-impact tables only

---

### 🏗️ Job Implementation

* SQL Agent jobs created using table-driven target lists
* Separate jobs for Daily and Monthly execution
* Scheduling aligned with maintenance windows
* Execution logging added for visibility and troubleshooting

---

## 📈 Outcome

* Reduced TempDB latency during maintenance windows
* Improved job execution times and predictability
* Eliminated unnecessary statistics updates on low-impact tables
* Increased control over maintenance workload

---

## 🧠 Key Takeaways

* Blanket statistics maintenance is inefficient in large environments
* FULLSCAN should be used selectively, not broadly
* A small number of tables often drive the majority of maintenance cost
* Separating maintenance into phases improves stability and control
* Targeted maintenance provides better balance between performance and overhead

---

## 🧰 Tools & Techniques Used

* `sys.dm_db_stats_properties`
* `sys.tables`, `sys.indexes`, `sys.partitions`
* TempDB performance monitoring
* SQL Agent job scheduling
* Custom table scoring methodology

---

## 📎 Notes

* All identifiers and environment-specific details have been sanitized
* Approach is adaptable to environments of varying size and workload patterns
  
---

## 📎 Source Documentation

Derived from real-world implementation artifacts and sanitized for public portfolio use.

- [View Original PDF](../source/statistics-maintenance-framework.pdf)
