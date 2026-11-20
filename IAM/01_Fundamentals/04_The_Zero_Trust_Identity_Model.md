# 04. The Zero Trust Identity Model

[⬅️ Prev: AuthN vs AuthZ](./03_Authentication_vs_Authorization.md) | [Back to Fundamentals](./README.md) | [Next: Core Concepts Track ➡️](../02_Core_Concepts/README.md)

---

### 1. The Death of the Castle-and-Moat Architecture

Historically, enterprise security relied on the **perimeter security model** (the "Castle and Moat"):
- Everything outside the corporate firewall was hostile.
- Everything inside the corporate network (LAN or VPN) was trusted.

Once an attacker or compromised laptop breached the perimeter, they had unfettered lateral movement across the entire corporate network.

```mermaid
graph TD
    subgraph "Legacy Castle-and-Moat (Broken)"
        Firewall["Perimeter Firewall"]
        LAN["Internal Corporate Network (Implicit Trust)"]
        Firewall -->|VPN Breach| LAN
        LAN --> Server1["Database (No Auth)"]
        LAN --> Server2["File Share (Open)"]
    end

    subgraph "Modern Zero Trust Architecture"
        Client["Requesting Device & User"]
        PEP["Policy Enforcement Point (Conditional Access)"]
        PDP["Policy Decision Point (Risk Engine)"]
        App["Target Application"]

        Client -->|"Every Request"| PEP
        PEP <-->|"Evaluate Signals"| PDP
        PEP -->|"Encrypted, Explicitly Authorized Channel"| App
    end
```

---

### 2. Core Zero Trust Principles (NIST SP 800-207)

1. **Verify Explicitly**: Always authenticate and authorize based on all available data points—including user identity, location, device health, service or workload, data classification, and anomalies.
2. **Use Least Privileged Access**: Limit user access with Just-In-Time (JIT) and Just-Enough-Access (JEA), risk-based adaptive policies, and data protection.
3. **Assume Breach**: Minimize blast radius and segment access. Verify end-to-end encryption and use analytics to gain visibility and improve threat detection.

---

### 3. Identity as the Primary Control Plane

In Zero Trust, **Identity replaces the Corporate Network** as the boundary of control.

Before granting access to any resource, the Identity Provider evaluates dynamic signals in real time:

```mermaid
flowchart TD
    UserSig["User Signals:<br>- Group Memberships<br>- Compromised Credentials DB<br>- Real-time User Risk"] --> Engine
    DevSig["Device Signals:<br>- Managed by Intune/MDM?<br>- Antivirus Healthy?<br>- BitLocker Enabled?"] --> Engine
    LocSig["Location & Network:<br>- Corporate IP or Anonymous Proxy?<br>- Impossible Travel Velocity?"] --> Engine
    AppSig["Resource Sensitivity:<br>- Public Web App vs High-Risk HR/Payroll"] --> Engine

    Engine{"Policy Decision Engine<br>(Conditional Access)"}

    Engine -->|Low Risk| Allow["Allow Access"]
    Engine -->|Medium Risk| MFA["Challenge with FIDO2 MFA"]
    Engine -->|High Risk / Non-Compliant Device| Block["Block Access & Alert SOC"]
```

---

### 4. Key Metrics for Zero Trust Identity Maturity

- **100% MFA Enforcement**: No legacy basic authentication (POP3, IMAP, NTLM).
- **Device Health Verification**: Only compliant, enrolled devices can connect to SaaS data.
- **Continuous Access Evaluation (CAE)**: If an active session user gets fired or their password is changed, their active tokens are revoked across all apps within seconds, rather than waiting for token expiration.
