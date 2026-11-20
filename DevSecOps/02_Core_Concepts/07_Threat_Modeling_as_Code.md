# 07. Threat Modeling as Code (TMaC)

[⬅️ Prev: Infrastructure as Code](./06_Infrastructure_as_Code_IaC_and_Policy_as_Code.md) | [Back to Core Concepts](./README.md) | [Next: Technologies & Architectures Track ➡️](../03_Technologies_and_Architectures/README.md)

---

### 1. The Challenge with Traditional Threat Modeling

Historically, threat modeling was performed using whiteboards and manual Microsoft Word documents. 
- **The Problem**: Software architectures evolve daily in agile CI/CD environments. A threat model documented on a whiteboard becomes obsolete within 2 sprints, discarded as forgotten paperwork.

**Threat Modeling as Code (TMaC)** bridges this gap by defining systems, trust boundaries, data flows, and threat mitigations directly in version-controlled programming code (Python, YAML, or HCL) stored right alongside application source code.

---

### 2. Automated STRIDE Threat Modeling with Python (`pytm`)

Using open-source frameworks like `pytm`, developers define their architecture programmatically:

```python
#!/usr/bin/env python3
from pytm import TM, Server, Datastore, Dataflow, Boundary, Actor, Classification

tm = TM("Cloud Banking Payment Gateway")
tm.description = "Automated Threat Model for Payment API"

# Trust Boundaries
internet = Boundary("Untrusted Internet")
corporate_dmz = Boundary("Corporate Cloud VPC")

# Actors & Elements
customer = Actor("Mobile Customer")
customer.inBoundary = internet

api_gateway = Server("Payment API Gateway")
api_gateway.inBoundary = corporate_dmz
api_gateway.isHardened = True

database = Datastore("Customer Financial DB")
database.inBoundary = corporate_dmz
database.isEncryptedAtRest = True

# Data Flows
Dataflow(customer, api_gateway, "Submit Credit Card Payment (HTTPS TLS 1.3)")
Dataflow(api_gateway, database, "Store Encrypted Token (mTLS)")

if __name__ == "__main__":
    tm.resolve()
    tm.check() # Automatically evaluates STRIDE threats and outputs report!
```

---

### 3. Benefits of Threat Modeling in CI/CD

- **Automated Threat Reports**: Every time an engineer adds a new service or data flow in code, the pipeline regenerates updated Data Flow Diagrams (DFDs) and STRIDE threat matrices.
- **Architectural Security Unit Tests**: The pipeline fails if a developer connects an untrusted public internet actor directly to an unencrypted database.
