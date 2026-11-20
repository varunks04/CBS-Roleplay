# 04. Digital Forensics and Incident Response (DFIR)

[⬅️ Prev: SOAR Automation](./03_SOAR_Automation_Playbooks_and_Orchestration.md) | [Back to Advanced Topics](./README.md) | [Next: Interview Prep ➡️](../09_Interview_Preparation/README.md)

---

### 1. Definition and Purpose

While front-line SOC analysts triage alerts in real time, **Digital Forensics and Incident Response (DFIR)** specialists perform post-compromise deep-dive investigations. They extract volatile artifacts, rebuild microscopic attack timelines, analyze non-volatile disk images, and preserve legally defensible evidence for law enforcement and regulatory litigation.

---

### 2. The Order of Volatility (RFC 3227)

When arriving at a compromised crime scene, an investigator must collect evidence in order from **most volatile** (disappears within milliseconds of power loss) to **least volatile** (persists indefinitely on disk):

```
[MOST VOLATILE - COLLECT FIRST]
  1. CPU registers, cache contents
  2. Routing tables, ARP cache, process table, kernel memory statistics
  3. Temporary file systems, swap space, volatile RAM memory
  4. Non-volatile local disk storage (NVMe/SSD/HDD)
  5. Remote logging data, network topology configurations
  6. Physical archive media (tapes, optical discs, offline backups)
[LEAST VOLATILE - COLLECT LAST]
```

---

### 3. Essential Forensic Artifacts on Windows

| Artifact | Location | What It Proves to the Investigator |
| :--- | :--- | :--- |
| **Shimcache (AppCompatCache)** | `SYSTEM` Registry Hive | Proves that a specific executable binary **existed and was executed** on the host, including path and file size. |
| **Amcache.hve** | `C:\Windowsppcompat\Programs\Amcache.hve` | Contains program metadata, compilation timestamps, and SHA-1 hashes of executed applications. |
| **Prefetch Files (`.pf`)** | `C:\Windows\Prefetch` | Records process execution count, first run timestamp, last 8 run timestamps, and DLL dependencies loaded. |
| **UserAssist** | `NTUSER.DAT` Registry Hive | Records applications run via the Windows Explorer GUI with ROT13-encoded names. |
| **Master File Table ($MFT)**| NTFS Root | Complete filesystem metadata catalog documenting file creation ($STANDARD_INFORMATION vs $FILE_NAME timestamps to detect **Timestomping**). |

---

### 4. Volatility 3 Memory Forensics Workflow

When an attacker uses in-memory living-off-the-land techniques without touching the disk, **RAM Memory Analysis** using Volatility 3 is required:

```bash
# 1. Inspect running process tree from memory dump
vol -f memory.raw windows.pstree

# 2. Scan for hidden or unlinked processes (DKOM - Direct Kernel Object Manipulation)
vol -f memory.raw windows.psscan

# 3. Detect injected shellcode and unmapped memory sections (Meterpreter / Cobalt Strike)
vol -f memory.raw windows.malfind

# 4. Extract command line arguments passed to processes
vol -f memory.raw windows.cmdline

# 5. Dump suspicious process memory for static Yara analysis
vol -f memory.raw -o ./output windows.dumpfiles --pid 4812
```
