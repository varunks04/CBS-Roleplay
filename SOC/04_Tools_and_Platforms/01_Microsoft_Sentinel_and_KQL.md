# 01. Microsoft Sentinel and Kusto Query Language (KQL)

[⬅️ Back to Tools](./README.md) | [Next: Splunk Enterprise Security ➡️](./02_Splunk_Enterprise_Security_and_SPL.md)

---

### 1. Platform Overview

**Microsoft Sentinel** is a scalable, cloud-native Security Information and Event Management (SIEM) and Security Orchestration, Automation, and Response (SOAR) platform built inside Microsoft Azure.

---

### 2. Kusto Query Language (KQL) Mastery for Analysts

KQL is the query language used to search, filter, and analyze data in Microsoft Sentinel and Defender. Queries flow from left to right using the pipe (`|`) operator.

#### Essential KQL Hunting Queries:

```kql
// 1. Detect Suspicious PowerShell Execution with Encoded Commands
SecurityEvent
| where TimeGenerated > ago(24h)
| where EventID == 4688
| where Process has_any ("powershell.exe", "pwsh.exe")
| where CommandLine has_any ("-enc", "-encodedcommand", "downloadstring", "invoke-expression")
| project TimeGenerated, Computer, Account, ParentProcessName, CommandLine
| order by TimeGenerated desc

// 2. Identify Potential Brute-Force Password Spraying
SigninLogs
| where TimeGenerated > ago(4h)
| where ResultType != 0 // Non-zero indicates failed sign-in
| summarize FailedCount = count() by IPAddress, Location, UserPrincipalName
| where FailedCount > 25
| order by FailedCount desc

// 3. Find Outbound Network Connections to Known Malicious IPs from Threat Intel
CommonSecurityLog
| where TimeGenerated > ago(12h)
| where DeviceVendor == "Palo Alto Networks"
| join kind=inner (
    ThreatIntelligenceIndicator
    | where ExpirationDateTime > now()
    | project NetworkIP = ThreatIntelligenceIndicatorIP
) on $left.DestinationIP == $right.NetworkIP
| project TimeGenerated, SourceIP, DestinationIP, DeviceAction, NetworkIP
```
