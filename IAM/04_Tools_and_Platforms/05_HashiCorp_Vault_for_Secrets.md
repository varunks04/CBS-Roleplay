# 05. HashiCorp Vault for Secrets Management

[⬅️ Prev: CyberArk](./04_CyberArk_Privileged_Access_Security.md) | [Back to Tools](./README.md) | [Next: Workflows & Processes Track ➡️](../05_Workflows_and_Processes/README.md)

---

### 1. Platform Overview

**HashiCorp Vault** is a purpose-built platform for managing secrets, protecting sensitive data, and securing machine identities across dynamic cloud and containerized infrastructure.

### 2. Core Pillars

- **Dynamic Secrets**: Instead of giving an application a static database password that never changes, Vault generates an ephemeral database user on demand with a 1-hour Time-to-Live (TTL). When the app finishes, Vault automatically drops the user from the database.
- **Transit Secrets Engine**: "Cryptography as a Service"—encrypts sensitive application data (credit card numbers, PII) in transit without developers needing to write custom cryptographic algorithms.
- **PKI Secrets Engine**: Acts as an internal Certificate Authority (CA) generating short-lived X.509 certificates for microservice mutual TLS (mTLS).
