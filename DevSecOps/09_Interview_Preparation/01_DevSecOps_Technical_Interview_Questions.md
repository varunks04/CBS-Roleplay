# 01. DevSecOps Technical Interview Questions and Model Answers

[⬅️ Back to Interview Prep](./README.md) | [Next: Pipeline Design Scenarios ➡️](./02_DevSecOps_Pipeline_Design_Scenarios.md)

---

### Q1: What is the difference between SAST, DAST, IAST, and SCA? Where does each belong in the SSDLC?
**Model Answer**:
- **SAST (Static Analysis)**: White-box testing analyzing uncompiled source code for programmatic flaws (SQLi, buffer overflows, insecure deserialization). Runs early in the pipeline (Code/Build stage) on every Git Pull Request.
- **SCA (Software Composition Analysis)**: Dependency testing analyzing third-party open-source libraries (`package.json`, `pom.xml`) for known CVEs and license risks. Runs in the Build stage alongside SAST.
- **DAST (Dynamic Analysis)**: Black-box testing attacking a deployed, running web service over HTTP/HTTPS from the outside without source code access. Runs in staging integration testing before production deployment.
- **IAST (Interactive Analysis)**: Gray-box testing utilizing an agent inside the application runtime to correlate active network traffic with line-of-code execution. Runs during automated QA and integration testing.

---

### Q2: How do you eliminate static AWS/Azure cloud credentials in CI/CD pipelines?
**Model Answer**:
By implementing **OpenID Connect (OIDC) Workload Identity Federation**. 
Instead of storing permanent static access keys (`AWS_ACCESS_KEY_ID`) in GitHub Secrets:
1. GitHub Actions acts as an OIDC token provider, generating a short-lived JSON Web Token (JWT) signed by GitHub's private key.
2. The CI/CD runner presents this JWT to AWS Security Token Service (STS) using `AssumeRoleWithWebIdentity`.
3. AWS validates the token's cryptographic signature against GitHub's public `jwks.json` endpoint and checks the repository trust policy (e.g., verifying `repo:org/app:ref:refs/heads/main`).
4. AWS issues temporary, ephemeral IAM session credentials valid for only 15 minutes.
5. Even if the build runner is compromised, no long-lived secrets exist to be exfiltrated.

---

### Q3: What is a Container Escape, and what kernel primitives prevent it?
**Model Answer**:
A container escape occurs when a process running inside a container breaks through the isolation boundary and executes commands on the underlying host operating system kernel.
**Protective Mechanisms**:
1. **Linux Namespaces**: Ensure process isolation (PID, Mount, Network, IPC, UTS).
2. **Cgroups (Control Groups)**: Limit CPU, memory, and I/O consumption to prevent denial-of-service.
3. **Drop Capabilities (`--cap-drop=ALL`)**: Specifically drop `CAP_SYS_ADMIN` which allows mounting filesystems and loading kernel modules.
4. **Read-Only Root Filesystem (`--read-only`)**: Prevents an attacker who achieves Remote Code Execution (RCE) from writing malicious binaries to `/bin` or `/tmp`.
5. **Non-Root Execution (`USER 10001`)**: Ensures the containerized process runs with an unprivileged UID.
6. **Seccomp (Secure Computing Mode)**: Filters allowable system calls down to a minimal whitelist.

---

### Q4: What is an SBOM, and why did it become mandatory under executive orders?
**Model Answer**:
A **Software Bill of Materials (SBOM)** is a machine-readable nested inventory of all software components, libraries, modules, and supply chain dependencies comprising an application. Standard formats include **CycloneDX** and **SPDX**.
Following major supply chain attacks like SolarWinds and the Log4j crisis, organizations spent weeks manually searching codebases to identify vulnerable packages. With an automated SBOM generated at build time, an organization can query its central catalog in under 60 seconds to identify every production service using a newly disclosed vulnerable component.
