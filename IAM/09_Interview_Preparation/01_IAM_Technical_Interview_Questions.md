# 01. IAM Technical Interview Questions and Model Answers

[⬅️ Back to Interview Prep](./README.md) | [Next: Scenario Questions ➡️](./02_IAM_Scenario_Based_Interview_Questions.md)

---

### Q1: Explain the difference between SAML 2.0 and OAuth 2.0 / OpenID Connect. When would you choose one over the other?
**Model Answer**:
- **SAML 2.0** is an XML-based federation protocol designed primarily for enterprise browser-based Single Sign-On (SSO). It packages authentication claims and user attributes inside a heavy, digitally signed XML assertion. It is the enterprise standard for web portals and legacy SaaS applications.
- **OAuth 2.0** is NOT an authentication protocol; it is an authorization framework designed for API access delegation using lightweight tokens (usually JSON Web Tokens - JWTs).
- **OpenID Connect (OIDC)** is the identity authentication layer built directly on top of OAuth 2.0. It returns an `id_token` proving user identity alongside an `access_token` for API authorization.
- **Selection Criteria**: Choose SAML for standard enterprise SaaS applications requiring mature enterprise SSO integration. Choose OIDC/OAuth 2.0 for modern Single-Page Applications (SPAs), mobile apps, and microservice API architectures where lightweight JSON parsing and API authorization scopes are required.

---

### Q2: What is Kerberoasting and how do you remediate it?
**Model Answer**:
Kerberoasting (MITRE ATT&CK T1558.003) is an attack where an authenticated domain user requests a Kerberos Ticket-Granting Service (TGS) ticket for any service account that has a Service Principal Name (SPN) registered. The KDC encrypts the ticket using the password hash of the target service account. The attacker extracts this ticket from memory and performs offline brute-force password cracking using tools like Hashcat.
**Remediation**:
1. Migrate all service accounts running SPNs to **Group Managed Service Accounts (gMSA)**, where Windows automatically manages a 128-character complex password that is rotated automatically.
2. If gMSA cannot be used, enforce passwords of 25+ characters with maximum complexity to render offline cracking computationally infeasible.
3. Monitor Active Directory Event ID 4769 for abnormal volumes of TGS requests with RC4 encryption (`0x17`).

---

### Q3: What is the difference between an ID Token and an Access Token in OIDC?
**Model Answer**:
- An **ID Token** is formatted as a JSON Web Token (JWT) intended strictly for the **Client Application**. It contains identity claims about the user (e.g., `sub`, `email`, `name`, `auth_time`) proving that authentication occurred. The client application decodes the ID token to render the user profile.
- An **Access Token** is intended strictly for the **Resource Server (API)**. It informs the API that the bearer is authorized to perform specific actions defined by the token's `scopes` or entitlements. The client application treats the access token as an opaque string and attaches it to the `Authorization: Bearer <token>` HTTP header.

---

### Q4: Explain the difference between RBAC and ABAC. What is "Role Explosion" and how do you solve it?
**Model Answer**:
- **RBAC (Role-Based Access Control)** assigns permissions to static roles, and users are assigned to roles. It is simple to audit but inflexible. When an organization attempts to model fine-grained access using RBAC (e.g., creating unique roles for every department, location, and seniority level), it suffers from **Role Explosion**, where the directory ends up containing more security groups than human employees.
- **ABAC (Attribute-Based Access Control)** dynamically evaluates access at runtime based on attributes of the Subject (department, clearance), Object (classification), Action (read, delete), and Environment (time, network, device compliance).
- **Solution**: Modern enterprises use a hybrid Policy-Based Access Control (PBAC) model: broad birthright access is assigned via RBAC, while fine-grained data actions are governed dynamically via ABAC policy engines like Open Policy Agent (OPA).

---

### Q5: How does SCIM protocol simplify user lifecycle management?
**Model Answer**:
SCIM (System for Cross-domain Identity Management, RFC 7643/7644) standardizes user provisioning via REST APIs. Without SCIM, an IAM platform must write custom proprietary API integrations for every SaaS tool. With SCIM, the Identity Provider sends standard JSON payloads (`POST /v2/Users`, `PATCH /v2/Users/{id}`) to create, update, or disable accounts across hundreds of applications automatically. When an employee is terminated in HR, SCIM deprovisions downstream accounts in real time, eliminating orphaned accounts and reclaiming costly licenses.
