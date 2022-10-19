---
title: kubectl, the CLI for kubernetes
---
# kubectl, the kubernetes cli

## Creating pods
!!! abstract ""

    There are 3 differnet ways to create pods from kubectl CLI

    - `kubectl run` (run single pod per command)

    - `kubectl create` (create resources via CLI or YAML)

    - `kubectl apply` (create/update resources via YAML)


Create ngnix web-server pod

```bash
kubectl run mhs-nginx --image nginx
```

Get Pods
```
kubectl get pods
```