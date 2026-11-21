# 00. The IAM Beginner's Rosetta Stone: Acronym & Tool Decoder

[⬅️ Back to Fundamentals](./README.md) | [Next: What is IAM? ➡️](./01_What_is_IAM.md)

---

### 🌟 Why This Document Exists

When you enter Identity and Access Management (IAM), you are hit with a dizzying wall of three-letter and four-letter abbreviations: **IAM, IdP, SP, SSO, MFA, IGA, PAM, JML, RBAC, ABAC, SCIM, SAML, OIDC, TAP, PIM**.

This document is your **cheat-sheet and compass**. Whenever you see an acronym in any file or meeting, return here to understand:
1. What it stands for.
2. What it actually is in plain, simple English.
3. The relatable real-world analogy.
4. What happens if a company does NOT have it.
5. The top industry tools that provide it.

---

## 📚 Master IAM Acronym & Tool Dictionary

### 1. IAM — Identity and Access Management
- **What it stands for**: Identity and Access Management.
- **Plain English**: The overall discipline, security rules, and software that make sure the right people (and computers) get access to the right corporate resources, and keep unauthorized people out.
- **Analogy**: The complete security, badging, lock-and-key, and elevator system of an entire international airport.
- **Without it**: Chaos. Anyone can walk into the cockpit or server room; nobody knows who unlocked which door.

---

### 2. IdP — Identity Provider
- **What it stands for**: Identity Provider.
- **Plain English**: The central "master authentication server" where your username, password, and MFA live. Instead of creating accounts in 50 different apps, you create ONE account in the IdP, and all apps trust the IdP.
- **Analogy**: The Government Passport Office. The French embassy, the hotel, and the airline don't need to interview your parents to know who you are; they simply trust the passport issued by your government.
- **Top Enterprise Tools**: **Microsoft Entra ID** (formerly Azure AD), **Okta**, **Ping Identity**.
- **Without it**: Employees have to remember 60 different passwords for 60 different work websites written down on paper sticky notes.

---

### 3. SP / RP — Service Provider / Relying Party
- **What it stands for**: Service Provider (in SAML) or Relying Party (in OIDC/OAuth).
- **Plain English**: The actual application or website you are trying to use (e.g., Salesforce, Slack, Zoom, AWS). It provides the service, but it *relies* on the IdP to verify who you are.
- **Analogy**: The airline flight attendant checking your passport. The airline is the Service Provider; the government is the Identity Provider.
- **Examples**: Google Workspace, GitHub, Workday, ServiceNow, Zoom.

---

### 4. SSO — Single Sign-On
- **What it stands for**: Single Sign-On.
- **Plain English**: The technology that lets you log in **once** at the start of your day, and then open Slack, Salesforce, email, and payroll without ever typing your password again.
- **Analogy**: The waterproof wristband given to you at an amusement park. You show your ID once at the front gate, get the wristband, and ride all 30 rollercoasters without taking out your wallet or passport again.
- **Without it**: Extreme "password fatigue", locked accounts, and helpdesk call centers overwhelmed with reset tickets.

---

### 5. MFA — Multi-Factor Authentication
- **What it stands for**: Multi-Factor Authentication (sometimes called 2FA - Two-Factor Authentication).
- **Plain English**: Requiring you to prove who you are using **at least two different categories of proof** (e.g., your password PLUS a fingerprint, or your password PLUS a hardware key tap).
- **Analogy**: Withdrawing money from an ATM. You need the physical plastic card (**something you have**) PLUS the 4-digit PIN (**something you know**). A thief with just your card or just your PIN cannot get your cash.
- **Top Tools**: Microsoft Authenticator, Okta Verify, YubiKey (FIDO2 Hardware Key), Duo Security.
- **Without it**: If an employee's password is leaked in a dark web dump, the hacker logs into their account instantly.

---

### 6. IGA — Identity Governance and Administration
- **What it stands for**: Identity Governance and Administration.
- **Plain English**: The "compliance and auditing brain" of IAM. While an IdP logs you in, IGA answers: *Who approved this access? Does Alice still need access to the finance database after moving to marketing? Let's force managers to audit access every 90 days.*
- **Analogy**: The city building inspector and financial auditor who visits every quarter with a clipboard to make sure every tenant has the correct legal permits and nobody knocked down a load-bearing wall.
- **Top Tools**: **SailPoint IdentityIQ / IdentityNow**, **Saviynt**, **Omada Identity**.
- **Without it**: Massive "privilege creep" (employees accumulate hundreds of permissions over years) and instant failures during SOX, HIPAA, and ISO 27001 regulatory audits.

---

