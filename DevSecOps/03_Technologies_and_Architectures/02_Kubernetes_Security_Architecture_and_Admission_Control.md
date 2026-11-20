# 02. Kubernetes Security Architecture and Admission Control

[⬅️ Prev: Container Hardening](./01_Container_Security_and_Runtime_Hardening.md) | [Back to Technologies](./README.md) | [Next: Cloud Security & CSPM ➡️](./03_Cloud_Security_Shared_Responsibility_and_CSPM.md)

---

### 1. The 4Cs of Cloud-Native Security

```mermaid
graph TD
    Cloud["1. Cloud Security (AWS / Azure / GCP)"] --> Cluster["2. Cluster Security (Kubernetes Control Plane)"]
    Cluster --> Container["3. Container Security (Docker Images & Base OS)"]
    Container --> Code["4. Code Security (Application Logic & Dependencies)"]
```

---

### 2. Kubernetes Admission Controllers: The Cluster Gatekeepers

An **Admission Controller** intercepts requests to the Kubernetes API server *after* authentication and authorization, but *before* the object is persisted to `etcd`:

```mermaid
sequenceDiagram
    autonumber
    actor Dev as Developer / CI Runner
    participant API as K8s API Server
    participant Auth as AuthN / RBAC
    participant AC as Admission Controller (Kyverno / Gatekeeper)
    participant etcd as etcd Cluster State

    Dev->>API: 1. kubectl apply -f deployment.yaml
    API->>Auth: 2. Authenticate & verify RBAC permissions
    Auth-->>API: 3. Authorized!
    API->>AC: 4. Mutating & Validating Webhook Check
    Note over AC: Policy: Block any pod running as root or missing memory limits!
    alt Pod violates security policy
        AC-->>API: 5. REJECT! "Deployment violates PodSecurity standard: privileged=true"
        API-->>Dev: 6. 🚫 Error: Deployment blocked by Kyverno policy!
    else Pod is compliant
        AC-->>API: 5. ALLOW!
        API->>etcd: 6. Persist Pod definition
    end
```
