# 02. Detection Engineering and Sigma Rules

[⬅️ Prev: Threat Hunting](./01_Proactive_Threat_Hunting_Methodology.md) | [Back to Advanced Topics](./README.md) | [Next: SOAR Playbooks ➡️](./03_SOAR_Automation_Playbooks_and_Orchestration.md)

---

### 1. What is Sigma?
**Sigma** is an open-source, vendor-agnostic signature format for log events (similar to what Snort is for network packets or Yara for files). A detection engineer writes a rule once in YAML, and a converter compiles it into KQL for Sentinel, SPL for Splunk, or Elastic Query DSL.

#### Example Sigma Rule:
```yaml
title: Suspicious Certutil Download Command
id: 4a2b9182-1234-5678-abcd-ef0123456789
status: production
description: Detects the use of certutil.exe to download files from the internet, often used to bypass firewalls.
author: Enterprise SOC Engineering
references:
    - https://attack.mitre.org/techniques/T1105/
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        Image|endswith: '\certutil.exe'
        CommandLine|contains:
            - '-urlcache'
            - '-split'
    condition: selection
falsepositives:
    - Highly rare in standard corporate workstation use.
level: high
tags:
    - attack.ingress_tool_transfer
    - attack.t1105
```
