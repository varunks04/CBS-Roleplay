# 02. SonarQube and Semgrep: Static Application Security Testing (SAST)

[⬅️ Prev: GitHub Actions](./01_GitHub_Actions_and_GitLab_CI.md) | [Back to Tools](./README.md) | [Next: Snyk & Trivy SCA ➡️](./03_Snyk_and_Trivy_SCA_and_Vulnerability_Scanning.md)

---

### 1. Platform Comparison

- **SonarQube**: Enterprise code quality and security platform. Provides centralized web dashboards, historical code quality trends, and broad multi-language support.
- **Semgrep**: Ultra-fast, lightweight, open-source static analysis engine that looks like grep for code. Evaluates Abstract Syntax Trees (AST) using simple pattern matching syntax.

### 2. Semgrep in CI/CD Execution Example

```bash
# Run Semgrep with standard OWASP Top 10 ruleset and fail on high/critical findings
semgrep scan --config auto --error --severity ERROR
```
