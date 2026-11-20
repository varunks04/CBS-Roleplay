# Case Study 05: DNS Exfiltration and C2 Beaconing

[⬅️ Prev: Pass-the-Hash](./04_Scenario_Internal_Brute_Force_and_Pass_The_Hash.md) | [Back to Scenarios](./README.md) | [Next: Hands-On Labs Track ➡️](../07_Hands_On_Labs/README.md)

---

### 1. Detection via Network Analytics
Zeek/Corelight network sensors detect an anomaly: A single host is generating 20 DNS queries per minute to subdomains of `data-sync-cloud.biz`, with each subdomain containing high-entropy random characters:
- `dGhpcyBpcyBhIHNlY3JldA.data-sync-cloud.biz`
- `ZXhmaWx0cmF0aW9uIGRhdGE.data-sync-cloud.biz`

```mermaid
sequenceDiagram
    autonumber
    actor InfectedHost as Infected Host (10.0.4.15)
    participant CorpDNS as Internal Corporate DNS Server
    participant RootDNS as Root / Authoritative Nameservers
    participant AttackerDNS as Attacker Authoritative Nameserver

    InfectedHost->>CorpDNS: 1. Query: TXT dGhpcyBpcyBhIHNlY3JldA.data-sync-cloud.biz
    CorpDNS->>RootDNS: 2. Resolves delegation for data-sync-cloud.biz
    RootDNS-->>CorpDNS: 3. Nameserver is ns1.attacker.com
    CorpDNS->>AttackerDNS: 4. Forwards DNS Query to Attacker Nameserver
    Note over AttackerDNS: Attacker extracts Base64 string from query: "this is a secret"
    AttackerDNS-->>CorpDNS: 5. Replies with dummy IP: 1.1.1.1
    CorpDNS-->>InfectedHost: 6. Returns IP (Data exfiltrated successfully over Port 53!)
```

### 2. Defense and Remediation
- Configure DNS Firewalls / Umbrella to block dynamic non-business subdomains.
- Enforce strict packet inspection on UDP 53 to block DNS tunneling payloads exceeding standard character lengths.
