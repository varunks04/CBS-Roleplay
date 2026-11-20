# Case Study 04: Third-Party Vendor B2B Federation

[⬅️ Prev: Rogue Admin Offboarding](./03_Scenario_Emergency_Offboarding_Rogue_Admin.md) | [Back to Scenarios](./README.md) | [Next: Hands-On Labs Track ➡️](../07_Hands_On_Labs/README.md)

---

### 1. Architectural Challenge
Contoso Corporation partners with an external auditing firm ("AuditorCorp"). 50 external auditors require temporary access to internal financial PowerBI dashboards and SharePoint documents.
- **The Bad Way**: Creating 50 internal Contoso accounts (`auditor1@contoso.com`), managing their passwords, and dealing with onboarding/offboarding tickets.
- **The Enterprise Way**: Utilizing **Microsoft Entra B2B Direct Federation / Cross-Tenant Access Policies**.

```mermaid
sequenceDiagram
    autonumber
    actor Auditor as External Auditor (auditor@auditorcorp.com)
    participant ContosoApp as Contoso Financial Portal
    participant ContosoIdP as Contoso Resource Entra ID
    participant ExternalIdP as AuditorCorp Home IdP (Okta / Entra ID)

    Auditor->>ContosoApp: 1. Accesses https://finance.contoso.com
    ContosoApp->>ContosoIdP: 2. Redirects to Contoso Login
    ContosoIdP->>ContosoIdP: 3. Detects domain @auditorcorp.com in B2B Federation catalog
    ContosoIdP-->>Auditor: 4. Redirects to AuditorCorp Home IdP
    Auditor->>ExternalIdP: 5. Authenticates with AuditorCorp credentials & corporate MFA
    ExternalIdP-->>ContosoIdP: 6. Returns signed SAML/OIDC claim
    ContosoIdP->>ContosoIdP: 7. Evaluates B2B Conditional Access (Trust external MFA)
    ContosoIdP-->>ContosoApp: 8. Grants access to financial portal
```

### 2. Security Benefits
- When an auditor is fired from AuditorCorp, their access to Contoso terminates **instantly**, because their home directory account is disabled. Contoso bears zero administrative overhead for password resets or lifecycle tracking.
