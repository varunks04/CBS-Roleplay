# 04. CyberArk Privileged Access Security

[⬅️ Prev: SailPoint](./03_SailPoint_IdentityIQ_and_IdentityNow.md) | [Back to Tools](./README.md) | [Next: HashiCorp Vault ➡️](./05_HashiCorp_Vault_for_Secrets.md)

---

### 1. Platform Overview

**CyberArk** is the industry-standard **Privileged Access Management (PAM)** solution designed to secure, manage, and monitor privileged administrative accounts, secrets, and credentials across the enterprise.

### 2. Architecture & Components

- **Enterprise Password Vault (EPV)**: The hardened, encrypted core database storing all root, domain admin, and service credentials.
- **Central Policy Manager (CPM)**: The automated engine that connects to target servers (Windows, Linux, Cisco, Oracle) to automatically rotate credentials according to enterprise password policy.
- **Privileged Session Manager (PSM)**: The isolated jump-host proxy that brokers RDP/SSH sessions to target systems, injecting vaulted credentials without the human administrator ever seeing them, while recording full video and keystroke logs.
- **Privileged Threat Analytics (PTA)**: Behavioral analytics engine that alerts on anomalous privileged activities (e.g., unvaulted direct root access).
