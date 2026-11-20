# 04. Checkov and Tfsec for Infrastructure as Code (IaC) Scanning Deep Dive

[⬅️ Prev: Snyk & Trivy](./03_Snyk_and_Trivy_SCA_and_Vulnerability_Scanning.md) | [Back to Tools](./README.md) | [Next: Secrets Tools ➡️](./05_HashiCorp_Vault_and_GitGuardian_Secrets.md)

---

### 1. Platform Overview & Architecture

**Checkov** (by Bridgecrew / Palo Alto Networks) is an open-source static code analysis tool for **Infrastructure as Code (IaC)**. It evaluates Terraform, AWS CloudFormation, Kubernetes manifests, Helm charts, and Azure Resource Manager (ARM) templates against thousands of security best-practice policies.

```mermaid
flowchart LR
    IaC["Terraform Files (*.tf)<br>Kubernetes YAMLs"] --> Checkov["Checkov Policy Engine"]
    
    subgraph "Policy Evaluation"
        Checkov --> P1["CIS Benchmark Checks"]
        Checkov --> P2["AWS / Azure / GCP Well-Architected Policies"]
        Checkov --> P3["Custom Python & YAML Policies"]
    end

    Checkov --> Decision{"Violations Found?"}
    Decision -->|Failed (Exit 1)| Block["🚫 Halt CI/CD Pipeline & Block Merge"]
    Decision -->|Passed (Exit 0)| Deploy["✅ Proceed to Terraform Plan / Apply"]
```

---

### 2. Practical Checkov CLI Usage in CI/CD

```bash
# 1. Scan Terraform code directory and show only failed checks
checkov -d ./terraform --framework terraform --compact

# 2. Enforce strict exit code on High and Critical findings only
checkov -d ./terraform --framework terraform --check HIGH,CRITICAL --soft-fail-on LOW,MEDIUM

# 3. Export findings to standard JUnit XML (for Jenkins/GitLab test dashboards)
checkov -d ./terraform --output junitxml > checkov-report.xml
```

---

### 3. Managing Policy Exceptions & Suppressions

When a business requirement necessitates an exception (e.g., an S3 bucket hosting a public static documentation website), you must **never disable the scanner globally**. Suppress the rule inline with an explicit justification:

```hcl
resource "aws_s3_bucket" "public_docs" {
  bucket = "company-public-documentation"

  # checkov:skip=CKV_AWS_20: "Approved public website bucket - Ticket SEC-10492"
  acl    = "public-read"
}
```
