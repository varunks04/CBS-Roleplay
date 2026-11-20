# 02. Kerberos Authentication Protocol Deep Dive

[⬅️ Prev: Active Directory & LDAP](./01_Active_Directory_and_LDAP.md) | [Back to Technologies](./README.md) | [Next: SAML 2.0 Deep Dive ➡️](./03_SAML_2.0_Deep_Dive.md)

---

### 1. Definition and Purpose

**Kerberos** is a network authentication protocol developed by MIT (named after the three-headed dog of Greek mythology) designed to provide strong mutual authentication for client/server applications by using secret-key cryptography over an insecure network.

It is the default, native authentication protocol for Microsoft Active Directory and Unix enterprise domains.

> 💡 **Analogy**: The Movie Theater Ticket System:
> 1. You arrive at the theater box office and show your photo ID. You buy a stamped General Admission Wristband (**Ticket Granting Ticket - TGT**). You don't have to show your ID again today.
> 2. You walk down the hall to Auditorium 4 showing "Inception". The theater usher won't let you in with just the general wristband.
> 3. You take your wristband to the internal concession kiosk, and they exchange a stamp for a specific Movie Seat Ticket (**Service Ticket - ST**).
> 4. You hand the Service Ticket to the Auditorium 4 usher. They inspect the ticket, let you in, and tear off the stub.

---

### 2. The Step-by-Step Kerberos Exchange Flow

```mermaid
sequenceDiagram
    autonumber
    actor User as Client Workstation
    participant KDC_AS as KDC: Authentication Service (AS)
    participant KDC_TGS as KDC: Ticket Granting Service (TGS)
    participant Server as Target Application Server (e.g., File Share)

    Note over User,KDC_AS: Phase 1: Authentication Service Exchange
    User->>KDC_AS: 1. KRB_AS_REQ (Client requests TGT; pre-authenticates with timestamp encrypted by password hash)
    KDC_AS->>KDC_AS: 2. Decrypts timestamp with stored user hash; validates time skew (< 5 min)
    KDC_AS-->>User: 3. KRB_AS_REP (Returns TGT encrypted with KRBTGT key + Logon Session Key)

    Note over User,KDC_TGS: Phase 2: Ticket Granting Service Exchange
    User->>KDC_TGS: 4. KRB_TGS_REQ (Presents TGT + Authenticator + SPN of target service)
    KDC_TGS->>KDC_TGS: 5. Decrypts TGT with KRBTGT key; verifies user identity and PAC
    KDC_TGS-->>User: 6. KRB_TGS_REP (Returns Service Ticket encrypted with Target Service Secret Key)

    Note over User,Server: Phase 3: Client/Server Exchange
    User->>Server: 7. KRB_AP_REQ (Presents Service Ticket to Target Server)
    Server->>Server: 8. Decrypts Service Ticket with its own machine secret; extracts user groups (PAC)
    Server-->>User: 9. KRB_AP_REP (Mutual authentication confirmed; grants access!)
```

---

### 3. Key Components & Artifacts

- **Key Distribution Center (KDC)**: The trusted third-party service running on every Active Directory Domain Controller (Port 88). Contains the AS and TGS.
- **TGT (Ticket Granting Ticket)**: A cryptographic ticket encrypted exclusively with the domain's master `krbtgt` account key. Proof that the user successfully authenticated.
- **Service Ticket (ST / TGS)**: A ticket encrypted with the password hash of the specific service account hosting the target resource.
- **SPN (Service Principal Name)**: The unique identifier for a service instance on a network (e.g., `MSSQLSvc/db01.corp.contoso.com:1433`).
- **PAC (Privilege Attribute Certificate)**: A data structure inside the ticket containing the user's Security Identifiers (SIDs) and group memberships.

---

### 4. Infamous Kerberos Attacks (What IAM & SOC Must Know)

#### A. Kerberoasting (MITRE ATT&CK T1558.003)
- **Mechanism**: Any valid authenticated domain user can legally request a Service Ticket (ST) for any service account with a registered SPN.
- Because the ST is encrypted with the service account's password hash, the attacker extracts the ticket from memory, exports it offline, and runs Hashcat to crack the plain-text password using brute-force.
- **Defense**: Enforce 25+ character complex passwords or migrate to **Group Managed Service Accounts (gMSA)** where Active Directory rotates 128-character random keys automatically.

#### B. Golden Ticket Attack (MITRE ATT&CK T1558.001)
- **Mechanism**: If an attacker compromises the hash of the domain's `krbtgt` account, they can forge their own counterfeit TGT tickets offline with arbitrary group memberships (e.g., Domain Admins) and a 10-year validity period. They have total, undetectable domain control.
- **Defense**: Reset the `krbtgt` password **twice** consecutively (to cycle current and previous password history) following any domain breach.
