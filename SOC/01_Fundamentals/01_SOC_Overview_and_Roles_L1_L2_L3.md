# 01. SOC Overview and Roles: L1, L2, and L3

[⬅️ Back to Fundamentals](./README.md) | [Next: Threat Landscape Basics ➡️](./02_Cybersecurity_and_Threat_Landscape_Basics.md)

---

### 🎓 Beginner's Compass: Decoded Acronyms & Terms
- **Tier 1 (Triage Analyst)**: The frontline defenders who monitor the alert queue, inspect initial evidence, filter false positives, and escalate real threats.
- **Tier 2 (Incident Responder)**: Deep-dive investigators who take escalated tickets from L1, isolate compromised machines, perform memory forensics, and eradicate malware.
- **Tier 3 (Threat Hunter / DFIR Specialist)**: Senior experts who proactively search the network for stealthy hackers who bypassed detection rules, reverse-engineer malware, and build new detections.
- **SLA (Service Level Agreement)**: The legally binding clock that dictates how fast an alert must be acknowledged (e.g., *"Critical alerts must be triaged within 15 minutes"*).
- **Shift Handover (Follow-the-Sun)**: The formal transition meeting between outgoing and incoming SOC shifts to transfer active investigations without dropping context.

---

### 1. Plain-English Walkthrough: The 3 Tiers of Defense

Imagine a high-security international airport security team:
- **Tier 1 (The TSA Baggage Screeners)**: They watch thousands of luggage bags pass through the X-ray monitor every hour. 99% of bags are harmless toiletries. When a bag shows what looks like a weapon or explosive, the L1 analyst doesn't try to dismantle it—they pause the conveyor belt and immediately call the senior supervisor!
- **Tier 2 (The Police & Bomb Squad)**: They arrive at the terminal, escort the passenger to a private interrogation room, open the bag, examine the device, and neutralize the threat.
- **Tier 3 (The Federal Counter-Intelligence Agents)**: They don't wait for luggage alarms. They analyze intelligence reports, track terrorist travel patterns, hunt for covert sleeper cells operating inside the airport, and inspect security blueprints to patch blind spots.

```mermaid
flowchart TD
    Queue["Incoming Security Alerts (SIEM / EDR / Email Gateway)<br/>(1,000+ alerts per day)"] --> L1["Tier 1 Analyst (Triage & Validation)<br/>• Time per alert: 5 - 15 mins<br/>• Filters Benign True / False Positives<br/>• Gathers initial host & user context"]
    
    L1 -->|False Positive| Close["Close Ticket with Reason Code<br/>(e.g., FP-Benign-Admin-Activity)"]
    L1 -->|Verified Threat (True Positive)| L2["Tier 2 Analyst (Investigation & Containment)<br/>• Time per ticket: 1 - 4 hours<br/>• Isolates host via EDR, resets credentials<br/>• Performs scope analysis & malware detonation"]
    
    L2 -->|Major Incident / Advanced APT| L3["Tier 3 Analyst (Hunting & Forensics)<br/>• Proactive Threat Hunting (Hypothesis-driven)<br/>• Memory & Disk Forensics (EnCase, Volatility)<br/>• Detection Engineering & Sigma rules"]
    
    L1 & L2 & L3 --> Mgr["SOC Manager / Director<br/>• SLA monitoring, incident reporting to CISO<br/>• Tooling budgets, vendor reviews, training"]
```

---

### 2. Responsibilities by Tier

| Feature | Tier 1 (Triage Analyst) | Tier 2 (Incident Responder) | Tier 3 (Threat Hunter / DFIR) |
| :--- | :--- | :--- | :--- |
| **Typical Experience** | 0 – 2 years (Entry level) | 2 – 5 years (Mid-level) | 5+ years (Senior specialist) |
| **Primary Goal** | Alert triage, verification, and rapid queue processing | Deep investigation, root-cause analysis, and containment | Proactive threat hunting, forensic analysis, rule creation |
| **Average Ticket Time** | 5 – 15 minutes per alert | 1 – 4 hours per incident | Multi-day hunts and research |
| **Primary Tools** | SIEM queue (Sentinel, Splunk), VirusTotal, EDR console | EDR Live Response, Wireshark, Sandbox (Any.Run), PowerShell | Memory analyzers (Volatility), Ghidra, KQL/SPL hunting |
| **Escalates To** | Tier 2 | Tier 3 / IR Lead | CISO / Executive Leadership |

---

### 3. A Day in the Life of an L1 SOC Analyst

Here is what your actual workday looks like as an entry-level analyst:
1. **08:00 - Shift Handover**: You arrive and review the pass-down notes from the night-shift analyst: *"Host WIN-PROD-04 was isolated at 04:00 AM due to suspected Cobalt Strike beaconing; Tier 2 is currently analyzing the memory dump."*
2. **08:15 - Queue Monitoring**: You log into Microsoft Sentinel or Splunk. You see 12 pending alerts categorized as High, Medium, and Low.
3. **08:30 - Triage Investigation**: Alert #1042 fires: *"Suspicious PowerShell Execution on LAPTOP-FINANCE-02"*.
   * You click into the EDR timeline.
   * You see `powershell.exe -enc aWV4IChOZXctT2JqZWN0...`.
   * You paste the Base64 string into **CyberChef** and decode it: it attempts to download a malicious `.exe` from an untrusted Russian IP address!
4. **08:45 - Rapid Action & Escalation**:
   * You isolate the laptop using the EDR console with one click.
   * You open the ticketing system (ServiceNow), document the decoded command and attacker IP, assign the ticket to Tier 2, and page the on-duty Incident Responder.
5. **09:00 - Processing the Next Alert**: You pick up Alert #1043: *"Failed login attempts for user David"*. You verify it was David mistyping his password before coffee. You document it as a False Positive and close the ticket.

---

### 4. Critical SOC Metrics (KPIs)

Management evaluates SOC performance based on three operational numbers:
1. **MTTD (Mean Time to Detect)**: How long elapsed between the hacker executing code and your SIEM sounding the alarm? (Target: Under 15 minutes).
2. **MTTA (Mean Time to Acknowledge)**: How fast does an L1 analyst pick up an alert once it appears in the queue? (Target: Under 5 minutes).
3. **MTTR (Mean Time to Respond / Contain)**: How fast does the team neutralize the threat (e.g., isolating the host or revoking credentials)? (Target: Under 60 minutes for high-severity threats).

---

### 5. Recommended Next Steps
Understand who you are defending the organization against and the modern threat landscape:
- [02. Cybersecurity and Threat Landscape Basics ➡️](./02_Cybersecurity_and_Threat_Landscape_Basics.md)
