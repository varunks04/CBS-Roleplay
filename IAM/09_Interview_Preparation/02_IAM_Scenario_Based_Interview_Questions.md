# 02. IAM Scenario-Based Interview Questions

[⬅️ Prev: Technical Questions](./01_IAM_Technical_Interview_Questions.md) | [Back to Interview Prep](./README.md)

---

### Scenario 1: The Zero-Downtime SAML Certificate Rollover
**Prompt**: *"The token-signing X.509 certificate on your primary IdP (Entra ID / Okta) expires in 3 days. It is used by 180 federated enterprise applications. How do you roll over this certificate without causing an enterprise-wide SSO outage?"*

**Model Strategy**:
1. **Secondary Certificate Generation**: Generate a new valid token-signing certificate in the IdP and set its status to **Inactive / Secondary**. Do NOT immediately promote it to primary.
2. **Metadata Export**: Export the updated IdP Federation Metadata XML file, which now contains **both** the expiring primary public certificate and the new secondary public certificate.
3. **Application Updates**:
   - For applications supporting dynamic metadata polling (e.g., Salesforce, Zoom), trigger a metadata refresh.
   - For applications requiring manual certificate upload, upload the new public certificate as secondary.
4. **Promotion**: Once all downstream SPs have the new certificate staged, promote the new certificate to **Primary** in the IdP.
5. **Validation**: Test SSO across critical applications immediately.

---

### Scenario 2: Handling a Rogue Admin with Standing Privileges
**Prompt**: *"An IT Admin who just received a negative performance review has been observed querying Active Directory for executive passwords and downloading customer lists. How do you respond?"*

**Model Strategy**:
1. **Immediate Revocation**: Coordinate with HR to initiate the emergency leaver protocol immediately.
2. **Session Severing**: Fire Graph API session revocation (`Revoke-MgUserSignInsSession`) and invalidate active Kerberos tickets.
3. **Password Scramble**: Scramble on-premises and cloud password hashes and disable the account.
4. **Audit Scope**: Inspect SIEM logs for all systems accessed by that admin in the preceding 7 days.
5. **Credential Rotation**: Force password rotation on any shared service accounts the administrator had access to.
