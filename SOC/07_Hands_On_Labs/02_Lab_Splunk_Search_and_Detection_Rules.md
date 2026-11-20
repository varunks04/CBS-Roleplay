# Lab 02: Splunk Search and Detection Rule Creation

[⬅️ Prev: KQL Hunting](./01_Lab_KQL_Threat_Hunting_in_Sentinel.md) | [Back to Labs](./README.md) | [Next: Wireshark Analysis Lab ➡️](./03_Lab_Packet_Analysis_with_Wireshark.md)

---

### Objective
In this lab, you will download and install Splunk Enterprise Free Trial, ingest sample Windows Sysmon security logs from the famous BOTS (Boss of the SOC) dataset, and craft high-fidelity SPL detection rules to catch adversary persistence.

---

### Lab Prerequisites
- Windows 10/11 or Ubuntu Linux machine.
- 4GB RAM minimum.
- Free Splunk Enterprise trial account (`splunk.com`).

---

### Step-by-Step Lab Execution

#### Step 1: Install Splunk Free Trial
1. Download Splunk Enterprise for your OS.
2. Run installer (default ports: Web GUI on TCP 8000, Splunkd on TCP 8089).
3. Open browser and navigate to `http://localhost:8000`. Log in with your admin credentials.

#### Step 2: Ingest Sample Windows Security Logs
1. Download a sample Windows Sysmon log file (JSON or EVTX).
2. In Splunk, click **Settings ➔ Add Data**.
3. Choose **Upload** ➔ Select your sample log file.
4. Set Source Type to `xmlwineventlog` or `sysmon`.
5. Create a new index named `wineventlog` and click **Submit**.

#### Step 3: Write SPL Hunting Queries

```spl
# Query 1: Detect PowerShell downloading files using WebClient
index=wineventlog EventCode=1 Image="*powershell.exe" CommandLine="*DownloadString*"
| table _time, host, user, ParentImage, CommandLine

# Query 2: Aggregate top 10 parent processes spawning command shells
index=wineventlog EventCode=1 (Image="*cmd.exe" OR Image="*powershell.exe")
| top limit=10 ParentImage

# Query 3: Detect scheduled task persistence creation (EventCode 4698)
index=wineventlog EventCode=4698
| spath input=Message
| table _time, host, SubjectUserName, TaskName
```

#### Step 4: Convert Query into an Automated Splunk Alert
1. In the search interface, run Query 1.
2. Click **Save As ➔ Alert**.
3. Title: `Adversary Technique: PowerShell WebClient Ingress Transfer (T1105)`.
4. Trigger condition: Number of Results $> 0$.
5. Severity: **High**.
6. Actions: Add to Triggered Alerts & trigger webhook notification.
