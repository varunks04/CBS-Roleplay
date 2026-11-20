# 04. Wireshark and Network Packet Forensics

[⬅️ Prev: CrowdStrike & Defender](./03_CrowdStrike_Falcon_and_Defender_for_Endpoint.md) | [Back to Tools](./README.md) | [Next: OSINT Tools ➡️](./05_OSINT_and_Investigation_Tools_VirusTotal_AbuseIPDB.md)

---

### 1. Platform Overview

**Wireshark** is the world's foremost network protocol analyzer, allowing analysts to inspect raw packet captures (PCAP) down to the microscopic bit level.

---

### 2. Essential Wireshark Display Filters

| Analysis Goal | Wireshark Display Filter Syntax |
| :--- | :--- |
| **Filter by Source or Dest IP** | `ip.addr == 192.168.1.100` |
| **Filter by Specific TCP Port** | `tcp.port == 445` (SMB) or `tcp.port == 3389` (RDP) |
| **Find Plaintext HTTP GET/POST**| `http.request.method in {"GET", "POST"}` |
| **Inspect DNS Queries** | `dns.flags.response == 0` |
| **Filter Failed TCP Connections** | `tcp.flags.reset == 1` |
| **Inspect Suspicious User-Agents**| `http.user_agent contains "curl" or http.user_agent contains "python"` |

---

### 3. Practical Forensic Workflow: Follow TCP Stream
When an unencrypted HTTP or reverse shell connection is discovered:
1. Right-click the suspicious packet in Wireshark.
2. Select **Follow ➔ TCP Stream**.
3. Wireshark automatically reassembles the fragmented TCP packets into a single human-readable conversation transcript (Red text = client requests; Blue text = server responses).
