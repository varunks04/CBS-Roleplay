# 04. Active Directory Attack Paths and BloodHound Defense

[⬅️ Prev: CIAM](./03_Customer_Identity_and_Access_Management_CIAM.md) | [Back to Advanced Topics](./README.md) | [Next: Interview Prep ➡️](../09_Interview_Preparation/README.md)

---

### 1. Definition and Purpose

For decades, security teams audited Active Directory by looking at individual group memberships (e.g., checking who is in the `Domain Admins` group). 

Adversaries do not think in lists; **adversaries think in graphs**. 

**BloodHound** is an open-source graph analysis tool that visualizes hidden, non-obvious relationships, permissions, and access rights within Active Directory and Azure/Entra ID environments to uncover critical **Attack Paths** that lead to total administrative takeover.

> 💡 **Analogy**: The Domino Effect:
> - You believe your vault is safe because only the CEO has the master key.
> - But BloodHound reveals: The Janitor has write access to the HVAC maintenance panel ➔ The HVAC panel can reboot the Security Room Server ➔ The Security Room Server caches the Helpdesk Tech's session ➔ The Helpdesk Tech can reset the CEO's password.
> By tipping over the first unprivileged domino, the attacker knocks down the final king.

---

### 2. The Concept of "Shadow Admins"

A **Shadow Admin** is an account that is **not** a member of any high-privilege group (like Domain Admins or Enterprise Admins), but possesses granular Active Directory Access Control List (ACL) permissions that can be chained to achieve full Domain Admin rights.

```mermaid
graph LR
    Attacker["Compromised Standard User<br>(john.helpdesk)"] -->|"GenericAll / WriteDACL"| Group["Security Group:<br>Workstation Admins"]
    Group -->|"MemberOf"| Server["Workstation WS-099"]
    Server -->|"Active RDP Session"| Admin["Domain Admin Account<br>(admin-alice)"]
    Admin -->|"DCSync Right (DS-Replication-Get-Changes-All)"| DC["Domain Controller (KRBTGT Hash Dumped!)"]
```

---

### 3. Dangerous Active Directory Object ACL Permissions

| ACE / Right | What It Allows an Attacker to Do |
| :--- | :--- |
| **GenericAll** | Full control over the target object (e.g., can reset user's password, modify group members, add SPNs). |
| **WriteDacl** | Modify the security permissions (DACL) of the object, granting themselves `GenericAll`. |
| **WriteOwner** | Take ownership of the target object, then assign themselves `WriteDacl`. |
| **ForceChangePassword** | Instantly reset the target user's password without knowing their current password. |
| **AddMember** | Add any user or group to the target security group. |
| **DCSync (`DS-Replication-Get-Changes-All`)** | Impersonate a Domain Controller to pull password hashes (including `krbtgt`) directly over RPC. |

---

### 4. The Microsoft Enterprise Access Model (Tiering)

To break BloodHound attack paths, enterprises enforce the **Enterprise Access Model** (formerly known as AD Tiering):

```mermaid
graph TD
    subgraph "Control Plane (Tier 0)"
        T0["Tier 0: Identity & Control Plane<br>- Domain Controllers, Entra ID, PKI, ADFS, PAM<br>- Admin accounts used ONLY on Tier 0 systems<br>- Zero internet access"]
    end

    subgraph "Management Plane (Tier 1)"
        T1["Tier 1: Enterprise Servers & Workloads<br>- Database servers, web clusters, cloud VMs<br>- Server admins NEVER log into user workstations"]
    end

    subgraph "User Access (Tier 2)"
        T2["Tier 2: User Devices & Workstations<br>- Laptops, desktops, mobile phones, printers<br>- Local admin credentials unique per machine (via LAPS)"]
    end

    T0 -.->|"Strict Isolation: No Downward Credential Bleed"| T1
    T1 -.->|"Strict Isolation: No Downward Credential Bleed"| T2
```

#### The Golden Rule of Tiering:
**Credentials from a higher tier must NEVER be typed or cached on a system belonging to a lower tier.**
If a Domain Admin logs into an infected Tier 2 laptop to troubleshoot an email issue, their credentials are saved in memory (`lsass.exe`), instantly gifting the attacker Tier 0 control.
