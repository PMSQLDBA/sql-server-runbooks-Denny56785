# 📊 SQL Server Disk Latency Monitoring & Alerting System

---

## 📌 Overview

This runbook outlines a custom SQL Server disk latency monitoring system designed to capture **real-time I/O latency trends** using interval-based analysis rather than cumulative DMV data.

The solution leverages:
- Snapshot collection
- Interval-based calculations
- Threshold-driven alerting
- Historical tracking
- Noise suppression controls

---

## 🎯 Purpose

SQL Server’s native DMV (`sys.dm_io_virtual_file_stats`) provides **cumulative latency metrics since last restart**, which makes it difficult to:

- Detect short-lived latency spikes
- Identify transient storage issues
- Alert accurately in real-time

This system solves that by:
- Capturing periodic snapshots
- Calculating latency **between intervals**
- Applying configurable alert thresholds
- Tracking alert history for trend analysis

---

## ❗ Problem This Solves

Traditional monitoring approaches:
- Use cumulative averages
- Miss short-duration spikes
- Generate excessive alert noise

This solution provides:
- Interval-based latency detection
- Controlled alerting (cooldowns, thresholds)
- Historical visibility into latency patterns

---

## 🧠 Architecture

```
Snapshot Collector Job (Every 5 Minutes)
        ↓
DiskLatencySnapshots Table
        ↓
Alert Monitor Job (Every 5 Minutes)
        ↓
DiskLatencyAlertHistory Table
        ↓
SQL Agent Operator Notification
        ↓
Cleanup Job (Daily Retention Enforcement)
```

---

## ⚙️ Components

### 📥 Snapshot Table

Stores raw I/O statistics captured at intervals.

**Table:** `msdb.dbo.DiskLatencySnapshots`

Key data:
- Read/Write counts
- Stall times (ms)
- File-level granularity
- Timestamped snapshots

---

### 🚨 Alert History Table

Tracks latency breaches and alert decisions.

**Table:** `msdb.dbo.DiskLatencyAlertHistory`

Stores:
- Breach type (READ / WRITE / BOTH)
- Severity (WARNING / CRITICAL)
- Alert suppression state
- Thresholds used at time of evaluation

---

## 🔁 Job Design

### 1️⃣ Snapshot Collector Job
- Frequency: Every 5 minutes
- Purpose: Capture current I/O stats from DMVs
- Source: `sys.dm_io_virtual_file_stats`

---

### 2️⃣ Alert Monitor Job
- Frequency: Every 5 minutes
- Purpose:
  - Compare current vs previous snapshot
  - Calculate interval latency
  - Apply thresholds
  - Determine alert conditions

---

### 3️⃣ Cleanup Job
- Frequency: Daily
- Purpose:
  - Prevent table growth
  - Maintain performance

Default retention:
- Snapshots: 14 days
- Alerts: 30 days

---

## 📐 Latency Calculation Logic

Latency is calculated using **interval deltas**, not cumulative values:

```sql
avg_read_ms =
(io_stall_read_ms_current - io_stall_read_ms_previous)
/
(num_of_reads_current - num_of_reads_previous)

avg_write_ms =
(io_stall_write_ms_current - io_stall_write_ms_previous)
/
(num_of_writes_current - num_of_writes_previous)
```

---

## 🚨 Alert Behavior

### 🔶 Warning Alerts
Triggered when:
- Latency exceeds threshold
- Occurs multiple times within a rolling window

Controlled by:
- Consecutive breach count
- Time window (minutes)

---

### 🔴 Critical Alerts
Triggered when:
- Latency exceeds critical threshold

Behavior:
- Immediate alert
- No need for repeated occurrences

---

## 🔕 Alert Noise Control

To prevent alert storms:

### ✔ Consecutive Breaches
- Requires multiple occurrences before alerting

### ✔ Rolling Window
- Evaluates breaches within time range

### ✔ Cooldown Period
- Suppresses repeated alerts for same issue

---

## ⚙️ Configuration Parameters

### Default Thresholds

| Setting | Description |
|--------|-------------|
| ReadLatencyMs | Warning threshold for reads |
| WriteLatencyMs | Warning threshold for writes |
| CriticalReadLatencyMs | Critical threshold for reads |
| CriticalWriteLatencyMs | Critical threshold for writes |
| MinReads / MinWrites | Minimum activity required |
| ConsecutiveBreachesReq | Number of occurrences before alert |
| WarningWindowMinutes | Time window for evaluation |
| CooldownMinutes | Alert suppression period |

---

## 🎯 Per-Database Overrides

Custom thresholds can be applied for specific databases:

Use cases:
- Reporting databases (write-heavy)
- tempdb (high churn)
- High-volume OLTP systems

Overrides allow:
- Higher thresholds
- Different sensitivity
- Reduced false positives

---

## 📧 Example Alert Output

```
Subject:
SQL Disk Latency Alert on SERVER_NAME

Body:

Disk latency alert based on snapshot interval.

DB: SampleDB
File: SampleDB_Data
Type: ROWS

Breach: WRITE
Severity: CRITICAL

Reads: 3200
Writes: 18450

Latency:
Read: 95 ms
Write: 412 ms

Interval:
2026-01-01 09:10:00 -> 2026-01-01 09:15:00
```

---

## 🔍 Validation Queries

### Snapshot Table

```sql
SELECT TOP (1000) *
FROM msdb.dbo.DiskLatencySnapshots
ORDER BY SnapshotTime DESC;
```

---

### Alert History

```sql
SELECT TOP (1000) *
FROM msdb.dbo.DiskLatencyAlertHistory
ORDER BY AlertTime DESC;
```

---

## 🧪 Reset / Testing

To clear data during testing:

```sql
TRUNCATE TABLE msdb.dbo.DiskLatencyAlertHistory;
TRUNCATE TABLE msdb.dbo.DiskLatencySnapshots;
```

⚠️ Warning:
- This removes all historical data
- Use only in non-production or controlled scenarios

---

## 🛠️ Deployment Steps

1. Create Snapshot and Alert tables  
2. Create required indexes  
3. Deploy SQL Agent Jobs:
   - Snapshot Collector  
   - Alert Monitor  
   - Cleanup Job  
4. Configure SQL Agent Operator  
5. Adjust thresholds based on environment  
6. Allow multiple job cycles for data population  

---

## 🔍 Troubleshooting

### No Alerts Generated
- Ensure sufficient read/write activity  
- Verify thresholds are not too high  
- Confirm jobs are running successfully  

---

### No Data in Snapshot Table
- Wait for at least 2 collection cycles  
- Verify DMV access  
- Check SQL Agent job execution  

---

### Excessive Alerts
- Increase:
  - ConsecutiveBreachesReq  
  - CooldownMinutes  
- Adjust MinReads / MinWrites  

---

## ⚠️ Limitations

- Requires multiple intervals before evaluation  
- Not suitable for sub-minute monitoring  
- Dependent on SQL Agent scheduling accuracy  

---

## 🧾 Notes

- Designed for low overhead  
- Safe for production environments  
- Highly customizable per workload type  

---

## 📎 Source Documentation

Derived from real-world implementation artifacts and sanitized for public portfolio use.

- [View Original PDF](../source/disk-latency-monitor.pdf)
