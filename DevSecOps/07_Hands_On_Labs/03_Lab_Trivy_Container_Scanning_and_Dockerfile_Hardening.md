# Lab 03: Container Scanning with Trivy and Dockerfile Hardening

[⬅️ Prev: Custom Semgrep Lab](./02_Lab_Semgrep_Custom_Rule_Creation.md) | [Back to Labs](./README.md) | [Next: Checkov IaC Lab ➡️](./04_Lab_Checkov_Scanning_Terraform_Code.md)

---

### Objective
In this lab, you will build an insecure Docker image, scan it with Trivy to observe CVE vulnerabilities, refactor the Dockerfile using multi-stage builds and unprivileged non-root users, and re-scan to achieve zero critical CVEs.

---

### Step 1: Create the Insecure Dockerfile (`Dockerfile.vuln`)

```dockerfile
# INSECURE DOCKERFILE
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
# Runs as default ROOT user!
CMD ["python", "app.py"]
```

### Step 2: Build and Scan with Trivy
```bash
# Build vulnerable image
docker build -t vulnerable-app:latest -f Dockerfile.vuln .

# Scan image for vulnerabilities
trivy image --severity HIGH,CRITICAL vulnerable-app:latest
```
*Observe the results: Trivy flags 14+ vulnerabilities in the base OS and package libraries.*

---

### Step 3: Author the Hardened Multi-Stage Dockerfile (`Dockerfile.secure`)

```dockerfile
# HARDENED MULTI-STAGE DOCKERFILE
# Stage 1: Build Dependencies
FROM python:3.11-alpine AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# Stage 2: Final Minimal Runtime
FROM python:3.11-alpine
WORKDIR /app

# 1. Create dedicated non-root unprivileged user and group
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# 2. Copy only compiled python libraries from builder stage
COPY --from=builder /root/.local /home/appuser/.local
COPY --chown=appuser:appgroup app.py .

# 3. Enforce non-root execution
USER appuser
ENV PATH=/home/appuser/.local/bin:$PATH

EXPOSE 8080
CMD ["python", "app.py"]
```

### Step 4: Re-Build and Re-Scan
```bash
# Build secure image
docker build -t secure-app:v1 -f Dockerfile.secure .

# Re-scan with strict pass/fail gate
trivy image --severity HIGH,CRITICAL --exit-code 1 secure-app:v1
```
*Result: Exit code 0! Zero Critical or High vulnerabilities detected.*
