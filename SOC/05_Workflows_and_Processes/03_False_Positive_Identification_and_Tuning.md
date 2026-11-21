# 03. False Positive Identification and Tuning SOP

[⬅️ Prev: End-to-End Investigation](./02_End_to_End_Incident_Investigation_Workflow.md) | [Back to Workflows](./README.md) | [Next: Incident Containment and Recovery ➡️](./04_Incident_Containment_Eradication_and_Recovery.md)

---

### 🎓 Beginner's Compass: Decoded Acronyms & Terms
- **False Positive (FP)**: A security alert that fires on completely benign, authorized business activity (e.g., an internal vulnerability scanner triggering an intrusion alarm).
- **Rule Tuning**: Updating a SIEM correlation query or EDR policy with specific exceptions so that harmless business activity does not trigger alerts in the future.
- **Detection Engineer**: The security engineer whose job is writing, optimizing, and maintaining SIEM and EDR detection rules.

---

### 1. Plain-English Walkthrough: Fixing the Over-Sensitive Smoke Alarm

Imagine a smoke detector mounted directly above a kitchen stove that goes off every single time you boil water:
- If it beeps 20 times a day, people get annoyed and eventually remove the batteries.
- Then, when a real fire breaks out, nobody hears an alarm!
- **Tuning** means adjusting the sensor: *"Sound the alarm if temperature exceeds 150°F, but ignore light steam from boiling pasta."*
- In the SOC, tuning ensures analysts only wake up for real cyber threats.

---

### 2. The 8-Point Operational SOP

#### 1. Trigger
An analyst investigates an alert and proves beyond doubt that the activity was benign business operations.

#### 2. Responsible Parties (RACI)
* **Responsible (R)**: Tier 1 / Tier 2 Analyst (identifies and documents the FP).
* **Accountable (A)**: Detection Engineer (reviews and pushes rule modifications).
* **Consulted (C)**: Application Owner / IT SysAdmin who ran the script.
* **Informed (I)**: SOC Manager.

#### 3. Required Tools & Inputs
* **SIEM Rule Repository**: Git repository or Sentinel Analytic Rules portal.
* **Change Management System**: ServiceNow Change Request ticket.

#### 4. Step-by-Step Procedure
1. **Gather Proof of Legitimacy**:
   * Verify with the user or system administrator: Did they run this command? For what business reason?
   * Cross-reference against approved change requests (e.g., `CHG-90482`).
   * Verify the digital signature of the software binary.
2. **Draft the Exception Logic**:
   * **BAD Exception**: `where ProcessCommandLine !contains "powershell.exe"` (Too broad! Attackers will use PowerShell to bypass your rule!).
   * **GOOD Granular Exception**:
     ```kusto
     | where not (InitiatingProcessFileName == "BackupAgent.exe" 
                  and ProcessCommandLine has "backup_script.ps1" 
                  and AccountName == "svc-backup")
     ```
3. **Submit Tuning Request**: Open a detection tuning ticket for the Detection Engineering team with proposed logic.

#### 5. Decisions & Logic Gates
* **IF** the activity occurs daily across 1,000 laptops -> **Tune the rule at the SIEM query level**.
* **IF** the activity was a one-time scheduled maintenance event -> **Close ticket as Benign True Positive without rule modification**.

#### 6. Escalation Matrix
* Detection engineers must review and deploy approved tuning changes within **48 hours**.

#### 7. Expected Outcome
The alert stops firing on benign activity, lowering queue noise without introducing security blind spots.

#### 8. Documentation & Evidence Recorded
Save the user email confirmation, change management ticket number, and before/after KQL query diff.

---

### 3. Recommended Next Steps
Learn the step-by-step procedure for neutralizing confirmed threats:
- [04. Incident Containment, Eradication, and Recovery SOP ➡️](./04_Incident_Containment_Eradication_and_Recovery.md)
