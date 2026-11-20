# 05. OSINT and Investigation Tools: VirusTotal, AbuseIPDB, URLScan

[⬅️ Prev: Wireshark](./04_Wireshark_and_Network_Forensics.md) | [Back to Tools](./README.md) | [Next: Workflows Track ➡️](../05_Workflows_and_Processes/README.md)

---

### 1. Platform Overview & The OSINT Triage Mindset

During live alert triage, a SOC analyst constantly encounters unfamiliar artifacts: an unknown IP address in firewall logs, an external domain in web proxy telemetry, or a suspicious file hash in an EDR alert.

**Open Source Intelligence (OSINT)** tools provide crowdsourced reputation, historical telemetry, and malware attribution within seconds.

---

### 2. Core OSINT Investigation Toolset

#### A. VirusTotal (`virustotal.com`)
- **What it does**: Aggregates 70+ antivirus engines and sandbox tools to analyze suspicious files, hashes, URLs, and domains.
- **Critical Operational Caution**: **NEVER upload raw corporate files or internal documents to VirusTotal!** Public VirusTotal allows subscribers to download submitted files. If you upload an internal company PDF or source code, competitors and attackers can download it. **Search by SHA-256 hash ONLY.**

#### B. AbuseIPDB (`abuseipdb.com`)
- **What it does**: Community-driven threat intelligence database tracking malicious IP addresses reported for SSH brute-forcing, port scanning, web attacks, and spam.
- **Triage Benchmark**: An AbuseIPDB confidence score $> 50\%$ with recent reports within the last 48 hours confirms known malicious external reconnaissance.

#### C. URLScan.io (`urlscan.io`)
- **What it does**: Free web sandbox that navigates to a URL, takes high-resolution screenshots, captures DOM resources, records outbound HTTP/HTTPS requests, and logs TLS certificates without putting your workstation at risk.
- **Great for**: Analyzing phishing landing pages that imitate Microsoft 365 or Google login portals safely.

#### D. CyberChef (`gchq.github.io/CyberChef`)
- **What it does**: The "Swiss Army Knife" for forensic analysts, created by GCHQ.
- **Essential Recipes**:
  - `From Base64` + `Decode text (UTF-16LE)`: Decodes obfuscated PowerShell command lines.
  - `URL Decode`: Decodes percent-encoded HTTP parameters.
  - `Defang URL`: Converts `http://evil.com/malware.exe` to `hxxp[://]evil[.]com/malware[.]exe` to safely paste into incident tickets without accidental clicking.
