---
title: "Drop CAP_NET_RAW"
category: Best Practices
version: 
subject: Pod
policyType: "validate"
description: >
    Capabilities permit privileged actions without giving full root access. The CAP_NET_RAW capability, enabled by default, allows processes in a container to forge packets and bind to any interface potentially leading to MitM attacks. This policy ensures that all containers explicitly drop the CAP_NET_RAW ability.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//best-practices/require_drop_cap_net_raw/require_drop_cap_net_raw.yaml" target="-blank">/best-practices/require_drop_cap_net_raw/require_drop_cap_net_raw.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: drop-cap-net-raw
  annotations:
    policies.kyverno.io/title: Drop CAP_NET_RAW
    policies.kyverno.io/category: Best Practices
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Capabilities permit privileged actions without giving full root access. The
      CAP_NET_RAW capability, enabled by default, allows processes in a container to
      forge packets and bind to any interface potentially leading to MitM attacks.
      This policy ensures that all containers explicitly drop the CAP_NET_RAW
      ability.
spec:
  validationFailureAction: audit
  rules:
  - name: drop-cap-net-raw
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "The capability CAP_NET_RAW must be explicitly dropped."
      pattern:
        spec:
          containers:
          - securityContext:
              capabilities:
                drop: ["CAP_NET_RAW"]
          =(initContainers):
          - securityContext:
              capabilities:
                drop: ["CAP_NET_RAW"]
```