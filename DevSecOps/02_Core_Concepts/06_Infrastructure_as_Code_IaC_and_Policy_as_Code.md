# 06. Infrastructure as Code (IaC) and Policy-as-Code

[⬅️ Prev: Docker Security Basics](./05_Containerization_and_Docker_Security_Basics.md) | [Back to Core Concepts](./README.md) | [Next: Technologies & Architectures Track ➡️](../03_Technologies_and_Architectures/README.md)

---

### 1. What is Infrastructure as Code (IaC)?

**Infrastructure as Code (IaC)** is the practice of managing and provisioning computing infrastructure (servers, databases, load balancers, VPC networks, S3 buckets) through machine-readable definition files (such as **Terraform**, AWS CloudFormation, or Bicep) rather than physical hardware configuration or manual web console clicking.

---

### 2. The Danger of IaC Misconfigurations

A single line of erroneous Terraform code can instantly expose customer data to the entire public internet:

```hcl
# DANGEROUS TERRAFORM CODE: Public S3 Bucket with No Encryption
resource "aws_s3_bucket" "customer_data" {
  bucket = "company-sensitive-customer-data"
  # MISCONFIGURATION 1: Public Read
  acl    = "public-read"
}

resource "aws_s3_bucket_public_access_block" "public" {
  bucket = aws_s3_bucket.customer_data.id

  # MISCONFIGURATION 2: Public access allowed!
  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}
```

---

### 3. Policy-as-Code (PaC)

**Policy-as-Code** means writing security rules as software code that can be automatically executed against IaC templates before deployment.

Using tools like **Checkov** or **Open Policy Agent (OPA)** with Rego:

```rego
# OPA Rego Policy: Block any S3 bucket without server-side encryption
package cloud.aws.s3

default allow = false

allow {
    input.resource.aws_s3_bucket_server_side_encryption_configuration[_]
}

deny[msg] {
    not allow
    msg := "Security Policy Violation: Every S3 bucket must have server-side encryption enabled!"
}
```
