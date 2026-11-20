# 01. DevSecOps Technical Interview Questions and Model Answers

[⬅️ Back to Interview Prep](./README.md) | [Next: Pipeline Design Scenarios ➡️](./02_DevSecOps_Pipeline_Design_Scenarios.md)

---

### Q1: What is the difference between SAST and DAST? Where does each belong in a CI/CD pipeline?
**Model Answer**:
- **SAST (Static Application Security Testing)** is a white-box testing methodology that analyzes source code, bytecode, or AST without executing the application. It runs early in the pipeline (Code/Build stage) and finds issues like SQL injection flaws, hardcoded secrets, and buffer overflows. It is fast and has high code coverage, but cannot detect runtime environment or infrastructure flaws.
- **DAST (Dynamic Application Security Testing)** is a black-box testing methodology that attacks a running application over HTTP/HTTPS from the outside without knowledge of the underlying source code. It runs later in the pipeline (Staging/QA deployment stage) and finds runtime issues like authentication bypass, cross-site scripting (XSS), TLS misconfigurations, and CORS headers.
- **Where they belong**: SAST belongs on every PR commit to provide immediate feedback to developers. DAST belongs in staging integration testing before production deployment.

---

### Q2: Why should you avoid storing static AWS access keys in GitHub Secrets, and what is the modern replacement?
**Model Answer**:
Storing static long-lived credentials (`AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY`) poses severe risks of credential theft. If an attacker compromises a third-party pipeline dependency, the key can be printed to logs or exfiltrated, giving permanent administrative access until manually rotated.
**Modern Replacement**: **OpenID Connect (OIDC) Workload Identity Federation**. GitHub Actions acts as an OIDC token provider. When a workflow runs, GitHub generates a short-lived, cryptographically signed JSON Web Token (JWT). The runner presents this token to AWS Security Token Service (STS) using `sts:AssumeRoleWithWebIdentity`. AWS validates the token signature, checks repository and branch trust conditions, and issues an ephemeral IAM session credential that expires automatically in 15 to 60 minutes. No long-lived credentials exist to be compromised.

---

### Q3: What is a Container Escape, and how do you harden Docker against it?
**Model Answer**:
A container escape occurs when a process running inside a container breaks through the container isolation boundary (namespaces/cgroups) and gains execution or root access on the underlying host operating system kernel.
**Hardening Controls**:
1. Run containers as an unprivileged non-root user (`USER 10001`).
2. Drop all Linux capabilities using `--cap-drop=ALL` (strip `CAP_SYS_ADMIN`).
3. Set the root filesystem to read-only (`--read-only`).
4. Prevent privilege escalation flags (`--security-opt=no-new-privileges:true`).
5. Use minimal base images (Distroless or Alpine) to eliminate binaries like `curl`, `sh`, and `sudo`.
6. Enforce Kubernetes Pod Security Standards (`Restricted` profile) via admission controllers like Kyverno.
