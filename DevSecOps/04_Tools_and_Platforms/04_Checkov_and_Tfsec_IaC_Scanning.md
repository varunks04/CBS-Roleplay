# 04. Checkov and Tfsec for Infrastructure as Code (IaC) Scanning

[⬅️ Prev: Snyk & Trivy](./03_Snyk_and_Trivy_SCA_and_Vulnerability_Scanning.md) | [Back to Tools](./README.md) | [Next: Secrets Management Tools ➡️](./05_HashiCorp_Vault_and_GitGuardian_Secrets.md)

---

### 1. Platform Overview

**Checkov** (by Bridgecrew/Palo Alto) is a static code analysis tool for Infrastructure as Code (IaC). It scans Terraform, CloudFormation, Kubernetes, Helm, and ARM templates for hundreds of enterprise security and compliance best practices.

### 2. Checkov CLI Pipeline Scanning Example

```bash
# Scan Terraform directory; output JUnit test report for CI/CD results
checkov -d ./terraform --framework terraform --output junitxml > test-results.xml
```
