# 02. Scenario: Suspicious Impossible Travel Login

[⬅️ Prev: Spear Phishing Payload](./01_Scenario_Spear_Phishing_with_Malicious_Payload.md) | [Back to Scenarios](./README.md) | [Next: Ransomware Deployment ➡️](./03_Scenario_Ransomware_Deployment_Attempt.md)

---

### 🚨 The Incident Briefing
* **Timestamp**: Wednesday, 03:22:10 UTC
* **Affected User**: `marcus.vance@contoso.com` (VP of Engineering)
* **Initial Alert**: Entra ID Identity Protection Alert: *"Atypical Travel & Anonymous IP Sign-in"* (Severity: **High**)

---

### 1. Step-by-Step Investigation Walkthrough

#### Step 1: Query Microsoft Sentinel Sign-in Telemetry
The analyst executes a KQL query to reconstruct Marcus's login activity over the last 6 hours:

```kusto
SigninLogs
| where TimeGenerated > ago(6h)
| where UserPrincipalName == "marcus.vance@contoso.com"
| project TimeGenerated, AppDisplayName, IPAddress, Location, ResultType, DeviceDetail.operatingSystem
| order by TimeGenerated desc
```

#### Step 2: Analyze the Telemetry
| Time (UTC) | IP Address | Location | Device | Result |
| :--- | :--- | :--- | :--- | :--- |
| **03:20:15** | `185.220.101.77` | Frankfurt, Germany | Linux (Tor Browser) | **Success (MFA Satisfied by Cookie)** |
| **02:50:00** | `198.51.100.12` | Austin, Texas (USA) | Windows 11 (Intune Managed) | **Success** |

**The Red Flag**: It is physically impossible to travel from Austin, Texas to Frankfurt, Germany in **30 minutes**!

#### Step 3: Determining How MFA Was Bypassed
Notice that the German login shows `MFA Satisfied`. Why didn't Marcus's phone buzz?
* **AiTM Session Cookie Theft**: Marcus was tricked earlier that day into visiting a fake reverse-proxy login page (`Evilginx`).
* The attacker stole his **`ESTSAUTH` session cookie** and imported it into their Tor browser, bypassing MFA completely!

---

### 2. Immediate Containment Actions Taken

1. **Immediate Session Termination (The Kill Switch)**:
   ```powershell
   Revoke-MgUserSignInSession -UserId "marcus.vance@contoso.com"
   Set-MgUser -UserId "marcus.vance@contoso.com" -AccountEnabled $false
   ```
2. **Check for Malicious Mailbox Rules**:
   Attackers often create hidden forwarding rules to steal incoming emails:
   ```powershell
   Get-InboxRule -Mailbox "marcus.vance@contoso.com"
   ```
   * **Finding**: Found rogue rule *"Move all emails containing 'wire', 'invoice', or 'password' to RSS Feeds folder"*. Rule deleted immediately!
3. **Password Reset & Hardware Key Enrollment**: Force password reset and require FIDO2 YubiKey for future logins.

---

### 3. Root Cause and Lessons Learned
* **Root Cause**: Phone-based push notifications are vulnerable to Adversary-in-the-Middle (AiTM) reverse proxy session theft.
* **Remediation**: Migrate all executives and privileged users to **Phishing-Resistant FIDO2 WebAuthn authentication (YubiKeys / Windows Hello)**.
