# Lab 02: Writing Custom Semgrep SAST Rules

[⬅️ Prev: GitHub Actions Lab](./01_Lab_Building_Secure_GitHub_Actions_Pipeline.md) | [Back to Labs](./README.md) | [Next: Trivy Container Lab ➡️](./03_Lab_Trivy_Container_Scanning_and_Dockerfile_Hardening.md)

---

### Objective
Write a custom Semgrep pattern rule to detect dangerous raw SQL queries in a Python application, and test it against a vulnerable sample file.

#### Step 1: Create the Vulnerable Code (`vuln.py`):
```python
import sqlite3

def get_user(username):
    conn = sqlite3.connect("database.db")
    cursor = conn.cursor()
    # VULNERABLE: Direct string formatting into SQL query!
    query = f"SELECT * FROM users WHERE username = '{username}'"
    cursor.execute(query)
    return cursor.fetchall()
```

#### Step 2: Create Custom Semgrep Rule (`custom-sql-rule.yaml`):
```yaml
rules:
  - id: custom-insecure-sql-formatting
    languages: [python]
    severity: ERROR
    message: "Detected raw string formatting inside SQL execute call. Use parameterized queries instead!"
    patterns:
      - pattern-either:
          - pattern: $CURSOR.execute(f"...", ...)
          - pattern: |
              $QUERY = f"..."
              ...
              $CURSOR.execute($QUERY, ...)
```

#### Step 3: Run Semgrep Locally:
```bash
semgrep --config custom-sql-rule.yaml vuln.py
```
