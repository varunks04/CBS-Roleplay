# 02. Identity Threat Detection and Response (ITDR)

[⬅️ Prev: Passwordless FIDO2](./01_Passwordless_Authentication_FIDO2_and_Passkeys.md) | [Back to Advanced Topics](./README.md) | [Next: CIAM ➡️](./03_Customer_Identity_and_Access_Management_CIAM.md)

---

### 1. Why ITDR Emerged
Traditional endpoint (EDR) and network (NDR) tools have a major blind spot: they monitor processes and packets, but cannot detect identity attack paths inside Active Directory or cloud tenants (e.g., BloodHound graph attack paths, shadow admin accounts, unconstrained Kerberos delegation).
**ITDR** continuously scans and defends the identity infrastructure itself.
