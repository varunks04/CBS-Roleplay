# 05. OSINT and Investigation Tools: VirusTotal, AbuseIPDB, URLScan

[⬅️ Prev: Wireshark](./04_Wireshark_and_Network_Forensics.md) | [Back to Tools](./README.md) | [Next: Workflows & Processes Track ➡️](../05_Workflows_and_Processes/README.md)

---

### 1. The Role of OSINT in Triage

During alert triage, an analyst frequently discovers unknown artifacts (an external IP, an unfamiliar domain, or a file hash). Open Source Intelligence (OSINT) tools provide external reputation and context within seconds.

---

### 2. Essential Analyst Investigation Tools

- **VirusTotal (`virustotal.com`)**: Scans hashes, domains, and IPs against 70+ AV and threat engines.
  - *Caution*: Never upload proprietary corporate files or documents to public VirusTotal! Attackers monitor public VT feeds. Search by **SHA256 hash** only.
- **AbuseIPDB (`abuseipdb.com`)**: Community-driven database tracking reports of malicious scanning, DDoS, and brute-force IPs.
- **URLScan.io (`urlscan.io`)**: Free service that detonates and screenshots websites safely in a sandboxed browser, capturing DOM scripts, outbound requests, and TLS certificates without endangering your workstation.
- **CyberChef (`gchq.github.io/CyberChef`)**: The "Swiss Army Knife" for decoding Base64, URL encoding, XOR obfuscation, and carving hex strings.
