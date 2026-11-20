# 07. Identity Security Incident Investigation SOP

[⬅️ Prev: Privileged Access JIT](./06_Privileged_Access_Just_In_Time_JIT_Request.md) | [Back to Workflows](./README.md) | [Next: Real-World Scenarios Track ➡️](../06_Real_World_Scenarios/README.md)

---

### 1. Workflow Architecture Diagram

```mermaid
flowchart TD
    A["1. Trigger: High-Risk Alert in Entra ID Protection<br>(Impossible Travel / Leaked Credentials / MFA Spray)"] --> B["2. IAM / SOC Analyst reviews Sign-in Logs & Telemetry"]
    B --> C{"Is the Sign-in Legitimate?<br>(Verify with user out-of-band)"}
    C -->|Yes: Legitimate VPN / Travel| D["Mark as False Positive / Dismiss Alert"]
    C -->|No: Confirmed Account Takeover (ATO)| E["3. EXECUTE EMERGENCY CONTAINMENT<br>- Revoke All User Sessions (CAE)<br>- Disable Account in Directory<br>- Force Password Reset"]
    E --> F["4. Scrape & Audit Post-Compromise Actions<br>(OAuth App Consents, New MFA Methods, Mailbox Forwarding Rules)"]
    F --> G["5. Remove Attacker Persistence Mechanisms"]
    G --> H["6. Secure Re-enrollment via TAP & Restored Access"]
```

---

### 2. Standard 8-Point Operational Breakdown

#### 1. Trigger
- An automated identity risk alert (e.g., Entra ID Identity Protection alert: *Atypical Travel*, *Leaked Credentials*, or *Anomalous Token Generation*), or a user report of unauthorized MFA push notifications.

#### 2. Responsible Parties (RACI Matrix)
- **Accountable**: Incident Response Commander.
- **Responsible**: IAM Security Engineer / SOC L2 Analyst.
- **Consulted**: Compromised Employee, Employee's Manager.
- **Informed**: Legal, GRC, Helpdesk.

#### 3. Required Tools & Information
- **Tools**: Microsoft Entra ID Audit & Sign-in Logs, Microsoft Defender for Cloud Apps, Kusto Query Language (KQL) in Microsoft Sentinel, PowerShell.
- **Inputs**: User UPN, IP Addresses, User-Agent strings, Session IDs, Timestamp of alert.

#### 4. Step-by-Step Execution Procedure
1. **Initial Triage & Triage Query**:
   - Query sign-in logs for anomalous IPs and authentications:
     ```kql
     SigninLogs
     | where UserPrincipalName == "victim@company.com"
     | where CreatedDateTime > ago(24h)
     | project CreatedDateTime, IPAddress, Location, AppDisplayName, ClientAppUsed, RiskLevelDuringSignIn
     | order by CreatedDateTime desc
     ```
2. **Immediate Threat Containment**:
   - Execute session revocation to kill all active OAuth refresh tokens and CAE sessions immediately:
     ```powershell
     Revoke-MgUserSignInsSession -UserId "victim@company.com"
     ```
   - Disable the account to prevent re-authentication during investigation.
3. **Audit Attacker Persistence**:
   - Check if the attacker added a new MFA method (e.g., added an attacker-owned phone or authenticator app):
     ```powershell
     Get-MgUserAuthenticationMethod -UserId "victim@company.com"
     ```
   - Check for newly registered OAuth applications or service principal credentials.
   - Check for malicious Exchange Online inbox forwarding rules designed to exfiltrate email.
4. **Remediation & Eradication**:
   - Delete unauthorized MFA methods and remove malicious OAuth consent grants.
   - Remove any suspicious email forwarding rules.
   - Force a permanent password reset.
5. **Identity Restoration**:
   - Issue a secure, one-time Temporary Access Pass (TAP) directly to the employee via a verified out-of-band phone call.
   - Re-enable the account.
   - Guide the user through registering a new phishing-resistant FIDO2 key.

#### 5. Decision Points & Logic Gates
- **IF** the compromised account possesses administrative privileges (Global Admin, Cloud Architect), **THEN** declare a **Sev-1 Incident** and initiate full tenant-wide compromise assessment.
- **IF** token theft / AiTM is suspected, **THEN** assume passwords were NOT used; investigate session cookie extraction.

#### 6. Escalation Matrix
- **Data Exfiltration Confirmed**: Escalate to Chief Legal Officer and Privacy Officer for regulatory breach reporting (SLA: 1 hour).

#### 7. Expected Outcome
- The attacker is completely severed from the environment, all persistence mechanisms are eradicated, the legitimate user is securely restored, and root cause is documented.

#### 8. Documentation & Audit Evidence
- Incident Response Post-Mortem Report, full KQL query results, forensic packet/session captures, and before-and-after authentication method audit logs.
