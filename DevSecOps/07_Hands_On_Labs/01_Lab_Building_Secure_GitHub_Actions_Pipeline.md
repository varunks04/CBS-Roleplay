# Lab 01: Building a Production-Grade Secure GitHub Actions Pipeline

[⬅️ Back to Labs](./README.md) | [Next: Custom Semgrep Rules ➡️](./02_Lab_Semgrep_Custom_Rule_Creation.md)

---

### Objective
Create a complete `.github/workflows/security.yml` pipeline that runs automated secret scanning, Semgrep SAST, Trivy vulnerability checks, and uploads standard SARIF results to the GitHub Security tab.

### The Complete Production Pipeline YAML:

```yaml
name: DevSecOps Automated Security Pipeline

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

permissions:
  contents: read
  security-events: write

jobs:
  # =========================================================================
  # STAGE 1: Secret Scanning
  # =========================================================================
  secrets-scan:
    name: Secret Leak Detection (TruffleHog)
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1
        with:
          fetch-depth: 0

      - name: Run TruffleHog OSS
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: ${{ github.event.repository.default_branch }}
          head: HEAD
          extra_args: --only-verified

  # =========================================================================
  # STAGE 2: Static Application Security Testing (SAST)
  # =========================================================================
  sast-scan:
    name: Semgrep SAST
    runs-on: ubuntu-latest
    needs: secrets-scan
    steps:
      - name: Checkout Code
        uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11

      - name: Run Semgrep OSS
        run: |
          docker run --rm -v "${{ github.workspace }}:/src" returntocorp/semgrep \
            semgrep scan --config auto --sarif --output /src/semgrep.sarif

      - name: Upload SARIF to GitHub Security Tab
        uses: github/codeql-action/upload-sarif@v3
        if: always()
        with:
          sarif_file: semgrep.sarif

  # =========================================================================
  # STAGE 3: Dependency Vulnerability Scanning (SCA)
  # =========================================================================
  sca-scan:
    name: Trivy Dependency Scan
    runs-on: ubuntu-latest
    needs: secrets-scan
    steps:
      - name: Checkout Code
        uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11

      - name: Run Trivy Vulnerability Scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          ignore-unfixed: true
          severity: 'CRITICAL,HIGH'
          exit-code: '1' # Fails the pipeline if Critical/High vulnerabilities exist!
```
