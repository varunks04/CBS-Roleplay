# 02. Logs, Log Sources, and Event Analysis

[⬅️ Prev: Telemetry](./01_Security_Monitoring_and_Telemetry.md) | [Back to Core Concepts](./README.md) | [Next: Cyber Kill Chain ➡️](./03_Cyber_Kill_Chain_and_Unified_Kill_Chain.md)

---

### 1. Definition and Anatomy of a Security Log

A **Log** is a computer-generated, timestamped record documenting an event that occurred within an operating system, software application, or network device.

#### Anatomical Structure of a Raw Syslog Message:
```text
<134>1 2026-11-20T06:12:45.102Z fw01.corp.contoso.com filterlog - - - [drop src="203.0.113.55" dst="192.168.1.10" proto="TCP" sport="54128" dport="445" reason="SMB_Inbound_Blocked"]
```

---

### 2. Critical Log Engineering Concepts

1. **Log Normalization (Schema Mapping)**:
   - Cisco firewalls call the source IP `src_ip`. Palo Alto calls it `src`. Windows calls it `IpAddress`. AWS calls it `sourceIPAddress`.
   - SIEM platforms use Common Information Models (OSSF, CIM, ECS) to normalize all incoming variations into a single universal field: `source.ip`. This allows analysts to write one query that works across all vendors.
2. **Time Synchronization (NTP)**:
   - If server A is off by 4 minutes and server B is off by 2 minutes, reconstructing an attack timeline is impossible. Every enterprise machine must synchronize with trusted Network Time Protocol (NTP) servers.
3. **Log Ingestion Pipeline**:
   - `Source ➔ Log Agent (Fluentd/Splunk Universal Forwarder) ➔ Transport (Kafka/Syslog-ng) ➔ SIEM Storage & Indexing`.
