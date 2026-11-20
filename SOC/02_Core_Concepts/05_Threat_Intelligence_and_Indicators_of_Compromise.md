# 05. Threat Intelligence and Indicators of Compromise (IOCs)

[⬅️ Prev: MITRE ATT&CK](./04_MITRE_ATTACK_Framework_in_Practice.md) | [Back to Core Concepts](./README.md) | [Next: Vulnerability Management vs IR ➡️](./06_Vulnerability_Management_vs_Incident_Response.md)

---

### 1. The Pyramid of Pain

Created by David Bianco, the **Pyramid of Pain** illustrates how difficult it is for a defender to block an adversary artifact, and conversely, how much pain that defensive block inflicts on the threat actor:

```
                  ▲
                 /                 /   \     TTPs (Tactics, Techniques, Procedures)
               / TTP \    Pain to Attacker: TOUGH! Forces them to invent new hacking techniques.
              /───────             /  Tools  \   Pain to Attacker: CHALLENGING. They must rewrite software code.
            /───────────           / Network/Host\ Pain to Attacker: ANNOYING. Must register new C2 domains & IP proxies.
          /   Artifacts            /─────────────────        /   Domain Names    \ Pain to Attacker: SIMPLE. Cost $5 to buy a new domain.
       /─────────────────────      /      IP Addresses      \ Pain to Attacker: TRIVIAL. Can spin up a new cloud IP in 30 seconds.
     /─────────────────────────    /        Hash Values        \ Pain to Attacker: ZERO. Changing 1 character changes MD5/SHA256 completely.
   /─────────────────────────────```

---

### 2. Indicators of Compromise (IOCs) vs Indicators of Attack (IOAs)

| Dimension | Indicator of Compromise (IOC) | Indicator of Attack (IOA) |
| :--- | :--- | :--- |
| **Nature** | **Forensic Evidence (Reactive)**: Artifact left behind after an attack occurred. | **Behavioral Pattern (Proactive/Real-time)**: Intent and action observed while happening. |
| **Examples** | - Malware hash: `e3b0c44298fc1...`<br>- C2 IP: `198.51.100.14`<br>- Phishing sender: `evil@phish.com` | - A standard user executing PowerShell to dump memory from `lsass.exe`<br>- 500 outbound SSH attempts across internal subnets |
| **Lifespan** | Extremely short (hours to days). Attackers recompile code to change hashes instantly. | Long-lasting. Adversaries must follow certain behaviors regardless of tool names. |

---

### 3. Cyber Threat Intelligence (CTI) Levels

- **Strategic CTI**: High-level trends and threat actor motives for executives (e.g., *"Ransomware targeting healthcare increased 40%"*).
- **Operational CTI**: Details of specific campaigns and actor TTPs for architects (e.g., *"Lazarus Group is weaponizing trojanized PDF readers targeting aerospace engineers"*).
- **Tactical CTI**: Concrete technical IOCs (IPs, hashes, domains) fed directly into SIEM/firewall blocklists via STIX/TAXII feeds.
