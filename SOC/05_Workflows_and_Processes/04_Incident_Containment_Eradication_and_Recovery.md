# 04. Incident Containment, Eradication, and Recovery SOP

[⬅️ Prev: False Positive Tuning](./03_False_Positive_Identification_and_Tuning.md) | [Back to Workflows](./README.md) | [Next: Escalation Matrix ➡️](./05_Escalation_Matrix_L1_to_L2_L3_and_IR.md)

---

### 1. Workflow Architecture Diagram

```mermaid
flowchart TD
    subgraph "Phase 1: Containment"
        C1["Host Network Isolation via EDR"] --> C2["Revoke Cloud / Identity Sessions & Disable Accounts"]
        C2 --> C3["Block Malicious IPs / Domains on Perimeter Firewalls"]
    end

    subgraph "Phase 2: Eradication"
        E1["Kill Malicious Processes & Delete Persistence Reg Keys"] --> E2["Remove Dropped Backdoors & Web Shells"]
        E2 --> E3["Patch Root-Cause Vulnerability (Apply CVE Security Update)"]
    end

    subgraph "Phase 3: Recovery"
        R1["Restore Systems from Known-Good Offline Backups"] --> R2["Force Enterprise Password Reset for Compromised Personas"]
        R2 --> R3["Gradual Reconnection to Production Network under Heightened Monitoring"]
    end

    Phase 1: Containment --> Phase 2: Eradication
    Phase 2: Eradication --> Phase 3: Recovery
```

---

### 2. Standard 8-Point Operational Breakdown

#### 1. Trigger
- Confirmation of an active threat actor inside the enterprise network (malware execution, credential dumping, lateral movement, or data exfiltration).

#### 2. Responsible Parties (RACI Matrix)
- **Accountable**: Incident Commander / SOC Manager.
- **Responsible**: Incident Response Team, EDR Administrators, Network Engineers.
- **Consulted**: Infrastructure Server Admins, Cloud Engineers.
- **Informed**: Executive Leadership, Legal, Communications.

#### 3. Required Tools & Information
- **Tools**: EDR Console, Perimeter Firewall Management (Palo Alto Panorama), Microsoft Entra ID Admin, Backup/Recovery System (Veeam / Commvault).
- **Inputs**: List of compromised hostnames, compromised accounts, external C2 indicators (IPs, domains).

#### 4. Step-by-Step Execution Procedure
1. **Network Containment (Minutes 0–15)**:
   - Click **Isolate Host** in EDR console for all infected machines.
   - Push emergency firewall rule blocking outbound traffic to attacker C2 IP addresses.
2. **Identity Containment (Minutes 15–30)**:
   - Invalidate all active user tokens via PowerShell:
     `Revoke-MgUserSignInsSession -UserId "compromised.user@company.com"`
   - Disable user accounts and rotate passwords.
3. **Eradication (Hours 1–4)**:
   - Identify and delete all malware persistence mechanisms:
     - Delete scheduled tasks: `schtasks /delete /tn "MaliciousUpdater" /f`
     - Remove registry Run keys.
   - Carve and securely delete dropped binaries and staging folders (`C:\ProgramData\...`).
   - Patch the underlying vulnerability (e.g., apply Windows Cumulative Security Update).
4. **Recovery & Restoration (Hours 4–12)**:
   - If ransomware or deep rootkit activity occurred, **do not attempt to clean the OS**—wipe the machine completely and re-image from standard golden image, or restore from verified clean offline backup.
   - Reconnect machine to network segment.
   - Place host under **Heightened Monitoring Watchlist** in SIEM for 14 days.

#### 5. Decision Points & Logic Gates
- **IF** the compromised system is a mission-critical domain controller or healthcare life-support system, **THEN** consult Incident Commander prior to network isolation to evaluate operational patient/business impact.
- **IF** backups are suspected of being infected, **THEN** scan backup snapshots offline in an isolated sandbox prior to restoration.

#### 6. Escalation Matrix
- **Inability to Contain Spreading Malware**: Invoke Disaster Recovery (DR) and execute emergency enterprise network quarantine.

#### 7. Expected Outcome
- The adversary is 100% neutralized, zero persistence remains, systems are safely restored to production, and underlying entry vulnerabilities are sealed.

#### 8. Documentation & Audit Evidence
- Containment execution timestamps, firewall change ticket numbers, backup validation hashes, and post-restoration forensic integrity scan results.
