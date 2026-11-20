# 01. SOC Technical Interview Questions and Model Answers

[⬅️ Back to Interview Prep](./README.md) | [Next: Scenario Questions ➡️](./02_SOC_Scenario_Based_Investigation_Questions.md)

---

### Q1: Walk me through what happens when you type `https://www.google.com` into a browser from a network perspective.
**Model Answer**:
1. **Local DNS Cache / Hosts File**: Browser checks its internal DNS cache and OS hosts file.
2. **DNS Resolution**: If not cached, OS sends a UDP DNS query on port 53 to the local DNS resolver. The resolver recursively queries Root, TLD (.com), and Authoritative nameservers until the IP address is returned.
3. **ARP Resolution**: To send the packet, OS uses Address Resolution Protocol (ARP) to find the MAC address of the default gateway.
4. **TCP 3-Way Handshake**: Client initiates a TCP connection to the destination IP on port 443 via SYN ➔ SYN-ACK ➔ ACK.
5. **TLS Handshake**: Client and server negotiate cipher suites, the server presents its X.509 certificate, the client validates the digital signature against trusted Certificate Authorities (CAs), and a symmetric session key is generated.
6. **Encrypted HTTP Request**: Client sends `GET / HTTP/1.1` over TLS.
7. **Server Response**: Server returns `HTTP 200 OK` with HTML/CSS data.

---

### Q2: What is the difference between Windows Event ID 4624 and 4625? What is a Logon Type 3 vs Logon Type 10?
**Model Answer**:
- **Event ID 4624**: Successful account logon.
- **Event ID 4625**: Failed account logon (critical for spotting brute-force or password spraying).
- **Logon Type 3 (Network Logon)**: Accessing a resource over the network without interactive desktop display (e.g., connecting to an SMB file share, RPC call, or lateral movement with PsExec).
- **Logon Type 10 (RemoteInteractive)**: Remote Desktop Protocol (RDP) connection. If a regular workstation sees an external inbound Logon Type 10, it indicates an administrator or attacker connecting via RDP.

---

### Q3: How do you handle an alert if you suspect a True Positive malware infection?
**Model Answer**:
Follow the standard Incident Response framework:
1. **Identification**: Verify the alert, inspect process command line, hash reputation, and parent-child hierarchy.
2. **Containment**: Immediately isolate the infected endpoint via EDR to prevent lateral movement. Invalidate user tokens and reset passwords.
3. **Investigation & Scoping**: Search the SIEM across the entire enterprise to check if other endpoints contacted the same C2 IP or downloaded the same hash.
4. **Eradication**: Kill active malicious processes, delete persistence registry keys and scheduled tasks, and remove backdoors.
5. **Recovery**: If malware was complex, re-image the machine from a golden image, reconnect to network, and place under heightened monitoring.
6. **Lessons Learned**: Document the incident report, update SIEM detection rules, and block IOCs on perimeter firewalls.
