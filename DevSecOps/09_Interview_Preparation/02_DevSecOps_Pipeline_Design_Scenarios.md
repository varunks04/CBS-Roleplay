# 02. DevSecOps Pipeline Design Scenarios

[⬅️ Prev: Technical Questions](./01_DevSecOps_Technical_Interview_Questions.md) | [Back to Interview Prep](./README.md)

---

### Scenario 1: Designing an Enterprise Zero-Trust CI/CD Pipeline
**Prompt**: *"Design an automated CI/CD pipeline for a financial microservices application running on AWS EKS that ensures zero secrets are stored in Git, stops vulnerable code before production, and guarantees artifact provenance."*

**Model Strategy**:
1. **Pre-Commit / IDE**: Gitleaks hook catches secrets before commit; IDE plugins provide real-time SAST feedback.
2. **Pre-Build Gate**: TruffleHog scans PR commit diffs for secrets.
3. **Build & Code Analysis**: Semgrep executes SAST; Trivy parses `package-lock.json` for dependency CVEs. Fails on Critical/High with available fix.
4. **Hardened Container Build**: Multi-stage Dockerfile compiling into Google Distroless base image running as non-root UID 10001.
5. **Image Scanning & Signing**: Trivy scans the compiled image. Cosign signs the image hash using GitHub Actions OIDC keyless authentication and pushes the signature to Amazon ECR.
6. **Infrastructure as Code**: Checkov scans Terraform manifests, enforcing encryption, private subnets, and least-privilege IAM roles.
7. **Admission Control**: Kubernetes Kyverno policy enforces that only images cryptographically signed by the corporate Cosign key can run in the cluster.
