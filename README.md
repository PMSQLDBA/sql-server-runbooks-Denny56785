# SQL Server Runbooks & Technical Documentation Portfolio

> **Technical Operations portfolio focused on reliability, performance, and production stability — using SQL Server as a core system component.**

## 👤 Author  
Dennis Lubins  
Technical Operations & Runbook Portfolio

---

## 📌 Overview

This repository showcases real-world SQL Server runbooks and technical documentation built from hands-on experience stabilizing, monitoring, and operating production database environments.

> While centered on SQL Server, this portfolio reflects broader **technical operations and system reliability practices** — including monitoring design, incident response, and maintenance strategy.

The focus is not just documentation — but **designing operational systems** that improve:

- Reliability  
- Observability  
- Incident response  
- Performance stability  

---

## 🚀 Case Studies

Real-world production scenarios demonstrating investigation, system design, and operational impact.

---

### 🔥 [Reducing 2,000+ Daily SQL Server Deadlocks to Near Zero](./docs/01-case-study-deadlocks.md)

- Identified systemic deadlock patterns using Extended Events  
- Replaced reactive fixes with visibility-driven analysis  
- Targeted indexing, statistics, and schema improvements  
- Built monitoring to prevent regression  

**Results:**  
~2,000 deadlocks/day → near zero across 3 servers  

📎 **Related Runbooks & Systems:**
- [Deadlock Monitoring & Alerting System](./docs/01-deadlock-monitoring-system.md)
- [Deadlock Analysis Query](./docs/08-deadlock-analysis-query.md)
- [SQL Server Agent Runbooks](./source/sql_alert_runbooks.md) 

---

### ⚙️ [Eliminating TempDB Latency from Statistics Maintenance](./docs/02-case-study-tempdb.md)

- Traced disk latency to inefficient FULLSCAN statistics updates  
- Replaced blanket maintenance with targeted strategy  
- Implemented phased execution (daily sampled + monthly fullscan)  

**Results:**  
Reduced TempDB load and eliminated maintenance window latency spikes  

📎 **Related Runbooks & Systems:**
- [Statistics Maintenance Framework](./docs/07-statistics-maintenance-framework.md)
- [SQL Server Agent Runbooks](./source/sql_alert_runbooks.md) 

---

### 📡 [Building a SQL Server Alerting System Without Database Mail](./docs/03-case-study-alerting.md)

- Replaced unreliable Database Mail with queue-based alerting system  
- Integrated SQL + PowerShell for controlled delivery  
- Implemented full alert lifecycle tracking and retry logic  

**Results:**  
Reliable alerting, improved visibility, and simplified troubleshooting  

📎 **Related Runbooks & Systems:**
- [SQL Alert Notification System (PowerShell-Based)](./docs/02-sql-alert-notification-system.md)
- [SQL Server Agent Runbooks](./source/sql_alert_runbooks.md)

---

## 🧠 Operational Philosophy

→ [How I Approach Production Systems](./docs/operational-philosophy.md)

---

## 🔥 Featured Projects (Start Here)

These represent high-impact, production-grade solutions that demonstrate system design, operational thinking, and advanced SQL Server expertise.

### 🚨 [SQL Server Deadlock Monitoring & Alerting System](./docs/01-deadlock-monitoring-system.md)
- Victim-based deadlock detection using Extended Events  
- Threshold-driven alerting with noise suppression  
- Automated operational response workflow  
- Built from foundational deadlock analysis and pattern identification  

---

### 📊 [SQL Server Disk Latency Monitoring & Alerting System](./docs/06-disk-latency-monitor.md)
- Interval-based latency detection (solves DMV limitations)  
- Multi-job monitoring architecture (collector, evaluator, cleanup)  
- Advanced alerting logic:
  - Rolling windows  
  - Consecutive breach detection  
  - Cooldown suppression  
- Per-database tuning (tempdb, reporting workloads)  
- Historical tracking for trend and incident analysis  

---

## 📂 Full Runbook Library

### 🔹 Monitoring & Alerting Systems

#### [SQL Server Deadlock Monitoring & Alerting System](./docs/01-deadlock-monitoring-system.md)
- Deadlock detection and alerting strategy  
- Extended Events parsing  

#### [SQL Server Disk Latency Monitoring & Alerting System](./docs/06-disk-latency-monitor.md)
- Real-time I/O latency monitoring  
- Interval-based analysis  

#### [SQL Alert Notification System (PowerShell-Based)](./docs/02-sql-alert-notification-system.md)
- Queue-based alerting system  
- SQL + PowerShell integration  
- Reliable alternative to Database Mail  

---

### 🔹 Troubleshooting & Analysis

#### [SQL Server Deadlock Analysis Query (Extended Events)](./docs/08-deadlock-analysis-query.md)
- Extended Events (`system_health`) deadlock extraction  
- XML parsing for victim-based identification  
- Timeframe-based analysis for pattern detection  
- Foundation for monitoring system design and threshold tuning  

#### [SQL Server Deadlock Analysis: Self-Referencing INSERT Pattern](./docs/05-deadlock-analysis.md)
- Real-world deadlock investigation  
- Query Store and DMV-based analysis  
- Concurrency pattern identification  
- Practical mitigation strategies  

#### [Database Mail TLS & .NET Troubleshooting](./docs/03-database-mail-tls-troubleshooting.md)
- TLS 1.2 validation  
- .NET compatibility checks  
- Registry-level fixes  

---

### 🔹 Operations & Maintenance

#### [SQL Server Database File Migration Runbook](./docs/04-database-file-migration.md)
- File relocation procedures  
- System DB handling  
- Backup/restore migration

#### [SQL Server Statistics Maintenance Optimization: TempDB Contention Reduction](./docs/07-statistics-maintenance-framework.md)
- Investigation of TempDB latency during statistics maintenance  
- Heavy table identification and workload scoring approach  
- Targeted statistics maintenance strategy (vs blanket updates)  
- Phased execution model (daily sampled + monthly fullscan)  
- Reduction of maintenance overhead and improved system stability    

---

## 🎯 What This Portfolio Demonstrates

- Designing **monitoring and alerting systems**, not just queries  
- Improving **system reliability and operational stability**    
- Translating complex incidents into **repeatable operational runbooks**  
- Building **production-ready automation and workflows**  
- Applying **noise reduction and alert tuning strategies**  
- Bridging **deep technical work with clear documentation**

---

## 🧠 Documentation Approach

Each runbook is structured for real-world usability:

- Overview  
- Problem  
- Investigation  
- Solution  
- Implementation  
- Considerations  
- Outcome  

---

## 🤖 AI-Ready Design

- Structured for content chunking  
- Clear procedural steps  
- Reusable command blocks  

---

## ⚙️ Focus Areas

- SQL Server performance and concurrency  
- Operational reliability and incident response  
- Monitoring and alerting system design  
- Runbook-driven operations and knowledge reuse  

---

## 🛠️ Technologies

- SQL Server  
- PowerShell  
- Extended Events  
- .NET  
- Windows Server  

---

## ⚠️ Notes

- All content anonymized  
- Based on real-world systems  

---

## 📈 Future Additions

- Index optimization  
- Performance tuning  
- Advanced monitoring strategies  

---

## 📬 Contact

LinkedIn: https://www.linkedin.com/in/dennis-lubins-25372a356

---

## 📄 License

This project is licensed under the MIT License.
