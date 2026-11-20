# 05. Containerization and Docker Security Fundamentals

[⬅️ Prev: Supply Chain & SBOM](./04_Software_Supply_Chain_Security_and_SBOM.md) | [Back to Core Concepts](./README.md) | [Next: Infrastructure as Code ➡️](./06_Infrastructure_as_Code_IaC_and_Policy_as_Code.md)

---

### 1. Containers vs Virtual Machines: The Security Boundary

A container is **NOT** a lightweight virtual machine.
- A **Virtual Machine (VM)** virtualizes physical hardware and runs a complete, independent operating system kernel managed by a Hypervisor (Type 1 or Type 2). The hypervisor provides a strong hardware isolation boundary.
- A **Container** is merely an isolated user-space process running on the host OS kernel, constrained by Linux **Namespaces** and **Cgroups (Control Groups)**.

> 🚨 **Critical Security Rule**: Containers share the host kernel. If an unprivileged container process exploits a kernel vulnerability, it can break out of the container (**Container Escape**) and gain root access over the host machine!

---

### 2. The Golden Rules of Dockerfile Hardening

#### ❌ VULNERABLE DOCKERFILE:
```dockerfile
# VULNERABLE: Uses bloated base image, runs as root, copies unnecessary files
FROM ubuntu:latest
WORKDIR /app
COPY . .
RUN apt-get update && apt-get install -y python3 python3-pip
RUN pip3 install -r requirements.txt
EXPOSE 8080
# Runs as default ROOT user!
CMD ["python3", "server.py"]
```

#### ✅ HARDENED SECURE DOCKERFILE:
```dockerfile
# Multi-stage build using minimal Alpine or Distroless base image
FROM python:3.11-alpine AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# Final Production Stage
FROM python:3.11-alpine
WORKDIR /app

# 1. Create dedicated non-root unprivileged user and group
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# 2. Copy only compiled dependencies from builder stage
COPY --from=builder /root/.local /home/appuser/.local
COPY --chown=appuser:appgroup server.py .

# 3. Enforce non-root execution
USER appuser
ENV PATH=/home/appuser/.local/bin:$PATH

EXPOSE 8080
CMD ["python", "server.py"]
```
