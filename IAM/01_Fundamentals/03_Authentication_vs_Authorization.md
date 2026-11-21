# 03. Authentication vs Authorization: The Cornerstone Boundary

[⬅️ Prev: Identity vs Account](./02_Identity_vs_Account_vs_Identifier.md) | [Back to Fundamentals](./README.md) | [Next: Zero Trust Model ➡️](./04_The_Zero_Trust_Identity_Model.md)

---

### 🎓 Beginner's Compass: Decoded Terms in This File
- **AuthN (Authentication)**: Proving that you are who you say you are (*"Show me your ID"*).
- **AuthZ (Authorization)**: Granting permission to do specific things (*"You may enter Room 104, but not the vault"*).
- **JWT (JSON Web Token)**: A compact, digitally signed data packet passed between web apps containing user claims.
- **HTTP 401 Unauthorized**: Misleadingly named in web standards—it actually means **Unauthenticated** (*"Who are you? Log in first"*).
- **HTTP 403 Forbidden**: Means **Unauthorized** (*"I know who you are, but you do not have permission to touch this"*).
- **IDOR / BOLA (Broken Object-Level Authorization)**: A critical security vulnerability where an app verifies who you are, but fails to check whether you own the file you asked to view.

---

### 1. Conceptual Breakdown

One of the most frequent mistakes in cybersecurity is treating **Authentication** and **Authorization** as interchangeable. They address two fundamentally different questions:

- **Authentication (AuthN)**: *"Who are you?"* (Identity verification)
- **Authorization (AuthZ)**: *"What are you permitted to do?"* (Access decision)

> 💡 **Analogy**: Commercial Airline Travel
> 1. At airport security (TSA), an officer checks your physical government passport and scans your face with biometric cameras. They confirm that you are truly John Doe. **This is Authentication (AuthN)**.
> 2. At the airplane gate, the scanner reads your boarding pass barcode. It confirms you are assigned seat 14B in Economy, meaning you cannot walk up and sit in First Class Suite 1A, nor can you enter the cockpit. **This is Authorization (AuthZ)**.
> Proving who you are does not grant you the right to enter every room.

---

### 2. Deep Comparison Matrix

| Dimension | Authentication (AuthN) | Authorization (AuthZ) |
| :--- | :--- | :--- |
| **Core Question** | Who is the caller/user? | What actions can the verified caller perform? |
| **Execution Order** | Must ALWAYS occur first. | Occurs second, only after AuthN succeeds. |
| **Input Artifacts** | Passwords, TOTP tokens, FIDO2 WebAuthn keys, X.509 certificates. | Security tokens containing claims, scopes, groups, roles, ABAC policies. |
| **Protocols & Standards** | Kerberos, OpenID Connect (OIDC), SAML 2.0 (AuthnRequest), FIDO2/WebAuthn. | OAuth 2.0 (Access Tokens), XACML, OPA (Open Policy Agent Rego), AWS IAM Policies. |
| **Visibility** | Directly visible to and experienced by the end-user (login prompts, MFA push). | Largely invisible to user; enforced by backend API gateways and services. |
| **Failure Response** | `401 Unauthorized` (Technically means *Unauthenticated*). | `403 Forbidden` (User is authenticated, but lacks sufficient permissions). |

---

### 3. Step-by-Step Technical Execution Flow

```mermaid
sequenceDiagram
    autonumber
    actor User as User (Browser)
    participant IdP as Identity Provider (AuthN)
    participant API as API Gateway / App (AuthZ)
    participant DB as Customer Database

    User->>IdP: 1. Submit Credentials + FIDO2 Token
    IdP->>IdP: 2. Verify Credential Hash & Biometric Signature
    Note over IdP: Authentication Successful!
    IdP-->>User: 3. Issue Signed JWT with Claims: {sub: "user123", role: "Viewer"}

    User->>API: 4. HTTP POST /records/delete (Bearer JWT)
    API->>API: 5. Inspect JWT: User is authenticated, but role is 'Viewer'
    Note over API: Authorization Check: DELETE requires 'Admin' role!
    API-->>User: 6. HTTP 403 Forbidden (Insufficient Entitlements)
```

---

### 4. Common Security Vulnerabilities

#### A. Broken Object-Level Authorization (BOLA / IDOR)
- **Mechanism**: The application correctly authenticates the user, but fails to check whether the authenticated user owns the specific object requested.
- **Example**: User 102 logs in successfully (`HTTP 200`). Then they request `GET /api/invoices?id=103`. The server verifies that User 102 is authenticated and returns User 103's private financial data!
- **Remediation**: Always enforce authorization checks at the data layer against the current authenticated subject context.

#### B. Confused Deputy Attack
- **Mechanism**: A privileged service is tricked by an unprivileged user into performing an action on the user's behalf that the user themselves lacks permission to execute.
