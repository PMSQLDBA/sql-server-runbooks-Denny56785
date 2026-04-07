# 🚨 SQL Server Deadlock Monitoring & Alerting System
## Victim-Based Detection Approach

---

## 📖 Overview

This document outlines a lightweight monitoring system for detecting SQL Server deadlocks by analyzing **deadlock victim events over time** and triggering alerts when thresholds are exceeded.

The solution leverages:

- Extended Events (`system_health`)
- A state-based tracking approach

to identify deadlock trends within defined time intervals.

---

## ⚠️ Problem

SQL Server does **not provide a direct counter** for deadlocks over time.

### Key Challenges

- Deadlock events are **transient** and quickly purged  
- No built-in mechanism for **interval-based deadlock counting**  
- Difficult to detect patterns such as **deadlock storms**  

---

## 💡 Solution Approach

Deadlocks are tracked by identifying **deadlock victims** from Extended Events data.

### Core Concept

> Every deadlock has at least one victim → **counting victims = counting deadlocks**

### System Behavior

- Captures deadlock victim events  
- Stores a processing watermark  
- Compares counts between intervals  
- Triggers alerts when thresholds are exceeded  

---

## 🧱 Architecture

```text
Extended Events (system_health)
        ↓
Deadlock Victim Extraction
        ↓
State Table (Watermark)
        ↓
SQL Agent Job (Scheduled)
        ↓
Threshold Evaluation
        ↓
Alert Notification
```

---

## 🔧 Key Components

### 1. Deadlock State Table

A lightweight, single-row table used to track processing state:

```sql
CREATE TABLE dbo.DeadlockMonitorState (
    MonitorName sysname PRIMARY KEY,
    LastProcessedUtc datetime2(3),
    LastUpdatedLocal datetime2(3)
);
```

**Purpose:**

- Stores last processed timestamp (watermark)  
- Prevents duplicate counting  
- Requires no indexing or cleanup  

---

### 2. SQL Agent Job

Runs on a schedule (default: every 5 minutes):

- Reads new deadlock events since last run  
- Counts victim occurrences  
- Compares against threshold  
- Sends alert if threshold is exceeded  

---

### 3. Deadlock Extraction Logic

Deadlocks are parsed from Extended Events:

```sql
sys.fn_xe_file_target_read_file('system_health*.xel', NULL, NULL, NULL)
```

**Process:**

- Extract XML deadlock reports  
- Parse victim processes  
- Resolve database context  

---

### 4. Deduplication Logic

To prevent double counting, events are deduplicated using:

- Timestamp  
- Victim ID  
- Database name  

---

### 5. Threshold-Based Alerting

**Example Configuration:**

- Threshold: ≥ 2 deadlocks  
- Interval: 5 minutes  

**Trigger Condition:**

```
Total Deadlock Victims >= Threshold
```

---

## 📢 Alert Output

Alerts include:

- Total deadlocks detected  
- Per-database breakdown  
- Detailed event list:
  - Timestamp  
  - Database  
  - Victim ID  

### Example Insights

- Per-database victim counts  
- Timestamped victim details for investigation  

---

## 🧠 Key Design Decisions

### ✔ Victim-Based Counting

- Reliable proxy for deadlock events  
- Avoids dependency on full deadlock graph analysis  

### ✔ Watermark Tracking

- Ensures incremental processing  
- Prevents reprocessing old events  

### ✔ Lightweight State Table

- Single-row design  
- No growth, no maintenance required  

---

## ⚙️ Operational Considerations

- Threshold tuning is critical:
  - Low threshold → noisy alerts  
  - High threshold → missed issues  

- Job frequency defines monitoring window  

- Requires Extended Events (`system_health`) to be enabled  

---

## 📊 Outcome

This system enables:

- Detection of deadlock trends over time  
- Early identification of contention issues  
- Actionable alerting with detailed context  

---

## 🤖 AI / Knowledge Base Optimization Notes

This documentation is structured for:

- Event-driven troubleshooting workflows  
- Clear separation of detection logic and alerting behavior  
- Automated analysis or chatbot-driven diagnostics  

---

## 📌 Summary

A lightweight, efficient deadlock monitoring system using **victim-based detection**, enabling **trend analysis, proactive alerting, and improved SQL Server reliability**.

---

## ✍️ Author Notes

This system was developed to solve a real-world issue where deadlocks were difficult to quantify over time due to the transient nature of Extended Events data.

The original implementation consisted of raw scripts and operational notes. This document represents a structured version designed for reuse, troubleshooting, and team adoption.

---

## 📎 Source Documentation

Derived from real-world implementation artifacts and sanitized for public portfolio use.

- [View Original PDF](../source/deadlock-monitor.pdf)
