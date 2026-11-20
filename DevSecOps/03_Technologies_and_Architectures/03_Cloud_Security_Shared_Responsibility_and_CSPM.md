# 03. Cloud Security, Shared Responsibility, and CSPM

[⬅️ Prev: Kubernetes Security](./02_Kubernetes_Security_Architecture_and_Admission_Control.md) | [Back to Technologies](./README.md) | [Next: Zero Trust CI/CD Runners ➡️](./04_Zero_Trust_CI_CD_Runner_Security_and_OIDC.md)

---

### 1. The Cloud Shared Responsibility Model

A critical error beginners make is assuming that moving to AWS, Azure, or GCP makes their infrastructure automatically secure. Cloud providers enforce the **Shared Responsibility Model**:
- **The Provider is responsible for Security OF the Cloud** (Physical datacenters, hardware servers, hypervisor, core network infrastructure).
- **The Customer is responsible for Security IN the Cloud** (Customer data, IAM policies, OS patching, network firewall rules, encryption, and application code).

---

### 2. What is CSPM (Cloud Security Posture Management)?

**CSPM** tools (e.g., Wiz, Prisma Cloud, Microsoft Defender for Cloud) continuously monitor multi-cloud environments via read-only API calls to detect security misconfigurations, compliance drift, and toxic permission combinations:
- Unencrypted storage buckets (S3, Azure Blob).
- Security groups with port 22 (SSH) or 3389 (RDP) open to `0.0.0.0/0`.
- IAM users with administrator privileges lacking MFA.
