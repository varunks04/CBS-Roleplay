# 01. Microsoft Entra ID (formerly Azure Active Directory)

[⬅️ Back to Tools](./README.md) | [Next: Okta Workforce Identity ➡️](./02_Okta_Workforce_Identity.md)

---

### 1. Platform Overview

**Microsoft Entra ID** is Microsoft's flagship cloud identity and access management solution, serving as the default identity plane for Microsoft 365, Microsoft Azure, and thousands of enterprise SaaS integrations.

### 2. Key Architecture Components

- **Entra ID Tenant**: A dedicated, trusted instance of Entra ID representing an organization.
- **Microsoft Entra PIM (Privileged Identity Management)**: Provides time-bound, approval-based Just-In-Time role activation for Azure resources and Entra ID roles (e.g., Global Administrator, Security Admin).
- **Microsoft Graph API**: The unified REST API endpoint (`https://graph.microsoft.com/v1.0/`) used to automate identity operations, group assignments, and audit log ingestion via PowerShell or Python.
- **App Registrations & Enterprise Applications**: How internal and third-party apps integrate with the tenant via SAML, OIDC, and OAuth.

### 3. Practical CLI Example: Automating Identity with Microsoft Graph PowerShell

```powershell
# Connect to Entra ID using least-privilege Graph scopes
Connect-MgGraph -Scopes "User.ReadWrite.All", "Directory.AccessAsUser.All"

# Query all users with an active Global Administrator role
$Role = Get-MgDirectoryRole | Where-Object {$_.DisplayName -eq "Global Administrator"}
Get-MgDirectoryRoleMember -DirectoryRoleId $Role.Id | Select-Object Id, DisplayName, UserPrincipalName

# Instantly revoke all active refresh tokens for a compromised user (Emergency response)
Revoke-MgUserSignInsSession -UserId "alice.smith@contoso.com"
```
