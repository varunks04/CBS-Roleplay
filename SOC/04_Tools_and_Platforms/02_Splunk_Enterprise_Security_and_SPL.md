# 02. Splunk Enterprise Security and Search Processing Language (SPL)

[⬅️ Prev: Microsoft Sentinel](./01_Microsoft_Sentinel_and_KQL.md) | [Back to Tools](./README.md) | [Next: CrowdStrike & Defender ➡️](./03_CrowdStrike_Falcon_and_Defender_for_Endpoint.md)

---

### 1. Platform Overview

**Splunk Enterprise Security (ES)** is the pioneer and market-leading on-premises and cloud SIEM platform used across Fortune 500 security operations centers.

---

### 2. Search Processing Language (SPL) Mastery

SPL uses Unix-style pipes to transform raw log indexes into structured alerts and charts:

```spl
# 1. Detect Potential Lateral Movement via SMB / PsExec
index=windows EventCode=7045 Service_Name="PSEXESVC"
| stats count earliest(_time) as first_seen latest(_time) as last_seen by host, Service_File_Name, user
| convert ctime(first_seen) ctime(last_seen)

# 2. Top 10 Talkers: Highest Data Volume Exfiltration Candidates
index=firewall action=allowed
| stats sum(bytes_out) as TotalBytes by src_ip, dest_ip, dest_port
| eval TotalMB = round(TotalBytes / 1024 / 1024, 2)
| sort - TotalMB
| head 10

# 3. Rare Process Execution on Critical Servers
index=endpoint sourcetype=sysmon EventCode=1 host="PROD-DB-*"
| rare limit=20 Image by host
```
