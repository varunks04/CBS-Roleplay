# Lab 03: Packet Analysis with Wireshark Deep Dive

[⬅️ Prev: Splunk Lab](./02_Lab_Splunk_Search_and_Detection_Rules.md) | [Back to Labs](./README.md) | [Next: Phishing Header Lab ➡️](./04_Lab_Phishing_Email_Header_and_Body_Analysis.md)

---

### Objective
Open a real-world malicious network packet capture (PCAP) from `malware-traffic-analysis.net`, apply advanced Wireshark display filters, extract unencrypted credentials, and reconstruct an attacker's interactive reverse shell session.

---

### Step-by-Step Lab Instructions

#### Step 1: Open PCAP and Establish Protocol Baseline
1. Open Wireshark and load `sample-malware-traffic.pcap`.
2. Navigate to **Statistics ➔ Protocol Hierarchy**:
   - Observe percentage breakdown of protocols (Ethernet, IPv4, TCP, UDP, TLS, HTTP, DNS).
3. Navigate to **Statistics ➔ Conversations**:
   - Inspect the **IPv4** tab: Which IP pair exchanged the largest volume of bytes?
   - Note the top conversation: Host `10.0.0.15` communicated heavily with external IP `185.220.101.5` over port 4444.

#### Step 2: Apply Display Filters
In the filter bar, enter:
```text
tcp.port == 4444
```
Observe standard TCP 3-way handshake packets (SYN, SYN-ACK, ACK), followed by continuous PSH-ACK packets containing data payloads.

#### Step 3: Follow TCP Stream to Reconstruct Reverse Shell
1. Right-click the first data packet in the conversation.
2. Select **Follow ➔ TCP Stream**.
3. A transcript window appears showing the raw commands executed:
   - Attacker: `whoami`
   - Target Host: `nt authority\system`
   - Attacker: `net user hacker Password123! /add`
   - Attacker: `net localgroup administrators hacker /add`
4. **Conclusion**: Host was completely compromised with a root shell and a backdoor local administrative account was established.
