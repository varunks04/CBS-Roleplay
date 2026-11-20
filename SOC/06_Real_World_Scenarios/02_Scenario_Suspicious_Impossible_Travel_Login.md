# Case Study 02: Suspicious Impossible Travel and Session Replay

[⬅️ Prev: Spear Phishing](./01_Scenario_Spear_Phishing_with_Malicious_Payload.md) | [Back to Scenarios](./README.md) | [Next: Ransomware Deployment ➡️](./03_Scenario_Ransomware_Deployment_Attempt.md)

---

### 1. Alert Context
Microsoft Sentinel triggers a High-Severity incident:
`Atypical Travel Sign-in detected for user: cfo@contoso.com`
- **10:00 AM EST**: Successful login from New York, USA (Known corporate IP, Windows 11).
- **10:42 AM EST**: Successful login from Lagos, Nigeria (Tor Exit Node, Linux User-Agent).
Physical travel speed required: 6,800 miles/hour (Physically impossible).

### 2. KQL Investigation Query
```kql
SigninLogs
| where UserPrincipalName == "cfo@contoso.com"
| where TimeGenerated > ago(6h)
| project TimeGenerated, IPAddress, Location, UserAgent, ResultType, RiskLevelDuringSignIn, AuthenticationRequirement
```

### 3. Analyst Discovery
The second sign-in bypassed MFA without prompting! Why?
The attacker executed an **Adversary-in-the-Middle (AiTM)** reverse proxy attack earlier that morning. They stole the legitimate `ESTSAUTH` session cookie from the CFO's browser and replayed it from an AWS VPS in Nigeria.

### 4. Remediation Steps
1. Kill all active refresh tokens and sessions:
   `Revoke-MgUserSignInsSession -UserId "cfo@contoso.com"`
2. Enforce Continuous Access Evaluation (CAE) to terminate active sessions immediately.
3. Check for attacker persistence:
   - Verify if any new authenticator app or phone number was added.
   - Check Exchange Online for new Inbox Rules forwarding mail outside the organization.
