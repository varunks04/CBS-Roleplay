# Lab 04: Scanning Terraform Code with Checkov

[⬅️ Prev: Trivy Lab](./03_Lab_Trivy_Container_Scanning_and_Dockerfile_Hardening.md) | [Back to Labs](./README.md) | [Next: Advanced Topics Track ➡️](../08_Advanced_Topics/README.md)

---

### Objective
Install Checkov, author an intentionally insecure Terraform template, execute an automated compliance scan, interpret the security findings, and remediate the code to pass the security gate.

---

### Step 1: Install Checkov
```bash
pip install checkov
```

### Step 2: Create Insecure Terraform Template (`main.tf`)
```hcl
provider "aws" {
  region = "us-east-1"
}

# VULNERABLE 1: Public S3 Bucket with unencrypted storage
resource "aws_s3_bucket" "insecure_bucket" {
  bucket = "my-test-insecure-storage-bucket"
  acl    = "public-read"
}

# VULNERABLE 2: Security Group allowing SSH (Port 22) to 0.0.0.0/0
resource "aws_security_group" "insecure_sg" {
  name        = "allow-ssh-from-world"
  description = "Allows SSH ingress from everywhere"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

### Step 3: Run Checkov Scan
```bash
checkov -f main.tf --framework terraform
```
*Checkov reports:*
- `FAILED for resource: aws_s3_bucket.insecure_bucket (CKV_AWS_20: S3 Bucket has an ACL allowing public read access)`
- `FAILED for resource: aws_security_group.insecure_sg (CKV_AWS_24: Ensure no security groups allow ingress from 0.0.0.0/0 to port 22)`

---

### Step 4: Remediate the Terraform Code (`main.tf`)
```hcl
resource "aws_s3_bucket" "secure_bucket" {
  bucket = "my-test-secure-storage-bucket"
}

resource "aws_s3_bucket_public_access_block" "public_block" {
  bucket = aws_s3_bucket.secure_bucket.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

resource "aws_security_group" "secure_sg" {
  name        = "allow-ssh-internal"
  description = "Allows SSH ingress strictly from corporate VPN"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["10.0.0.0/16"] # Restricted to internal VPN
  }
}
```
Re-run `checkov -f main.tf`: **PASSED: 100% compliance!**
