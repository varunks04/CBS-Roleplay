# 05. HashiCorp Vault for Machine Identities & Secrets Deep Dive

[⬅️ Prev: CyberArk](./04_CyberArk_Privileged_Access_Security.md) | [Back to Tools](./README.md) | [Next: Workflows Track ➡️](../05_Workflows_and_Processes/README.md)

---

### 1. Platform Overview

While CyberArk focuses primarily on human administrative access to infrastructure, **HashiCorp Vault** is purpose-built for **Machine-to-Machine Identity (NHI)**, API secrets, cryptographic key lifecycle management, and dynamic credential generation in cloud-native containerized architectures.

---

### 2. Core Secrets Engines & Capabilities

```mermaid
graph TD
    Vault["HashiCorp Vault Core"]
    
    Vault --> KV["1. KV Secrets Engine<br>Encrypted key-value store with versioning"]
    Vault --> Dyn["2. Dynamic Secrets Engine<br>Generates ephemeral DB users on demand (TTL: 1h)"]
    Vault --> PKI["3. PKI Secrets Engine<br>Acts as internal CA, issuing short-lived X.509 certs"]
    Vault --> Transit["4. Transit Secrets Engine<br>Cryptography-as-a-Service: Encrypts data in-transit"]
```

#### A. Dynamic Database Credentials
Instead of storing a static database password in application configuration files:
1. Application authenticates to Vault using its Kubernetes ServiceAccount token.
2. Requests database credentials for PostgreSQL.
3. Vault connects to PostgreSQL, generates a random username (`v_token_app_842`), assigns specific SQL permissions, and returns credentials with a 1-hour Time-to-Live (TTL).
4. When the TTL expires, Vault executes `DROP USER v_token_app_842;` automatically.

#### B. PKI Engine (Short-Lived Mutual TLS)
Vault can issue internal X.509 certificates for microservices with lifetimes measured in hours or days rather than years, completely eliminating the risk of unrevoked expired certificates.

---

### 3. Practical Vault CLI & Production Workflow

```bash
# 1. Enable Database Secrets Engine
vault secrets enable database

# 2. Configure Vault connection to PostgreSQL
vault write database/config/my-postgres-db \
    plugin_name=postgresql-database-plugin \
    allowed_roles="read-only-app" \
    connection_url="postgresql://{{username}}:{{password}}@postgres.corp:5432/orders?sslmode=disable" \
    username="vault_admin" \
    password="VaultAdminPassword123!"

# 3. Define Role with automated SQL user creation template
vault write database/roles/read-only-app \
    db_name=my-postgres-db \
    creation_statements="CREATE ROLE \"{{name}}\" WITH LOGIN PASSWORD '{{password}}' VALID UNTIL '{{expiration}}'; GRANT SELECT ON ALL TABLES IN SCHEMA public TO \"{{name}}\";" \
    default_ttl="1h" \
    max_ttl="24h"

# 4. Application fetches dynamic credentials
vault read database/creds/read-only-app
```
