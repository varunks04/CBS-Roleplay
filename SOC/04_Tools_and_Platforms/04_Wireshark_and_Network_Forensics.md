# 04. Wireshark and Network Packet Forensics Deep Dive

[⬅️ Prev: CrowdStrike & Defender](./03_CrowdStrike_Falcon_and_Defender_for_Endpoint.md) | [Back to Tools](./README.md) | [Next: OSINT Tools ➡️](./05_OSINT_and_Investigation_Tools_VirusTotal_AbuseIPDB.md)

---

### 1. Platform Overview & The Role of PCAP

**Wireshark** is the world's most widely used network packet analyzer. While firewall logs state that a connection occurred between IP A and IP B on Port 443, **Full Packet Capture (PCAP)** records the microscopic raw byte payload.

---

### 2. Essential Wireshark Display Filters for Incident Response

| Analysis Goal | Wireshark Display Filter Syntax | What It Reveals |
| :--- | :--- | :--- |
| **Filter by Host IP** | `ip.addr == 192.168.1.45` | Shows all packets sent to or from the suspect host. |
| **Inspect Unencrypted Web Traffic** | `http.request or http.response` | Reveals plain-text URLs, User-Agents, and HTTP POST parameters. |
| **Detect Plaintext Credential Leaks** | `http.request.method == "POST" and (http contains "password" or http contains "passwd")` | Catches credentials submitted over unencrypted HTTP. |
| **Isolate DNS Query Activity** | `dns.flags.response == 0` | Reveals all domain lookups initiated by infected hosts. |
| **Isolate Suspicious External Ports**| `tcp.port in {4444, 1337, 8080, 9001}` | Standard Metasploit, Netcat, and C2 listener ports. |
| **Find SYN Flood / Port Scans** | `tcp.flags.syn == 1 and tcp.flags.ack == 0` | Half-open SYN packets used by Nmap and port scanners. |
| **Extract TLS SNI (Server Names)**| `tls.handshake.extensions_server_name` | Shows which domain was visited inside encrypted HTTPS traffic! |

---

### 3. Step-by-Step Forensic Workflow: Carving Dropped Files from PCAP

When malware is downloaded over unencrypted HTTP, an analyst can carve the exact executable file out of the packet capture:

1. Open the PCAP file in Wireshark (`File ➔ Open`).
2. Navigate to **File ➔ Export Objects ➔ HTTP...**
3. Wireshark inspects all reassembled HTTP streams and displays a list of transferred files (e.g., `update.exe`, `invoice.pdf`).
4. Click on the suspicious file and select **Save**.
5. Generate the SHA-256 hash in PowerShell:
   ```powershell
   Get-FileHash -Algorithm SHA256 .\update.exe
   ```
6. Search the resulting hash in VirusTotal to identify malware family attribution.
