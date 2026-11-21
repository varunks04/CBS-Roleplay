# 04. IAM Tools and Platforms Track

In the cybersecurity industry, understanding theoretical protocols is not enough. To land a job or perform on day one, you must understand the **actual software platforms** used by enterprise organizations.

---

### 🗺️ The Enterprise Identity Architecture Stack

Beginners often get confused because they think tools like Okta, SailPoint, and CyberArk compete with each other. **They do not!** In a large enterprise, they work together as complementary layers of defense:

```mermaid
flowchart TD
    HR["1. Authoritative Source of Truth<br/>(Workday / SAP SuccessFactors / BambooHR)"] -->|Employee Data (Hire/Transfer/Fire)| IGA["2. Identity Governance & Administration (IGA)<br/>(SailPoint IdentityNow / IdentityIQ)<br/>Rules, Roles, SoD, Audits & Approvals"]
    
    IGA -->|Provision User & Groups via SCIM| IDP["3. Cloud Identity Provider (IdP) & SSO<br/>(Microsoft Entra ID / Okta)<br/>MFA, Conditional Access, App Catalog"]
    
    IDP -->|Federated SSO / OIDC / SAML| SaaS["4. Cloud SaaS & Business Apps<br/>(Salesforce, Zoom, M365, AWS, GitHub)"]
    
    IGA -->|Vault Admin Access Requests| PAM["5. Privileged Access Management (PAM)<br/>(CyberArk / BeyondTrust / HashiCorp Vault)<br/>Domain Admins, Root Keys, Dynamic Secrets"]
    
    PAM -->|Secure RDP/SSH Proxied Sessions| Infra["6. Critical Infrastructure<br/>(Domain Controllers, Cloud Root, SQL Databases)"]
    
    IDP & PAM -->|Streaming Audit Logs & Threat Telemetry| SOC["7. SOC / SIEM & MDR Layer<br/>(Microsoft Sentinel / Splunk + CrowdStrike MDR)<br/>24/7 Real-Time Detection & Rapid Incident Response"]
```

---

### 📚 Tools at a Glance: The Enterprise Toolbox

| Tool Category | Leading Platform | Primary Role | What Happens Without It? |
| :--- | :--- | :--- | :--- |
| **Cloud IdP & SSO** | [01. Microsoft Entra ID](./01_Microsoft_Entra_ID.md) | Central cloud identity, Conditional Access, and M365 authentication | Employees have 50 separate passwords; IT cannot enforce MFA across apps. |
| **Cloud Workforce IdP** | [02. Okta Workforce Identity](./02_Okta_Workforce_Identity.md) | Universal cloud SSO, MFA, adaptive access, and automated workflows | Password sprawl, credential stuffing attacks, and fragmented app access. |
| **Governance & IGA** | [03. SailPoint](./03_SailPoint_IdentityIQ_and_IdentityNow.md) | Role mining, compliance audits, access certifications, and JML automation | Fired employees retain access for months; companies fail federal SOX audits. |
| **Human PAM** | [04. CyberArk PAS](./04_CyberArk_Privileged_Access_Security.md) | Vaulting admin passwords, recording privileged sessions, and JIT elevation | Shared `root`/`admin` passwords on sticky notes; zero audit accountability. |
| **Machine Secrets PAM** | [05. HashiCorp Vault](./05_HashiCorp_Vault_for_Secrets.md) | Dynamic database credentials, API key brokering, and secret encryption | Plaintext database passwords committed to public GitHub repositories. |
| **IAM Automation** | [06. PowerShell & Graph API](./06_IAM_Automation_with_PowerShell_and_Graph_API.md) | Automating bulk user provisioning, emergency session kill-switch, stale audits | Manual clicking for 10,000 users; impossible to meet emergency incident SLAs. |

---

### 🎓 How to Study This Module
1. Read the **Beginner's Compass** callout in each tool guide to understand its vocabulary.
2. Read the **"What Happens Without It?"** section to understand why enterprises pay millions of dollars for these tools.
3. Review how each platform integrates with **SOC, SIEM, and MDR teams** to protect organizations during live cyberattacks.
