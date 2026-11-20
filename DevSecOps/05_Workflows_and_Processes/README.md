# 05. DevSecOps Operational Workflows and Standard Operating Procedures (SOPs)

This module provides real-world standard operating procedures for integrating and operating automated security in enterprise engineering teams.

Every operational procedure strictly complies with the **Enterprise 8-Point Operational Standard**:
1. **Trigger**
2. **Responsible Parties (RACI)**
3. **Required Tools & Inputs**
4. **Step-by-Step Procedure**
5. **Decisions & Logic Gates**
6. **Escalation Matrix**
7. **Expected Outcome**
8. **Documentation & Audit Artifacts**

---

## 📖 Module Contents

1. **[01_End_to_End_DevSecOps_Pipeline_Workflow.md](./01_End_to_End_DevSecOps_Pipeline_Workflow.md)** - Complete lifecycle execution from Git push to production deployment.
2. **[02_Developer_Pre_Commit_and_PR_Security_Check.md](./02_Developer_Pre_Commit_and_PR_Security_Check.md)** - Pre-commit hooks, IDE feedback, and Pull Request security gates.
3. **[03_Automated_Security_Gate_Pass_Fail_Criteria.md](./03_Automated_Security_Gate_Pass_Fail_Criteria.md)** - Establishing thresholds, CVSS cutoffs, and pipeline blocker criteria.
4. **[04_Vulnerability_Triage_and_Developer_Remediation.md](./04_Vulnerability_Triage_and_Developer_Remediation.md)** - Triaging SAST/DAST findings, filtering false positives, and tracking Jira remediation SLAs.
5. **[05_Dependency_Vulnerability_Management_and_Patching.md](./05_Dependency_Vulnerability_Management_and_Patching.md)** - Automated Dependabot/Renovate patching and emergency zero-day response.
6. **[06_Production_Runtime_Monitoring_and_Feedback_Loop.md](./06_Production_Runtime_Monitoring_and_Feedback_Loop.md)** - Runtime container protection (Falco), feedback loop into developer backlogs.
