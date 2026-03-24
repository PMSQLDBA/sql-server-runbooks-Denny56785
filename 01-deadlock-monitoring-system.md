📬 SQL Server Alert Notification System
🚀 PowerShell-Based Database Mail Alternative
📖 Overview

This project provides a scalable, reliable alternative to SQL Server Database Mail by using a queue-based architecture combined with PowerShell-driven email delivery.

It is designed for environments where:

❌ Database Mail is unreliable or restricted
📈 High-volume alerting is required
⚙️ Greater control over alert processing is needed
⚠️ Problem

SQL Server Database Mail can fail due to:

🔐 TLS / .NET compatibility issues
🌐 SMTP connectivity limitations
🧩 Lack of flexibility in alert handling

Additionally, native alerting mechanisms:

Are tightly coupled to SQL Server
Lack extensibility
Provide minimal customization
💡 Solution

A decoupled alerting system that separates alert generation from delivery.

🧱 Architecture Components
Component	Purpose
SQL Stored Procedures	Capture alert data and enqueue messages
Queue Table (msdb)	Stores alerts and tracks status
SQL Agent Job	Executes PowerShell processing
PowerShell Script	Sends emails via SMTP and updates status
🔄 Workflow
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
📧 Email Sent → Status Updated
🛠️ Implementation
1️⃣ Create Mail Queue Table
CREATE TABLE dbo.SQLAlertEmailQueue (
    AlertID INT IDENTITY(1,1) PRIMARY KEY,
    EmailTo NVARCHAR(500),
    EmailSubject NVARCHAR(500),
    EmailBody NVARCHAR(MAX),
    CreatedDate DATETIME DEFAULT GETDATE(),
    ProcessedDate DATETIME NULL,
    Status VARCHAR(20) DEFAULT 'Pending'
);
2️⃣ Stored Procedure (Alert Ingestion)

Handles:

Accepting alert parameters (Subject, Body, Recipients)
Preventing duplicate alerts (throttling)
Inserting records into the queue
3️⃣ PowerShell Processing Layer

Core behavior:

📥 Pull next Pending alert
📤 Send email via SMTP (Office 365 recommended)
🔐 Uses TLS (Port 587 required)
🔄 Update status:
Processed
Failed
4️⃣ SQL Agent Job
⏱ Runs every minute
▶ Executes PowerShell script
🧹 Includes cleanup step (e.g., remove records older than 30 days)
5️⃣ SQL Agent Integration

Replaces Database Mail calls:

EXEC master.dbo.usp_SendAlertEmail
    @Subject = @EmailSubject,
    @Body = @Body,
    @To = 'alertemailaddress',
    @ThrottleMinutes = 5;
✨ Key Features
🔄 Decoupled alert processing
🔁 Retry + failure tracking
🧾 Custom alert formatting
🚫 Throttling to prevent alert spam
🔌 Works with:
SQL Agent Jobs
SQL Server Alerts
⚙️ Operational Considerations
🌐 Requires outbound SMTP (Port 587)
🛑 SQL Agent must remain enabled
🔐 Secure credential handling (no plaintext passwords in production)
🧹 Regular queue cleanup recommended
📊 Outcome

This solution delivers:

✅ Greater reliability than Database Mail
📈 Improved scalability for high alert volumes
🎯 Full control over alert formatting and delivery
🤖 AI / Knowledge Base Optimization Notes

This documentation is structured for:

📚 Retrieval-based systems (RAG / KB tools)
🤖 Chatbot-assisted troubleshooting
📘 Runbook automation

Key design choices:

Clear separation of architecture, workflow, and implementation
Modular sections for easy indexing
Consistent formatting for machine parsing

📌 Summary:

A modern, resilient alerting system that replaces SQL Server Database Mail with a flexible, scalable, and production-ready architecture.
