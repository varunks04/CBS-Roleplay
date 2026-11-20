# 03. Automated Security Gate Pass/Fail Criteria SOP

[⬅️ Prev: PR Security Checks](./02_Developer_Pre_Commit_and_PR_Security_Check.md) | [Back to Workflows](./README.md) | [Next: Vulnerability Triage ➡️](./04_Vulnerability_Triage_and_Developer_Remediation.md)

---

### 1. Enterprise Threshold Matrix

A common failure in DevSecOps is failing pipelines on minor cosmetic flaws, causing developer rebellion. A mature security gate uses **risk-calibrated pass/fail thresholds**:

| Vulnerability Severity | Fix Available? | In Production Code? | Pipeline Action | Developer Remediation SLA |
| :--- | :--- | :--- | :--- | :--- |
| **CRITICAL (CVSS 9.0–10.0)** | Yes | Yes | 🚫 **FAIL BUILD (Blocker)** | $\le 24$ Hours |
| **HIGH (CVSS 7.0–8.9)** | Yes | Yes | 🚫 **FAIL BUILD (Blocker)** | $\le 7$ Days |
| **MEDIUM (CVSS 4.0–6.9)** | Any | Yes | ⚠️ **PASS WITH WARNING** (Create Jira Ticket) | $\le 30$ Days |
| **LOW (CVSS 0.1–3.9)** | Any | Yes | ℹ️ **PASS (Informational Log)** | $\le 90$ Days |
| **CRITICAL / HIGH** | **NO FIX EXISTS** | Yes | ⚠️ **PASS WITH DOCUMENTED EXCEPTION** | Requires AppSec Risk Sign-off |

---

### 2. Standard 8-Point Operational Breakdown

#### 1. Trigger
- Security scan tools (SAST, SCA, Container, IaC) finish processing during the CI/CD pipeline run.

#### 2. Responsible Parties (RACI Matrix)
- **Accountable**: Enterprise CISO & Head of Software Engineering.
- **Responsible**: Automated CI Quality Gate Engine.
- **Consulted**: Application Security Architect.
- **Informed**: Product Manager, Lead Developer.

#### 3. Required Tools & Information
- **Tools**: SonarQube Quality Gates, Snyk / Trivy CLI, GitHub Branch Protection.
- **Inputs**: Scan results JSON/SARIF file, CVSS scores, Exploit Prediction Scoring System (EPSS) scores, Fix availability flags.

#### 4. Step-by-Step Execution Procedure
1. **Result Parsing**: Pipeline step parses scanner JSON output using `jq`.
2. **Threshold Evaluation**:
   ```bash
   CRITICAL_COUNT=$(jq '[.Results[].Vulnerabilities[] | select(.Severity=="CRITICAL" and .FixedVersion != null)] | length' scan_results.json)
   if [ "$CRITICAL_COUNT" -gt 0 ]; then
       echo "[-] Security Gate Failed: Found $CRITICAL_COUNT fixable Critical vulnerabilities!"
       exit 1
   fi
   ```
3. **Quality Gate Decision**:
   - If count is 0, exit with `exit 0` (Pipeline passes).
   - If count > 0, exit with `exit 1` (Pipeline halts).

#### 5. Decision Points & Logic Gates
- **IF** vulnerability is in test code (`tests/` directory), **THEN** do not fail production gate.
- **IF** vulnerability has no vendor fix available, **THEN** check if compensatory control exists (e.g., WAF rule); if so, grant 14-day temporary waiver.

#### 6. Escalation Matrix
- **Emergency Hotfix Override**: Requires joint digital signature approval from Lead Architect and AppSec Lead.

#### 7. Expected Outcome
- Predictable, transparent build gating that protects production without needlessly stopping developer workflows.

#### 8. Documentation & Audit Evidence
- Quality gate pass/fail JSON payload archived with build artifacts; exception waiver tickets documented in Jira.
