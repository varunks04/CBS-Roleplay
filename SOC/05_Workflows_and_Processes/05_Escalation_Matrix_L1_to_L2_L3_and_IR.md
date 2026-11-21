# 05. Escalation Matrix: L1 to L2, L3, and Incident Response

[⬅️ Prev: Containment and Recovery](./04_Incident_Containment_Eradication_and_Recovery.md) | [Back to Workflows](./README.md) | [Next: Post-Incident Review ➡️](./06_Post_Incident_Review_and_Documentation.md)

---

### 🎓 Beginner's Compass: Decoded Acronyms & Terms
- **Escalation**: Formally transferring ownership of an incident from a junior tier to a senior tier or specialized external team.
- **Sev-1 (Severity 1 / P1)**: A critical enterprise emergency causing active data breach or major service outage.
- **Bridge Call**: A 24/7 emergency telephone conference where technical leads, managers, and executives coordinate incident response in real time.
- **RACI Matrix**: Defines who is **R**esponsible, **A**ccountable, **C**onsulted, and **I**nformed for every phase of operations.

---

### 1. Plain-English Walkthrough: The 911 Emergency Handoff

In a hospital emergency department, a triage nurse does not perform open-heart surgery:
- When a patient arrives holding their chest in cardiac arrest, the triage nurse doesn't say: *"Let me read a medical textbook for 3 hours."*
- **The nurse immediately sounds the trauma alarm** and transfers the patient to the surgical trauma team!
- In the SOC, an L1 analyst must escalate instantly when severe attack patterns appear.

```mermaid
flowchart TD
    L1["Tier 1 Analyst<br/>Triages alert & spots Critical Indicators"] --> Check{Does alert meet Escalation Criteria?}
    
    Check -->|Yes| L2["Tier 2 Incident Responder<br/>• Takes ownership in ticketing system<br/>• Begins deep scoping & containment"]
    Check -->|No| L1_Resolve["Tier 1 resolves locally<br/>(False Positive / Informational)"]
    
    L2 --> MajorCheck{Is it a Sev-1 Major Incident?<br/>(Ransomware / DC Compromise / C-Level Breach)}
    MajorCheck -->|Yes| WarRoom["Open Emergency Bridge Call<br/>• SOC Manager / IR Commander<br/>• Tier 3 DFIR Specialist<br/>• CISO & Legal Counsel"]
    MajorCheck -->|No| L2_Resolve["Tier 2 contains threat & closes ticket"]
```

---

### 2. Mandatory Escalation Criteria for Tier 1 Analysts

Tier 1 analysts must **immediately escalate** to Tier 2 if ANY of the following 5 conditions are met:
1. **Mimikatz / LSASS Dumping**: Any attempt to read `lsass.exe` memory or dump active credentials.
2. **Domain Controller Infiltration**: Any alert triggered on a Windows Domain Controller or critical database.
3. **Ransomware Indicators**: Detection of shadow copy deletion commands (`vssadmin delete shadows`), bulk file renames, or known ransomware hashes.
4. **Executive / C-Suite Account Compromise**: Any confirmed unauthorized login to the CEO, CFO, or General Counsel's account.
5. **C2 Beaconing**: Continuous beaconing traffic to a high-confidence malicious external threat actor IP.

---

### 3. The Professional Hand-Off Ticket Template

When escalating, never write a vague one-sentence message like *"Hey, this looks weird, check it out."*  
Use this standardized **5-Point Escalation Template**:

```markdown
### 🚨 INCIDENT ESCALATION HAND-OFF
- **Ticket ID**: INC-009482
- **Severity**: P1 - CRITICAL
- **Affected Asset**: HOST: FIN-LAPTOP-04 (IP: 10.20.4.12)
- **Affected User**: Alice Smith (Senior Director of Finance)
- **Detection Summary**: User downloaded weaponized Excel sheet via phishing email. Macro spawned hidden powershell.exe executing encoded command.
- **Initial Actions Taken by L1**:
  - Isolated host FIN-LAPTOP-04 from corporate network via EDR at 10:14 UTC.
  - Reset user password & revoked Entra ID sessions at 10:16 UTC.
  - VirusTotal score of dropped binary: 48/72 detections (Trojan.Emotet).
- **Assigned To**: Tier 2 On-Call (Bob Henderson)
```

---

### 4. Recommended Next Steps
Learn how organizations conduct post-incident reviews and document lessons learned:
- [06. Post-Incident Review and Documentation SOP ➡️](./06_Post_Incident_Review_and_Documentation.md)
