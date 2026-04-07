# SQL Server Runbooks & Technical Documentation Portfolio

> **Production-grade SQL Server monitoring, alerting, and operational runbooks built from real-world environments.**

## 👤 Author  
Dennis Lubins  
Technical Operations & Runbook Portfolio

---

## 📌 Overview

This repository showcases real-world SQL Server runbooks and technical documentation built from hands-on experience stabilizing, monitoring, and operating production database environments.

The focus is not just documentation — but **designing operational systems** that improve:

- Reliability  
- Observability  
- Incident response  
- Performance stability  

---

## 🔥 Featured Projects (Start Here)

These represent high-impact, production-grade solutions that demonstrate system design, operational thinking, and advanced SQL Server expertise.

### 🚨 [SQL Server Deadlock Monitoring & Alerting System](./docs/01-deadlock-monitoring-system.md)
- Victim-based deadlock detection using Extended Events  
- Threshold-driven alerting with noise suppression  
- Automated operational response workflow  

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

👉 Demonstrates **SRE-style monitoring design, not just DBA scripting**

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

---

## 🎯 What This Portfolio Demonstrates

- Designing **monitoring and alerting systems**, not just queries  
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
