# 06. SCIM Identity Provisioning

[⬅️ Prev: Entra ID & Conditional Access](./05_Microsoft_Entra_ID_and_Conditional_Access.md) | [Back to Protocols](./README.md) | [Next: Hybrid Identity & Sync ➡️](./07_Hybrid_Identity_Entra_Connect_Sync.md)

---

### 🎓 Beginner's Compass: Decoded Acronyms & Terms
- **SCIM (System for Cross-domain Identity Management)**: The open industry standard (RFC 7643 and RFC 7644) that automates creating, updating, and deleting user accounts between an Identity Provider and cloud applications.
- **Provisioning**: Automatically creating a user account and assigning rights when an employee is hired.
- **Deprovisioning**: Automatically suspending or deleting a user account the exact second an employee resigns or is terminated.
- **CRUD Operations**: The four basic database actions: **C**reate (`POST`), **R**ead (`GET`), **U**pdate (`PUT`/`PATCH`), **D**elete (`DELETE`).
- **SCIM Endpoint**: The REST API URL exposed by an application (e.g., `https://api.zoom.us/v2/scim/v2/Users`) where the IdP pushes user data.

---

### 1. Plain-English Walkthrough: The Automated Assembly Line

#### Life Without SCIM (The IT Ticket Nightmare)
Imagine an enterprise with 5,000 employees using 60 SaaS applications (Slack, Zoom, Salesforce, Jira, GitHub):
- Every Monday, 30 new employees start.
- IT technicians must log into 60 separate administrator consoles, type each employee's name, assign their license, and click "Save".
- That is **1,800 manual account creations every single week!**
- Human errors are inevitable: typos in email addresses, forgotten licenses, and missing accounts.
- Worst of all, when an employee is fired, IT forgets to delete their account in 3 out of the 60 tools, leaving gaping backdoors into sensitive company data!

#### Life With SCIM (The Automated Conveyor Belt)
- The HR department marks Alice as hired in Workday.
- Microsoft Entra ID or Okta notices the new hire.
- Via SCIM, the IdP sends automated REST/JSON commands across the internet:
  - Calls Zoom: `POST /Users` -> Zoom account created instantly.
  - Calls Slack: `POST /Users` -> Slack account created instantly.
  - Calls Salesforce: `POST /Users` -> Salesforce account created instantly.
- When Alice is fired, the IdP sends `PATCH /Users/{id}` with `{"active": false}` to all 60 applications simultaneously. Her access is revoked worldwide in under 10 seconds!

```mermaid
flowchart TD
    HR["HR System (Workday)<br/>(Authoritative Source)"] -->|New Hire: Alice Smith| IdP["Identity Provider<br/>(Microsoft Entra ID / Okta)"]
    
    subgraph "SCIM REST API Synchronization (JSON over HTTPS)"
        IdP -->|POST /Users| Slack["Slack API<br/>(Account Provisioned)"]
        IdP -->|POST /Users| Zoom["Zoom API<br/>(Account Provisioned)"]
        IdP -->|POST /Users| AWS["AWS Identity Center<br/>(Account Provisioned)"]
        IdP -->|POST /Users| SF["Salesforce API<br/>(Account Provisioned)"]
    end
```

---

### 2. Anatomy of SCIM REST API Calls

SCIM standardizes user management using common HTTP verbs and JSON:

#### A. Creating a User (`POST /Users`)
```http
POST /scim/v2/Users HTTP/1.1
Host: api.slack.com
Authorization: Bearer scim_token_984102948
Content-Type: application/scim+json

{
  "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
  "userName": "alice.smith@contoso.com",
  "name": {
    "givenName": "Alice",
    "familyName": "Smith"
  },
  "emails": [
    {
      "value": "alice.smith@contoso.com",
      "type": "work",
      "primary": true
    }
  ],
  "title": "Cloud Security Engineer",
  "department": "Cybersecurity",
  "active": true
}
```

#### B. Deprovisioning a User on Termination (`PATCH /Users/{id}`)
```http
PATCH /scim/v2/Users/usr_10293 HTTP/1.1
Host: api.slack.com
Authorization: Bearer scim_token_984102948
Content-Type: application/scim+json

{
  "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
  "Operations": [
    {
      "op": "replace",
      "path": "active",
      "value": false
    }
  ]
}
```

---

### 3. Common Beginner Mistakes vs. Best Practices

| Common Beginner Mistake | Real-World Risk | Industry Best Practice |
| :--- | :--- | :--- |
| **Configuring SSO Without SCIM** | Setting up SAML SSO allows users to log in, but doesn't create user licenses in advance or deprovision accounts when fired (Just-In-Time provisioning creates orphan accounts on termination). | Always pair SAML/OIDC SSO with automated **SCIM Provisioning** for full lifecycle coverage. |
| **Hardcoded SCIM Bearer Tokens** | SaaS SCIM bearer tokens often have 1-year or infinite lifespans. Storing them in plaintext leads to token theft. | Store SCIM API keys in a dedicated Key Vault with automated 90-day rotation reminders. |
| **Attribute Mapping Mismatches** | Mapping `mail` instead of `userPrincipalName` causes accounts to fail syncing or create duplicate profiles. | Verify the target application's SCIM schema documentation; test user provisioning in a staging sandbox first. |

---

### 4. Recommended Next Steps
Discover how organizations synchronize millions of legacy on-premises Active Directory accounts with modern cloud directories:
- [07. Hybrid Identity and Entra Connect Sync Deep Dive ➡️](./07_Hybrid_Identity_Entra_Connect_Sync.md)
