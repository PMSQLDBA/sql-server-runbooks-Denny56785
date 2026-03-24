# 📬 SQL Server Alert Notification System
## PowerShell-Based Database Mail Alternative

---

## Overview

This project provides a **scalable, reliable alternative to SQL Server Database Mail** using a **queue-based architecture** combined with **PowerShell-driven email delivery**.

It is designed for environments where:

- Database Mail is unreliable or restricted
- High-volume alerting is required
- Greater control over alert processing is needed

---

## Problem

SQL Server Database Mail can fail due to:

- TLS / .NET compatibility issues
- SMTP connectivity limitations
- Lack of flexibility in alert handling

Additionally, native alerting mechanisms:

- Are tightly coupled to SQL Server
- Lack extensibility
- Provide limited customization

---

## Solution

A **decoupled alerting system** separates alert generation from alert delivery.

### Architecture Components

| Component | Purpose |
|----------|---------|
| SQL Stored Procedures | Capture alert data and enqueue messages |
| Queue Table (`msdb`) | Stores alerts and tracks processing status |
| SQL Agent Job | Executes PowerShell processing on a schedule |
| PowerShell Script | Sends emails via SMTP and updates alert status |

---

## Workflow

```text
SQL Alert / Job Failure
        ↓
Stored Procedure
        ↓
SQLAlertEmailQueue (Pending)
        ↓
SQL Agent Job (Every Minute)
        ↓
PowerShell Script
        ↓
Email Sent → Status Updated
```

---

## Implementation

### 1. Create Mail Queue Table

```sql
CREATE TABLE dbo.SQLAlertEmailQueue (
    AlertID INT IDENTITY(1,1) PRIMARY KEY,
    EmailTo NVARCHAR(500),
    EmailSubject NVARCHAR(500),
    EmailBody NVARCHAR(MAX),
    CreatedDate DATETIME DEFAULT GETDATE(),
    ProcessedDate DATETIME NULL,
    Status VARCHAR(20) DEFAULT 'Pending'
);
```

### 2. Create Stored Procedure for Alert Ingestion

The stored procedure should:

- Accept subject, body, and recipient parameters
- Prevent duplicate alerts through throttling
- Insert new alert records into the queue table

### 3. PowerShell Processing Layer

The PowerShell script should:

- Pull the next pending alert from the queue
- Send email via SMTP
- Use TLS with port 587
- Update the queue record to `Processed` or `Failed`

### 4. SQL Agent Job

The SQL Agent job should:

- Run every minute
- Execute the PowerShell script
- Include a cleanup step to remove old records (e.g., older than 30 days)

### 5. SQL Agent Integration

```sql
EXEC master.dbo.usp_SendAlertEmail
    @Subject = @EmailSubject,
    @Body = @Body,
    @To = 'alertemailaddress',
    @ThrottleMinutes = 5;
```

---

## Key Features

- Decoupled alert processing
- Retry and failure tracking
- Custom formatting of alerts
- Throttling to prevent alert spam
- Supports:
  - SQL Agent Jobs
  - SQL Server Alerts

---

## Operational Considerations

- Requires outbound SMTP access on port 587
- SQL Agent jobs must remain enabled
- Credentials should be secured (avoid plaintext in production)
- Regular cleanup of the queue table is recommended

---

## Outcome

This solution provides:

- Greater reliability than Database Mail
- Improved scalability for high alert volumes
- Enhanced control over alert formatting and delivery

---

## AI / Knowledge Base Optimization Notes

This documentation is structured for:

- Retrieval-based systems
- Chatbot-assisted troubleshooting
- Runbook automation

Design considerations:

- Clear separation of architecture, workflow, and implementation
- Modular sections for indexing
- Consistent formatting for machine parsing

---

## Summary

A modern, resilient alerting system replacing Database Mail with a **flexible, scalable, production-ready architecture**.
