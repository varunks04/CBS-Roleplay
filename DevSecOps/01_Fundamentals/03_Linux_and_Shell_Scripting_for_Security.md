# 03. Linux and Shell Scripting for DevSecOps Automation

[⬅️ Prev: SDLC vs SSDLC](./02_SDLC_vs_Secure_SDLC_SSDLC.md) | [Back to Fundamentals](./README.md) | [Next: Git & GitHub Workflows ➡️](./04_Git_and_GitHub_Workflows_and_Branching.md)

---

### 1. Why DevSecOps Engineers Must Master Bash

CI/CD runners (GitHub Actions runners, GitLab runners, Jenkins agents) almost exclusively run Linux containers or virtual machines. When a DevSecOps engineer writes a pipeline step, they are executing shell commands.

---

### 2. The Golden Rules of Secure Shell Scripting

When writing automation scripts for security pipelines, you must write resilient, secure Bash:

```bash
#!/usr/bin/env bash
# Strict Bash Mode: Essential for CI/CD pipelines
set -euo pipefail
IFS=$'
	'

# -e: Exit immediately if any command exits with a non-zero status
# -u: Treat unset variables as an error and exit immediately
# -o pipefail: The return value of a pipeline is the status of the last command to exit with a non-zero status
# IFS: Prevents accidental word splitting on spaces

echo "=== Running Security Automation Check ==="

TARGET_DIR="${1:-.}"

if [ ! -d "$TARGET_DIR" ]; then
    echo "[-] Error: Directory $TARGET_DIR does not exist!" >&2
    exit 1
fi

echo "[+] Scanning directory: $TARGET_DIR"
```

---

### 3. Exit Codes and Pipeline Gating

In Unix systems, every process terminates with an **Exit Code** (or return code):
- `0`: Success / Clean execution.
- `Non-zero (1 to 255)`: Failure or error.

A CI/CD pipeline evaluates the exit code of every security tool:
- If Semgrep finds a Critical SQL Injection, it exits with `exit 1`. The CI runner detects the non-zero status and **fails the build immediately**, preventing deployment.
- If no critical vulnerabilities are found, Semgrep exits with `exit 0`, and the pipeline proceeds to the next stage.
