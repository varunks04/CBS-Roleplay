# Case Study 01: Leaked AWS API Key in Git Commit

[⬅️ Back to Scenarios](./README.md) | [Next: Log4j Crisis ➡️](./02_Scenario_Log4j_Zero_Day_Dependency_Crisis.md)

---

### 1. Incident Timeline
- **11:04 AM**: A junior developer debugging an S3 upload hardcodes `AWS_ACCESS_KEY_ID="AKIA..."` and `AWS_SECRET_ACCESS_KEY="wJalr..."` in `app.js`.
- **11:05 AM**: Developer commits and pushes the branch to an enterprise public repository.
- **11:06 AM**: Threat actor scanning bots scraping GitHub discover the key within 45 seconds.
- **11:07 AM**: Attacker uses the leaked key to launch 80 `c5.24xlarge` EC2 instances across 6 AWS regions to mine Monero.

```mermaid
sequenceDiagram
    autonumber
    actor Dev as Developer
    participant Git as GitHub Repo
    participant Bot as Attacker Bot
    participant AWS as Corporate AWS Cloud
    participant Guard as AWS GuardDuty
    participant Sec as DevSecOps / SOC

    Dev->>Git: 1. git push (commits AWS Access Key)
    par Threat Actor Exploitation
        Bot->>Git: 2. Scrapes key in 45 seconds
        Bot->>AWS: 3. Deploys 80 cryptomining EC2 instances
    and Security Detection
        Guard->>Sec: 4. Critical Alert: Anomalous EC2 creation
    end
    Sec->>AWS: 5. Deactivates leaked IAM Access Key
    Sec->>AWS: 6. Terminates rogue EC2 instances
```

### 2. Emergency Remediation Protocol

#### Step 1: Deactivate the Compromised Key Immediately (AWS CLI)
```bash
aws iam update-access-key \
    --access-key-id "AKIAIOSFODNN7EXAMPLE" \
    --status Inactive \
    --user-name "developer-alice"
```

#### Step 2: Rewrite Git Commit History using `git-filter-repo`
Deleting the file in a new commit does **NOT** remove it from Git history!
```bash
# Purge secret permanently from all commits and tags
git filter-repo --invert-paths --path app.js
git push origin --force --all
```

#### Step 3: Implement Permanent Preventative Controls
1. Install **Gitleaks** as a mandatory server-side pre-receive hook in GitHub Enterprise.
2. Migrate all CI/CD pipelines to **OIDC Workload Identity Federation**, permanently eliminating static AWS access keys.
