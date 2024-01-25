---
title: Fetch linux version recrusively in kubernetes
date: 2024-01-25
---


Set the namespace in current context
```
kubectl config set-context --current --namespace=<namespace>
```

Get the OS

```
kubectl get po -o custom-columns="POD:.metadata.name" | tail -n +2 | xargs -I {} kubectl exec {} -- sh -c 'echo "Pod - $(hostname): $(cat /etc/os-release | grep PRETTY_NAME)"'
```