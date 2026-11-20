# 05. Escalation Matrix: L1 to L2, L3, and Incident Response SOP

[⬅️ Prev: Containment & Recovery](./04_Incident_Containment_Eradication_and_Recovery.md) | [Back to Workflows](./README.md) | [Next: Post-Incident Review ➡️](./06_Post_Incident_Review_and_Documentation.md)

---

### 1. Incident Severity Definitions & SLA Matrix

| Severity Level | Definition | Maximum Triage SLA (L1) | Containment Target (L2) | Escalation Chain |
| :--- | :--- | :--- | :--- | :--- |
| **P1 - Critical** | Active enterprise-wide ransomware, domain controller compromise, confirmed large-scale customer data exfiltration. | $\le 10$ minutes | $\le 45$ minutes | L1 ➔ L2 ➔ Incident Commander ➔ CISO ➔ Executive Committee |
| **P2 - High** | Single compromised workstation with malware beaconing to C2, successful phishing with credential theft, unauthorized admin elevation. | $\le 20$ minutes | $\le 2$ hours | L1 ➔ L2 Responder ➔ SOC Manager |
| **P3 - Medium** | Multiple failed brute-force attempts from single IP, commodity malware blocked by antivirus, suspicious policy violation. | $\le 45$ minutes | $\le 8$ hours | Handled within L1 / L2 queue |
| **P4 - Low / Info**| Port scans from external internet, benign policy violations, single failed login. | $\le 4$ hours | Next Business Day | Handled by L1 / Automated SOAR |

---

### 2. Tier Handover Protocol

When an L1 analyst escalates a ticket to L2, they must **NOT** simply reassign the ticket with no context. They must include the standard **SBAR Handover Format**:
- **S (Situation)**: What alert fired, when, and on what host?
- **B (Background)**: Who owns the host? Is there recent context or authorized testing?
- **A (Assessment)**: What did you verify? What OSINT reputation was discovered? Why is this a true positive?
- **R (Recommendation)**: What immediate actions should L2 take (e.g., host isolation, memory capture)?
