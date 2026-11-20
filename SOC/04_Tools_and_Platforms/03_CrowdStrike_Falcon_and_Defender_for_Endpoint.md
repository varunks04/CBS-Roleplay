# 03. CrowdStrike Falcon and Microsoft Defender for Endpoint

[⬅️ Prev: Splunk](./02_Splunk_Enterprise_Security_and_SPL.md) | [Back to Tools](./README.md) | [Next: Wireshark & Forensics ➡️](./04_Wireshark_and_Network_Forensics.md)

---

### 1. Platform Overview

**CrowdStrike Falcon** and **Microsoft Defender for Endpoint (MDE)** are the two industry-dominating Endpoint Detection and Response (EDR) platforms.

---

### 2. Real-Time Analyst Incident Triage Capabilities

- **Process Lineage Tree**: Visual execution tree mapping child processes, network connections, file writes, and registry updates initiated by each binary.
- **Host Isolation**: Single-click quarantine dropping all network interfaces except the telemetry connection to the EDR cloud.
- **Live Response Console**: Command-line terminal into compromised machines:
  ```powershell
  # CrowdStrike Falcon Live Response Commands
  runscript -CloudFile="Collect-Triage"  # Executes forensic script
  memdump -Target="lsass.exe"             # Dumps memory of process
  kill -PID 4912                          # Immediately terminates malicious process
  isolate-host                            # Isolates endpoint
  ```
