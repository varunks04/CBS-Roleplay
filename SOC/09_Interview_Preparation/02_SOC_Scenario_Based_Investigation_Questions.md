# 02. SOC Scenario-Based Investigation Questions

[⬅️ Prev: Technical Questions](./01_SOC_Technical_Interview_Questions.md) | [Back to Interview Prep](./README.md)

---

### Scenario 1: The Midnight PowerShell Alert
**Interviewer Prompt**: *"It is 1:30 AM on a Saturday. An alert fires: `powershell.exe -enc ...` on the machine of an HR recruiter. What do you do in the next 15 minutes?"*

**Model Strategy**:
1. **Decode Command**: Copy the Base64 string into CyberChef immediately to reveal the plain-text script.
2. **Inspect Process Lineage**: In the EDR, check what spawned PowerShell. If spawned by `winword.exe` or `outlook.exe`, it is almost certainly a malicious macro document.
3. **Isolate Host**: Immediately click **Isolate Host** in the EDR console to cut off network access before the payload connects out.
4. **Check Outbound Connections**: Inspect firewall/EDR network telemetry to see if PowerShell successfully established a connection to an external IP.
5. **Escalate**: Document all findings in the ticketing system and escalate to Tier 2 with host details, decoded script, and isolated status.

---

### Scenario 2: The CFO Login from Russia
**Interviewer Prompt**: *"You see a successful login for the company CFO originating from an IP in Moscow, Russia. The CFO lives in Chicago. How do you investigate?"*

**Model Strategy**:
1. **Out-of-band Verification**: Call the CFO on their verified corporate mobile phone (do NOT email them, as their email may be compromised) or check with the executive protection team to see if they are traveling.
2. **Analyze Sign-in Telemetry**:
   - Check device compliance: Was the login from a corporate MDM-managed laptop or an unknown Linux/Tor browser?
   - Check MFA method: Did it use FIDO2 or did it trigger push notifications?
3. **Check for Session Hijacking (AiTM)**: Check if the login used a stolen session cookie.
4. **Immediate Containment**: If the CFO confirms they are in Chicago, immediately fire `Revoke-MgUserSignInsSession`, disable the account, and initiate an urgent Sev-1 incident investigation.
