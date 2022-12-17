---
title: kubectl, working with pods in kubernetes
---

# kubectl, & pods in kubernetes

### Create pods

!!! abstract ""

    There are 3 differnet ways to create pods from kubectl CLI

    - `kubectl run` (run single pod per command)

    - `kubectl create` (create resources via CLI or YAML)

    - `kubectl apply` (create/update resources via YAML)

Create ngnix web-server pod

```bash
kubectl run mhs-nginx --image nginx
```

### List Pods

!!! abstract ""

    This will show pods only in the default namespace

```
kubectl get pods
```

List pods with custom namespace

```
kubectl get pods --namespace=kube-system
```

List pods with IP address
```
kubectl get pod -o wide
```

List pods& refresh continously
```
kubectl get pods -o wide -w
```


Cluster info

```
kubectl cluster-info
```

### Describe pods

```
kubectl describe pod mhs-nginx
```

### Delete pods
```
kubectl delete pod mhs-nginx
```
### SSH into a pod
```
kubectl exec -it hello -- /bin/sh
```