# 03. Single Sign-On (SSO) and Identity Federation

[⬅️ Prev: RBAC vs ABAC](./02_RBAC_vs_ABAC_Access_Models.md) | [Back to Core Concepts](./README.md) | [Next: Multi-Factor Authentication ➡️](./04_Multi_Factor_Authentication_MFA.md)

---

### 1. Definition and Purpose

**Single Sign-On (SSO)** is an architectural mechanism that allows a user to authenticate once with a trusted central **Identity Provider (IdP)** and subsequently gain access to multiple independent applications (**Service Providers / Relying Parties**) without re-entering credentials.

**Identity Federation** expands SSO beyond a single corporate boundary. It establishes cryptographic trust between two distinct security domains (e.g., your company allowing its employees to log into an external vendor's SaaS platform using their corporate credentials).

> 💡 **Analogy**:
> - **Without SSO**: You visit an amusement park where every single ride requires you to create a separate account, remember a unique password, and pay an attendant individually. You carry 40 different membership cards.
> - **With SSO / Federation**: You show your verified passport at the central ticket office once. They give you a tamper-proof digital wristband (Security Token). Every ride attendant simply scans the wristband to verify you are authorized to ride. The individual ride operators never need or store your passport details.

---

### 2. Core Federation Architecture and Entities

```mermaid
sequenceDiagram
    autonumber
    actor User as User Browser
    participant SP as Service Provider (e.g., Salesforce SaaS)
    participant IdP as Identity Provider (e.g., Okta / Entra ID)

    User->>SP: 1. Attempts to access https://company.salesforce.com
    SP->>SP: 2. Recognizes corporate domain; generates SAML AuthnRequest
    SP-->>User: 3. HTTP 302 Redirect to IdP with encoded AuthnRequest
    User->>IdP: 4. Follows redirect to IdP login portal
    Note over IdP: 5. Authenticates user (Password + FIDO2 MFA)
    IdP->>IdP: 6. Evaluates Conditional Access & builds cryptographically signed SAML Assertion
    IdP-->>User: 7. HTTP POST containing signed SAML Response / Assertion
    User->>SP: 8. Posts SAML Response back to SP Assertion Consumer Service (ACS)
    SP->>SP: 9. Verifies IdP digital signature using pre-shared public X.509 certificate
    Note over SP: Signature valid! User authenticated as user@company.com
    SP-->>User: 10. Establishes application session & grants access
```

---

### 3. Key Concepts and Industry Vocabulary

- **Identity Provider (IdP)**: The authoritative entity that creates, maintains, and manages identity information while providing authentication services (e.g., Microsoft Entra ID, Okta, Ping Identity).
- **Service Provider (SP) / Relying Party (RP)**: The application or resource that provides the service and relies on the IdP for user authentication (e.g., Salesforce, ServiceNow, AWS Console).
- **Security Assertion**: A digitally signed XML or JSON document containing statements regarding user authentication, attributes (email, department), and entitlements.
- **Trust Relationship**: The pre-established cryptographic pairing where the SP trusts assertions signed by the IdP's private key, verified using the IdP's public certificate.

---

### 4. Enterprise Advantages vs Security Risks

| Advantages | Security Risks & Mitigations |
| :--- | :--- |
| **Elimination of Password Fatigue**: Users remember 1 strong credential rather than 50 weak passwords written on sticky notes. | **Single Point of Failure**: If the IdP goes down, no corporate applications are accessible. *Mitigation: High-availability multi-region IdP architecture.* |
| **Instant Offboarding**: Disabling the user in the IdP immediately cuts off access to all federated SaaS apps. | **Blast Radius of Credential Theft**: If an attacker compromises the IdP session, they compromise access to everything. *Mitigation: Strict FIDO2 MFA and Conditional Access.* |
| **Centralized Audit Logging**: Every application authentication event is logged in a single place for the SOC to monitor. | **Misconfigured Assertion Validation**: If the SP fails to validate the digital signature or audience restriction, attackers can forge assertions. |
