# 04. Zero Trust CI/CD Runner Security and OIDC Workload Federation

[⬅️ Prev: Cloud Security](./03_Cloud_Security_Shared_Responsibility_and_CSPM.md) | [Back to Technologies](./README.md) | [Next: Tools & Platforms Track ➡️](../04_Tools_and_Platforms/README.md)

---

### 1. Eliminating Long-Lived Cloud Credentials in CI/CD

Historically, to allow GitHub Actions to deploy infrastructure to AWS or Azure, engineers created a dedicated IAM user, generated static `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`, and pasted them into GitHub repository secrets.

**The Catastrophic Vulnerability**: If a malicious dependency or compromised action executes inside the pipeline, it dumps the environment variables and permanently steals the administrative AWS keys!

---

### 2. The Modern Solution: OIDC Workload Identity Federation

Using OpenID Connect (OIDC), **zero static secrets are stored in GitHub**:

```mermaid
sequenceDiagram
    autonumber
    participant Runner as GitHub Actions Runner
    participant GH_OIDC as GitHub OIDC Token Provider
    participant AWS_STS as AWS Security Token Service (STS)
    participant AWS_S3 as AWS S3 / Cloud Resources

    Runner->>GH_OIDC: 1. Requests short-lived OIDC JSON Web Token (JWT)
    GH_OIDC-->>Runner: 2. Returns signed JWT containing claims: {repo: "org/app", ref: "refs/heads/main"}
    Runner->>AWS_STS: 3. AssumeRoleWithWebIdentity(JWT, TargetRoleARN)
    AWS_STS->>AWS_STS: 4. Validates GitHub signature using GitHub JWKS; checks repo trust policy
    AWS_STS-->>Runner: 5. Issues EPHEMERAL temporary credentials (valid for 15 minutes)
    Runner->>AWS_S3: 6. Deploys app to AWS using temporary session
    Note over Runner,AWS_S3: At 15 minutes: Credentials die automatically!
```
