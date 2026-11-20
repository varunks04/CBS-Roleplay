# 01. Proactive Threat Hunting Methodology

[⬅️ Back to Advanced Topics](./README.md) | [Next: Detection Engineering ➡️](./02_Detection_Engineering_and_Sigma_Rules.md)

---

### 1. Threat Hunting vs Alert Triage

- **Alert Triage (Reactive)**: Waiting for the SIEM or EDR bell to ring, then checking the alert.
- **Threat Hunting (Proactive)**: Starting with the assumption: *"An attacker is ALREADY inside our network right now, and no alarm has gone off. How do I find them?"*

---

### 2. The Hypothesis-Driven Threat Hunting Cycle

```mermaid
flowchart TD
    A["1. Formulate Hypothesis<br>(e.g., 'Attackers are using living-off-the-land binaries (certutil) to download payloads')"] --> B["2. Identify Data Requirements<br>(Sysmon Event ID 1 / CommandLine logs)"]
    B --> C["3. Execute Investigation & Data Analytics<br>(Write queries, filter out known IT baseline tools)"]
    C --> D{"Did we discover Malicious Activity?"}
    D -->|Yes: Intrusion Found!| E["Initiate Incident Response Process"]
    D -->|No: Environment is Clean| F["Document Findings & Baseline"]
    E & F --> G["4. Automate Detection<br>(Convert successful hunt query into a permanent SIEM rule)"]
```
