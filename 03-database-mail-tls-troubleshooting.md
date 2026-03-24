# 🔐 Troubleshooting SQL Server Database Mail  
## TLS & .NET Compatibility Guide

---

## 📖 Overview

This guide outlines how to diagnose and resolve **SQL Server Database Mail failures** related to **TLS and .NET Framework compatibility**.

Database Mail relies on:

- The underlying **.NET Framework**
- System-level **cryptographic (SCHANNEL) settings**

Misconfiguration can prevent email delivery, especially when modern SMTP providers require **TLS 1.2 or higher**.

---

## ⚠️ Problem

Database Mail may fail due to:

- Unsupported TLS versions  
- Outdated .NET Framework versions  
- Missing or incorrect cryptographic registry settings  

---

## 🚨 Common Symptoms

- Email send failures from SQL Server  
- SMTP connection errors (e.g., port 587 failures)  
- Silent failures with no clear SQL-side error  
- TLS handshake failures  

---

## 🧠 Root Cause

Database Mail is a **.NET-based process** and depends on:

- Installed .NET version  
- System TLS configuration  
- SCHANNEL registry settings  

If:

- TLS 1.2 is not enabled, **or**
- .NET is outdated  

➡️ Secure SMTP connections will fail.

---

## 🔍 Diagnostic Process

### 1. Verify .NET Framework Version

```cmd
reg query "HKLM\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release
```

**Example Output:**

```
0x80eb1 → .NET Framework 4.8
```

> ⚠️ Versions below 4.8 should be upgraded

---

### 2. Check Supported TLS Protocols

```powershell
[Net.ServicePointManager]::SecurityProtocol
```

**Expected Output:**

```
Tls12
```

---

### 3. Validate TLS 1.2 Registry Settings

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client"
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server"
```

**Expected Values:**

- `Enabled = 1`  
- `DisabledByDefault = 0`  

---

### 4. Verify .NET Cryptography Settings

```cmd
reg query "HKLM\SOFTWARE\Microsoft\.NETFramework\v4.0.30319" /v SchUseStrongCrypto
reg query "HKLM\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319" /v SchUseStrongCrypto
```

**Expected Value:**

- `SchUseStrongCrypto = 1`

---

## 🛠️ Resolution Steps

### Enable Strong Crypto for .NET

```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319" `
 -Name "SchUseStrongCrypto" -Value 1 -PropertyType DWord -Force
```

---

### Enable TLS 1.2 (Client)

```powershell
New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client" `
 -Name "Enabled" -Value 1 -PropertyType DWord -Force
```

---

### Enable TLS 1.2 (Server)

```powershell
New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" `
 -Name "Enabled" -Value 1 -PropertyType DWord -Force
```

---

## 🔄 Restart Requirement

> ⚠️ A system reboot is required for registry changes to take effect.

---

## ⚙️ Key Considerations

- TLS 1.2 is required for modern SMTP providers (e.g., Office 365)  
- Registry misconfigurations are a **common root cause**  
- Always validate both:
  - 32-bit (.NETFramework)  
  - 64-bit (WOW6432Node) settings  
- Changes impact **system-wide cryptographic behavior**  

---

## 📊 Outcome

After applying the correct TLS and .NET configurations:

- Database Mail successfully establishes secure SMTP connections  
- Email delivery resumes  
- No SQL-level changes are required  

---

## 🤖 AI / Knowledge Base Optimization Notes

This document is structured for:

- Step-by-step chatbot troubleshooting flows  
- Easy command retrieval and reuse  
- Clear mapping of:
  - Symptoms → Cause → Resolution  

---

## 📌 Summary

A practical troubleshooting guide for resolving SQL Server Database Mail failures caused by **TLS and .NET misconfiguration**, restoring reliable and secure email delivery.
