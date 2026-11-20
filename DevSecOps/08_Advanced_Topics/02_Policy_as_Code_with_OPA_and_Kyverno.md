# 02. Policy-as-Code with OPA and Kyverno

[⬅️ Prev: SLSA Framework](./01_SLSA_Framework_and_Attestation_Signing.md) | [Back to Advanced Topics](./README.md) | [Next: Chaos Security ➡️](./03_Chaos_Engineering_and_Security_GameDays.md)

---

### 1. Kyverno for Kubernetes Security
**Kyverno** is a Kubernetes-native policy engine that validates, mutates, and generates configurations using standard YAML without learning a new programming language like Rego.

#### Example Kyverno Policy: Disallow Containers Running as Root
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-run-as-non-root
spec:
  validationFailureAction: Enforce
  rules:
  - name: check-runasnonroot
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "Running as root is forbidden. Set securityContext.runAsNonRoot to true."
      pattern:
        spec:
          securityContext:
            runAsNonRoot: true
```