### 7. PAM — Privileged Access Management
- **What it stands for**: Privileged Access Management.
- **Plain English**: Specialized software built to lock up, rotate, and monitor "super-admin" keys (like Windows Domain Admin, Linux root, AWS cloud root, and database sysadmin credentials).
- **Analogy**: The armored bank vault with dual-control combination locks. Ordinary tellers don't carry the keys; you must sign out the key, a security guard watches you, and a CCTV camera records your every move until you return the key.
- **Top Tools**: **CyberArk**, **BeyondTrust**, **HashiCorp Vault**, **Delinea**.
- **Without it**: Admins share a master root password written in a spreadsheet. If an attacker breaches one admin PC, they get the keys to the entire corporate kingdom.

---

### 8. PIM — Privileged Identity Management
- **What it stands for**: Privileged Identity Management (Microsoft's cloud implementation of PAM).
- **Plain English**: A feature inside Microsoft Entra ID that enforces "Zero Standing Privileges". Instead of being an administrator 24/7, an engineer has standard access. When an emergency happens, they request to "activate" admin rights for 2 hours with ticket justification.
- **Analogy**: A fire extinguisher cabinet. The glass isn't open all day; you break the glass and use the extinguisher only when there is an active fire, and it triggers an alarm.

---

### 9. JML — Joiner, Mover, Leaver
- **What it stands for**: Joiner, Mover, Leaver Process.
- **Plain English**: The complete lifecycle of an employee in a company:
  - **Joiner**: Newly hired ➔ Automatically create accounts and give birthright access.
  - **Mover**: Promoted or transferred ➔ Give new department access and revoke old access.
  - **Leaver**: Resigned or fired ➔ Instantly shut down all accounts, revoke tokens, and wipe company phones.
- **Analogy**: Checking in, upgrading rooms, and checking out of a luxury hotel.
- **Without it**: Fired employees can still log into their work email from their personal phone 6 months later.

---

### 10. RBAC vs ABAC — Role-Based vs Attribute-Based Access Control
- **RBAC**: Access granted based on broad job titles (e.g., *"All Financial Analysts can view the ledger"*).
  - *Analogy*: A VIP concert ticket that lets anyone wearing the badge into the backstage tent.
- **ABAC**: Access evaluated dynamically based on real-time attributes (e.g., *"Allow access ONLY IF user is in Finance, AND device is corporate-managed, AND location is inside the USA, AND time is between 9 AM and 5 PM"*).
  - *Analogy*: A smart biometric door that checks your badge, tests your temperature, verifies you are wearing a hard hat, and only unlocks during business hours.

---

### 11. SCIM — System for Cross-domain Identity Management
- **What it stands for**: System for Cross-domain Identity Management (RFC 7643/7644).
- **Plain English**: A standardized REST/JSON language that lets your central IdP automatically push user accounts into cloud apps like Slack, Salesforce, and Zoom.
- **Analogy**: The National Postal Forwarding Service. When you change your home address at the post office, they automatically notify your bank, utility company, and magazine subscriptions.
- **Without it**: IT Helpdesk staff must manually log into 50 different admin websites to type usernames and passwords every time someone is hired or fired.

---

### 12. SAML 2.0 vs OAuth 2.0 vs OIDC
- **SAML 2.0**: The heavy XML-based protocol for enterprise browser Single Sign-On (mostly for web portals).
  - *Analogy*: A sealed, stamped, physical diplomatic passport letter.
- **OAuth 2.0**: An authorization delegation framework that gives apps permission to call APIs without knowing your password.
  - *Analogy*: A valet parking key that lets the attendant drive your car 50 feet, but won't open your locked glovebox.
- **OIDC (OpenID Connect)**: The modern JSON/JWT identity layer built on top of OAuth 2.0 that tells the app *who you are*.
  - *Analogy*: A modern digital driver's license with your photo and birthdate.

---

### 13. TAP — Temporary Access Pass
- **What it stands for**: Temporary Access Pass (Microsoft Entra ID).
- **Plain English**: A time-limited, one-time passcode issued to a new hire or someone who lost their phone, allowing them to sign in for the very first time and register their permanent biometric/FIDO2 MFA key without needing a password.
- **Analogy**: The temporary paper parking permit the security gate hands you on your first day until your permanent windshield transponder is installed.

---

### 14. EDR / MDR / SIEM / SOC (The Detective Terms)
- **SIEM (Security Information & Event Management)**: The central database that collects all login logs and firewall records (e.g., Microsoft Sentinel, Splunk).
  - *Analogy*: The security control room with 100 TV screens recording all cameras.
- **EDR (Endpoint Detection & Response)**: Software installed on laptops and servers that stops malware and kills malicious processes (e.g., CrowdStrike Falcon, Microsoft Defender).
  - *Analogy*: A personal armed security bodyguard standing next to each computer.
- **MDR (Managed Detection and Response)**: An external 24/7 outsourced security team of human analysts that monitors your EDR and SIEM for you if your company is too small to build a full SOC.
  - *Analogy*: Hiring a professional private security firm (like ADT or Securitas) to monitor your alarm system 24/7.
