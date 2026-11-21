# 01. SIEM Architecture and Data Ingestion

[⬅️ Back to Technologies](./README.md) | [Next: EDR and XDR Mechanisms ➡️](./02_EDR_and_XDR_Mechanisms.md)

---

### 🎓 Beginner's Compass: Decoded Acronyms & Terms
- **SIEM (Security Information and Event Management)**: The central data repository that aggregates, correlates, analyzes, and retains logs from every computer, server, and firewall across the entire company.
- **Forwarder / Agent**: A lightweight software client installed on servers that collects local logs and ships them to the SIEM (e.g., Splunk Universal Forwarder, Azure Monitor Agent).
- **Correlation Rule**: An automated mathematical query running every 5 minutes that searches for patterns across different log sources to trigger alerts.
- **Storage Tiers (Hot / Warm / Cold)**:
  - **Hot**: Blazing fast, expensive SSD storage for instant searches (last 30–90 days).
  - **Warm**: Slower, compressed storage for historical investigations (90–365 days).
  - **Cold**: Ultra-cheap archive storage (e.g., AWS S3 Glacier) for legal compliance (1–7 years).
- **Ingestion Volume (GB/day or EPS)**: The speed at which logs enter the SIEM, measured in Gigabytes per day or **Events Per Second (EPS)**.

---

### 1. Plain-English Walkthrough: The Airport Air Traffic Control Tower

Imagine an international airport with 500 airplanes flying overhead, 100 baggage carts moving on the tarmac, and 50 fueling trucks:
- If each vehicle operated independently without talking to anyone, planes would collide on runways constantly!
- The **Air Traffic Control Tower** is the **SIEM**:
  - Every pilot transmits their radar altitude, speed, and heading to the tower.
  - The tower's computers correlate the flight paths.
  - If Plane A and Plane B are on a collision course, the alarm sounds instantly!
- In IT security, the SIEM collects firewalls, VPNs, Windows servers, and cloud databases into **one unified command screen**.

```mermaid
flowchart TD
    subgraph "1. Data Producers (The Fleet)"
        S1["Windows Domain Controllers (Security EVTX)"]
        S2["Linux Servers (Auth.log via Syslog)"]
        S3["Perimeter Firewalls (Palo Alto / Fortinet CEF)"]
        S4["Cloud Environments (AWS CloudTrail / Azure)"]
    end

    subgraph "2. Ingestion & Normalization"
        S1 & S2 & S3 & S4 --> Forwarders["Log Forwarders & Heavy Collectors<br/>(Buffering, Compression & TLS Encryption)"]
        Forwarders --> Parser["Parsing & Indexing Tier<br/>(Extracts fields, calculates timestamps & hashes)"]
    end

    subgraph "3. Storage & Analytics"
        Parser --> Storage[("Storage Tiers<br/>Hot (90d) / Warm (1y) / Cold (7y)")]
        Storage <--> Correlation["Real-Time Correlation Engine<br/>(Detects Brute Force, Token Theft, Ransomware)"]
    end

    Correlation --> Alert["SOC Analyst Incident Queue (P1/P2/P3 Alerts)"]
```

---

### 2. How Correlation Rules Work in Practice

A correlation rule combines multiple benign logs into a **single high-fidelity security alert**:

```mermaid
flowchart LR
    E1["Log 1: VPN Login from Russia (2:00 AM)"] --> Rule["SIEM Correlation Logic:<br/>IF Russian VPN (Log 1)<br/>AND Database Export (Log 2)<br/>AND Encrypted Zip Sent Out (Log 3)<br/>WITHIN 15 Minutes..."]
    E2["Log 2: User queries 500k SSNs from SQL DB"] --> Rule
    E3["Log 3: Outbound 500MB upload to Mega.nz"] --> Rule
    Rule --> Alert["🚨 CRITICAL ALERT: Immediate Data Exfiltration Confirmed!"]
```

---

### 3. Common Beginner Mistakes vs. Best Practices

| Common Beginner Mistake | Real-World Risk | Industry Best Practice |
| :--- | :--- | :--- |
| **Ingesting Everything Blindly** | Ingesting noisy debug logs (e.g., successful web server image requests) costs hundreds of thousands of dollars in SIEM license fees without providing security value. | Filter logs **at the source/forwarder level**: drop noisy, low-value events before they hit the SIEM billing meter. |
| **Ignoring Ingestion Lag** | If forwarder network buffers are jammed, logs arrive 3 hours late, meaning the SOC investigates attacks 3 hours after they already succeeded. | Configure **Ingestion Latency Health Alerts** in your SIEM to notify engineers if log arrival delay exceeds 10 minutes. |
| **Writing Broad, Unoptimized Queries** | Running wildcards (`*mimikatz*`) across 50 Terabytes of un-indexed data freezes the SIEM for other analysts. | Always scope searches by **Time Range**, **Index**, and specific fields (e.g., `index=windows EventCode=4688 Image="*mimikatz.exe"`). |

---

### 4. Recommended Next Steps
Learn how endpoint sensors watch running processes and isolate infected workstations:
- [02. EDR and XDR Mechanisms ➡️](./02_EDR_and_XDR_Mechanisms.md)
