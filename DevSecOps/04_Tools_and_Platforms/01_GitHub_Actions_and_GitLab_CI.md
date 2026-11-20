# 01. GitHub Actions and GitLab CI/CD

[⬅️ Back to Tools](./README.md) | [Next: SonarQube & Semgrep SAST ➡️](./02_SonarQube_and_Semgrep_SAST.md)

---

### 1. Platform Overview
**GitHub Actions** and **GitLab CI/CD** are the two dominant cloud and on-premises pipeline automation platforms. Workflows are defined as declarative YAML files stored directly in the Git repository (`.github/workflows/` or `.gitlab-ci.yml`).

### 2. Securing the CI/CD Pipeline Itself

- **Pin Actions to Full Commit SHAs**: Never use mutable tags like `uses: actions/checkout@v3` (an attacker compromising the action maintainer can update tag `v3` with malicious code). Always use immutable SHA hashes: `uses: actions/checkout@f43a0e5ff2bd294095638e18286ca9a3d1956744`.
- **Enforce Minimal GITHUB_TOKEN Permissions**: By default, restrict token permissions to read-only:
  ```yaml
  permissions:
    contents: read
    security-events: write
  ```
