# 🗄️ SQL Server Database & Log File Migration Runbook

---

## 📖 Overview

This runbook provides **step-by-step procedures** for moving SQL Server **database and log files** to new storage locations, including both **user and system databases**.

It also includes guidance for **migrating databases between servers** using backup and restore methods.

---

## 🎯 Use Cases

- Storage reorganization  
- Disk performance optimization  
- Capacity expansion  
- Migration to new infrastructure  

---

## ✅ Prerequisites

Before starting:

- Ensure SQL Server service account has:
  - Full control over new data and log directories  
- Confirm sufficient disk space on the target location  
- Plan downtime (**database must be offline**)  

---

## 🧱 Procedure: Move User Database Files

### Step 1: Take Database Offline

```sql
ALTER DATABASE YourDatabaseName SET OFFLINE;
```

---

### Step 2: Update File Locations

```sql
ALTER DATABASE YourDatabaseName
MODIFY FILE (
    NAME = YourDatabaseName_Data,
    FILENAME = 'E:\NewLocation\YourDatabaseName_Data.mdf'
);

ALTER DATABASE YourDatabaseName
MODIFY FILE (
    NAME = YourDatabaseName_Log,
    FILENAME = 'E:\NewLocation\YourDatabaseName_Log.ldf'
);
```

---

### Step 3: Move Files at OS Level

- Manually move `.mdf` and `.ldf` files to the new location  

---

### Step 4: Bring Database Online

```sql
ALTER DATABASE YourDatabaseName SET ONLINE;
```

---

## ⚙️ Procedure: Move TempDB

### Steps

1. Stop SQL Server  
2. Update file paths:

```sql
ALTER DATABASE tempdb MODIFY FILE
(
    NAME = tempdev,
    FILENAME = 'T:\NewLocation\tempdb.mdf'
);
```

3. Restart SQL Server  

> ℹ️ TempDB files are recreated automatically on startup

---

## ⚠️ Procedure: Move Master Database

### Key Steps

1. Stop SQL Server  
2. Move master database files to new location  
3. Update SQL Server startup parameters:

```text
-dD:\NewLocation\master.mdf
-lL:\NewLocation\mastlog.ldf
```

4. Restart SQL Server  

> ⚠️ Incorrect parameter format will prevent SQL Server from starting

---

## 🔄 Procedure: Move Databases Between Servers

### Step 1: Backup Source Database

```sql
BACKUP DATABASE YourDatabase
TO DISK = 'D:\Backup\YourDatabase.bak'
WITH COMPRESSION;
```

---

### Step 2: Restore on Target Server

```sql
RESTORE DATABASE YourDatabase
FROM DISK = 'D:\Backup\YourDatabase.bak'
WITH MOVE 'YourDatabase' TO 'D:\Data\YourDatabase.mdf',
     MOVE 'YourDatabase_log' TO 'D:\Logs\YourDatabase_log.ldf',
     RECOVERY;
```

---

## ⚙️ Key Considerations

- Always verify permissions before moving files  
- Ensure correct logical file names during restore  
- Test SQL Server startup after moving system databases  
- Schedule downtime appropriately  

---

## 📊 Outcome

- Databases successfully relocated with minimal disruption  
- Improved storage utilization and performance flexibility  
- Standardized process for future migrations  

---

## 🤖 AI / Knowledge Base Optimization Notes

This runbook is structured for:

- Step-by-step procedural retrieval  
- Clear separation of database types (user vs system)  
- Automation or guided workflow integration  

---

## 📌 Summary

A practical, repeatable runbook for safely migrating SQL Server database and log files across storage or infrastructure, ensuring **consistency, reliability, and minimal downtime**.
