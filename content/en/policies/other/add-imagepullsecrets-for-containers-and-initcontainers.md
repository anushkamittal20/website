---
title: "Add imagePullSecrets for Containers and InitContainers"
category: Sample
version: 1.3.6
subject: Pod
policyType: "mutate"
description: >
    Images coming from certain registries require authentication in order to pull them, and the kubelet uses this information in the form of an imagePullSecret to pull those images on behalf of your Pod. This policy searches for images coming from a registry called `corp.reg.com` referenced by either one of the containers or one  of the init containers and, if found, will mutate the Pod to add an imagePullSecret called `my-secret`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/add-imagepullsecrets-for-containers-and-initcontainers.yaml" target="-blank">/other/add-imagepullsecrets-for-containers-and-initcontainers.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-imagepullsecrets-for-containers-and-initcontainers
  annotations:
    policies.kyverno.io/title: Add imagePullSecrets for Containers and InitContainers
    policies.kyverno.io/category: Sample
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/minversion: 1.3.6
    policies.kyverno.io/description: >-
      Images coming from certain registries require authentication in order to pull them,
      and the kubelet uses this information in the form of an imagePullSecret to pull
      those images on behalf of your Pod. This policy searches for images coming from a
      registry called `corp.reg.com` referenced by either one of the containers or one 
      of the init containers and, if found, will mutate the Pod to add an
      imagePullSecret called `my-secret`.
spec:
  rules:
  - name: add-imagepullsecret
    match:
      resources:
        kinds:
        - Pod
    preconditions:
      any:
      - key: "corp.reg.com/*"
        operator: In
        value: "{{ images.initContainers.*.registry }}"
      - key: "corp.reg.com/*"          
        operator: In
        value: "{{ images.containers.*.registry }}"
    mutate:
      patchStrategicMerge:
        spec:
          imagePullSecrets:
          - name: my-secret
```
