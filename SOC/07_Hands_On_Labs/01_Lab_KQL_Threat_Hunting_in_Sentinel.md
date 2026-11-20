# Lab 01: KQL Threat Hunting in Microsoft Sentinel

[⬅️ Back to Labs](./README.md) | [Next: Splunk Detection Lab ➡️](./02_Lab_Splunk_Search_and_Detection_Rules.md)

---

### Objective
Learn how to use Kusto Query Language (KQL) in the free Azure Log Analytics demo environment to hunt for real-world cyber attack patterns.

### Lab Setup
1. Navigate to the free public demo: `https://aka.ms/lademo`.
2. This connects you to a live Microsoft telemetry workspace populated with enterprise Windows, Linux, and Cloud logs.

### Lab Exercises

#### Exercise 1: Hunt for Suspicious Process Execution
Find any process executing from a user's `AppData\Local\Temp` directory:
```kql
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4688
| where Process has @"AppData\Local\Temp"
| project TimeGenerated, Computer, SubjectUserName, NewProcessName, CommandLine
| take 50
```

#### Exercise 2: Detect Brute Force followed by Success
```kql
let FailedLogins = SecurityEvent
| where TimeGenerated > ago(24h)
| where EventID == 4625
| summarize FailCount = count() by TargetUserName, IpAddress, bin(TimeGenerated, 15m)
| where FailCount >= 5;
let SuccessLogins = SecurityEvent
| where TimeGenerated > ago(24h)
| where EventID == 4624
| project SuccessTime = TimeGenerated, TargetUserName, IpAddress;
FailedLogins
| join kind=inner (SuccessLogins) on TargetUserName, IpAddress
| where SuccessTime > TimeGenerated and SuccessTime < TimeGenerated + 30m
| project TargetUserName, IpAddress, FailCount, AttackTime = TimeGenerated, SuccessTime
```
