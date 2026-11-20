# Case Study 04: Internal Brute-Force and Pass-the-Hash Lateral Movement

[⬅️ Prev: Ransomware Attempt](./03_Scenario_Ransomware_Deployment_Attempt.md) | [Back to Scenarios](./README.md) | [Next: DNS Exfiltration ➡️](./05_Scenario_DNS_Exfiltration_and_Beaconing.md)

---

### 1. Detection Mechanics
An internal workstation generates 4,000 Event ID 4625 (Logon Failure) events against 50 different servers on port 445 within 3 minutes, followed by an Event ID 4624 (Logon Type 3: Network) using NTLM authentication.

### 2. Technical Mechanism: Pass-the-Hash (MITRE T1550.002)
The attacker dumped the local SAM database on a compromised workstation using Mimikatz, extracting the NTLM hash of a local administrator. Because the enterprise reused the exact same local administrator password across all workstations, the attacker uses the stolen hash directly to authenticate across the entire network without needing the plain-text password.

### 3. Remediation & Enterprise Hardening
1. Deploy **Microsoft LAPS (Local Administrator Password Solution)**: Automatically generates unique, random, 24-character passwords for every workstation's local admin account and rotates them in Active Directory.
2. Disable NTLM authentication across the domain in favor of Kerberos.
