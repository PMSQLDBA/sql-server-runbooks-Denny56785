# 🔍 SQL Server Deadlock Analysis Query
## Extended Events-Based Visibility Approach

---

## 📖 Overview

This document provides a query used to analyze SQL Server deadlocks using Extended Events (`system_health`).

It was developed to provide **visibility into deadlock frequency and patterns** before implementing automated monitoring.

---

## ⚠️ Problem

SQL Server does not provide:

- Historical deadlock counts out of the box  
- Easy visibility into deadlock frequency over time  
- Per-database breakdown without manual parsing  

---

### Real-World Impact

In the environment this was developed for:

- Over 2,000 deadlocks were occurring daily  
- Application errors and job failures were frequent  
- Operational teams were overwhelmed with incident tickets  

Deadlocks were difficult to quantify, making it hard to prioritize fixes or measure improvement.

---

## 💡 Approach

Deadlock events are extracted from Extended Events and parsed from XML.

### Key Capabilities

- Query deadlocks across a defined timeframe  
- Convert UTC timestamps to local time  
- Identify victim processes  
- Aggregate counts by database  

---

## 🔧 Query

```sql
DECLARE @StartTime DATETIME = CAST(GETDATE() AS DATE);
DECLARE @EndTime   DATETIME = DATEADD(DAY, 1, CAST(GETDATE() AS DATE));

DECLARE @RunDateTime VARCHAR(16) =
    CONVERT(CHAR(10), GETDATE(), 120) + ' ' +
    CONVERT(CHAR(5), GETDATE(), 108);

;WITH ParsedDeadlocks AS
(
    SELECT
        DATEADD(
            HOUR,
            DATEDIFF(HOUR, GETUTCDATE(), GETDATE()),
            D.event_data.value('(//event/@timestamp)[1]', 'datetime2')
        ) AS DeadlockTimeLocal,
        D.event_data
    FROM
    (
        SELECT
            TRY_CAST(event_data AS XML) AS event_data
        FROM sys.fn_xe_file_target_read_file('system_health*.xel', NULL, NULL, NULL)
        WHERE object_name = 'xml_deadlock_report'
    ) D
    WHERE
        D.event_data IS NOT NULL
        AND DATEADD(
                HOUR,
                DATEDIFF(HOUR, GETUTCDATE(), GETDATE()),
                D.event_data.value('(//event/@timestamp)[1]', 'datetime2')
            ) >= @StartTime
        AND DATEADD(
                HOUR,
                DATEDIFF(HOUR, GETUTCDATE(), GETDATE()),
                D.event_data.value('(//event/@timestamp)[1]', 'datetime2')
            ) < @EndTime
),

DeadlockVictims AS
(
    SELECT
        DB_NAME(p.process.value('(@currentdb)[1]', 'int')) AS DatabaseName,
        pd.DeadlockTimeLocal,
        v.victimProcess.value('(@id)[1]', 'varchar(100)') AS VictimID
    FROM ParsedDeadlocks pd
    CROSS APPLY pd.event_data.nodes('//victim-list/victimProcess') AS v(victimProcess)
    CROSS APPLY pd.event_data.nodes('//process-list/process')       AS p(process)
    WHERE
        v.victimProcess.value('(@id)[1]', 'varchar(100)') =
        p.process.value('(@id)[1]', 'varchar(100)')
),

MonitoredDBs AS
(
    SELECT 'Database1' AS DatabaseName
    UNION ALL SELECT 'Database2'
    UNION ALL SELECT 'Database3'
    UNION ALL SELECT 'Database4'
),

DeadlockCounts AS
(
    SELECT
        DatabaseName,
        COUNT(*) AS DeadlockCount
    FROM DeadlockVictims
    WHERE DatabaseName IS NOT NULL
    GROUP BY DatabaseName
),

Results AS
(
    -- Query run time row
    SELECT
        'Query Run Time' AS DatabaseName,
        @RunDateTime     AS DeadlockCount,
        -1               AS SortOrder

    UNION ALL

    -- Per-database rows
    SELECT
        m.DatabaseName,
        CAST(ISNULL(dc.DeadlockCount, 0) AS VARCHAR(16)) AS DeadlockCount,
        0 AS SortOrder
    FROM MonitoredDBs m
    LEFT JOIN DeadlockCounts dc
        ON m.DatabaseName = dc.DatabaseName

    UNION ALL

    -- Grand total row
    SELECT
        'GRAND TOTAL',
        CAST(SUM(ISNULL(dc.DeadlockCount, 0)) AS VARCHAR(16)),
        1
    FROM MonitoredDBs m
    LEFT JOIN DeadlockCounts dc
        ON m.DatabaseName = dc.DatabaseName
)

SELECT
    DatabaseName,
    DeadlockCount
FROM Results
ORDER BY
    SortOrder,
    DatabaseName;
```

---

## 📊 Output

The query produces:

- Query run timestamp  
- Per-database deadlock counts  
- Grand total across monitored databases  

---

## 🧠 Key Insights

- Deadlock frequency varies significantly by workload  
- Not all deadlocks are equal — context matters  
- Visibility is required before defining alert thresholds  

---

## 🔗 Relationship to Monitoring System

This query served as the foundation for:

➡️ [Deadlock Monitoring & Alerting System](./01-deadlock-monitoring-system.md)

It enabled:

- Identification of deadlock frequency and patterns  
- Definition of alerting thresholds  
- Validation of remediation efforts  

This represents the first step in the workflow:

👉 Visibility → Analysis → Monitoring → Prevention

---

## 📌 Summary

A lightweight, reusable query for analyzing deadlock activity, forming the foundation for operational monitoring and alerting strategies.

---

## 📊 Outcome

Using this analysis approach combined with targeted remediation:

- Deadlocks reduced from ~2,000/day to near zero  
- ~20 total deadlocks observed over a 3-month period  
- 0 deadlocks observed in the most recent 5-day window  
- Significant reduction in operational disruption and incident volume  

This validated the importance of establishing visibility before implementing automated monitoring.
