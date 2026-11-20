# 06. SCIM (System for Cross-domain Identity Management)

[⬅️ Prev: Entra ID & Conditional Access](./05_Microsoft_Entra_ID_and_Conditional_Access.md) | [Back to Technologies](./README.md) | [Next: Tools & Platforms Track ➡️](../04_Tools_and_Platforms/README.md)

---

### 1. Definition and Purpose

**SCIM (System for Cross-domain Identity Management)** (RFC 7643 and RFC 7644) is an open, standardized REST and JSON-based protocol designed to make managing user identities in cloud-based applications and services faster, cheaper, and automated.

> 💡 **Analogy**:
> - **SAML and OIDC** handle **Authentication**: They tell the SaaS app who you are when you open your browser.
> - **SCIM** handles **Provisioning**: It creates your account in the SaaS database *before* you ever open your browser, and deletes it the second you leave the company.
> - Without SCIM, an IT administrator would have to manually log into 50 different SaaS admin dashboards every time a new employee is hired to type their name, email, and department. With SCIM, the cloud IdP automatically pushes user creation requests to all 50 apps via standard REST API endpoints.

---

### 2. SCIM Protocol Architecture

```mermaid
sequenceDiagram
    autonumber
    participant IdP as Cloud IdP (SCIM Client: Entra ID / Okta)
    participant SaaS as Target SaaS App (SCIM Server: Slack, GitHub, Salesforce)

    Note over IdP: Joiner Event: Alice Smith hired in Finance
    IdP->>SaaS: POST /scim/v2/Users (JSON payload with user attributes)
    SaaS-->>IdP: HTTP 201 Created (User provisioned in SaaS DB with ID: usr_789)

    Note over IdP: Mover Event: Alice promoted to Director
    IdP->>SaaS: PATCH /scim/v2/Users/usr_789 (title: "Director", role: "Manager")
    SaaS-->>IdP: HTTP 200 OK (User updated)

    Note over IdP: Leaver Event: Alice resigns
    IdP->>SaaS: PATCH /scim/v2/Users/usr_789 (active: false)
    SaaS-->>IdP: HTTP 200 OK (User instantly disabled; licenses freed!)
```

---

### 3. Standard SCIM REST Endpoints and JSON Payload

A SCIM-compliant service provider implements standardized RESTful routes:
- `GET /v2/Users` - Search and list users
- `POST /v2/Users` - Create a new user
- `GET /v2/Users/{id}` - Retrieve user details
- `PUT /v2/Users/{id}` - Replace entire user object
- `PATCH /v2/Users/{id}` - Update specific attributes
- `DELETE /v2/Users/{id}` - Hard delete a user
- `GET, POST, PUT, PATCH /v2/Groups` - Manage group memberships and entitlements

#### Example SCIM User Creation Payload (`POST /scim/v2/Users`):
```json
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
  "title": "Senior Security Analyst",
  "active": true,
  "groups": []
}
```

---

### 4. Enterprise Advantages

- **Zero Orphaned Accounts**: Deprovisioning in the central IdP cascades automatically across every connected SCIM application.
- **License Optimization**: Reclaims costly SaaS software licenses (e.g., \$150/month per-seat tools) instantly upon employee departure.
- **Audit Compliance**: Eliminates manual human error in provisioning workflows.
