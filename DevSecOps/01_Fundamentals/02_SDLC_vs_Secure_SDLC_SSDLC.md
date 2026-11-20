# 02. Software Development Life Cycle (SDLC) vs Secure SDLC (SSDLC)

[⬅️ Prev: DevSecOps Philosophy](./01_DevSecOps_Philosophy_and_Shift_Left.md) | [Back to Fundamentals](./README.md) | [Next: Linux & Scripting ➡️](./03_Linux_and_Shell_Scripting_for_Security.md)

---

### 1. Integrating Security into Every SDLC Phase

A **Secure Software Development Life Cycle (SSDLC)** is a framework that introduces security activities at every step of software creation:

```mermaid
flowchart TD
    subgraph "The SSDLC Framework"
        P1["1. Requirements & Planning"] --> S1["Security Activity: Threat Modeling (STRIDE) & Regulatory Compliance Mapping"]
        P2["2. Architecture & Design"] --> S2["Security Activity: Secure Architecture Review, Least Privilege Design"]
        P3["3. Implementation / Coding"] --> S3["Security Activity: IDE Linters, Secret Detection, Pre-commit Hooks"]
        P4["4. Testing & Verification"] --> S4["Security Activity: Automated SAST, SCA, DAST, and Container Scans in CI"]
        P5["5. Deployment & Release"] --> S5["Security Activity: IaC Security Scans, Cryptographic Image Signing (Cosign)"]
        P6["6. Maintenance & Ops"] --> S6["Security Activity: Runtime Defense (Falco), Continuous Vulnerability Scanning"]
    end

    P1 -.-> P2 -.-> P3 -.-> P4 -.-> P5 -.-> P6
```

---

### 2. Threat Modeling: The STRIDE Methodology

Before writing code, engineers use Microsoft's **STRIDE** model to predict how an adversary might attack the proposed architecture:

| Threat Category | Property Violated | Example Attack | Defensive Countermeasure |
| :--- | :--- | :--- | :--- |
| **S - Spoofing** | Authentication | Attacker pretends to be a valid user or service. | Mutual TLS (mTLS), FIDO2 MFA, OIDC JWT validation. |
| **T - Tampering** | Integrity | Attacker modifies API requests or database values. | HMAC signatures, input validation, TLS encryption. |
| **R - Repudiation** | Non-repudiation | User denies performing a fraudulent wire transfer. | Tamper-proof append-only audit logging, cryptographic signing. |
| **I - Information Disclosure** | Confidentiality | Database connection string leaked in error response. | Secrets management, masked error messages, encryption at rest. |
| **D - Denial of Service** | Availability | Attacker floods an API with 50,000 requests per second. | Rate limiting, Cloudflare DDoS shielding, auto-scaling. |
| **E - Elevation of Privilege**| Authorization | Regular customer executes administrative API endpoint. | Strict RBAC/ABAC checks at every API route handler. |
