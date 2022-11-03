---
title: kubectl, the CLI for kubernetes
---

# kubectl, the kubernetes CLI

## pods

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


## Nodes

Get Nodes

```
kubectl get nodes
```

## Namespaces

In Kubernetes, namespaces provides a mechanism for isolating groups of resources within a single cluster.

Kubernetes starts with four initial namespaces:

- `default` The default namespace for objects with no other namespace

- `kube-system` The namespace for objects created by the Kubernetes system

- `kube-public` This namespace is created automatically and is readable by all users (including those not authenticated). This namespace is mostly reserved for cluster usage, in case that some resources should be visible and readable publicly throughout the whole cluster. The public aspect of this namespace is only a convention, not a requirement.

- `kube-node-lease` This namespace holds Lease objects associated with each node. Node leases allow the kubelet to send heartbeats so that the control plane can detect node failure.

List all namespaces in cluster

```
kubectl get namespaces
```

Start a pod with namespace

```
kubectl run nginx --image=nginx --namespace=<insert-namespace-name-here>
```

## Deployments
Deployment managed by the `Kubernetes Deployment controller` tells Kubernetes how to create or modify instances of the pods that hold a containerized application. A Deployment runs multiple replicas of your application and automatically replaces any instances that fail or become unresponsive.

### Create Deployment

Create & scale a deployment
```
kubectl create deployment nginx-deploy --image=nginx
```

Get details about a deployment
```
kubectl describe deployment nginx-deploy
```

Scale a deployment
```
kubectl scale deployment nginx-deploy --replicas=5
```

Remove deployment
```
kubectl delete deployment nginx-deploy
```

## [Services](/kubernetes/services/#kubernetes-services)

### Create a [ClusterIP](/kubernetes/services/#clusterip)

Expose a internal port 80 to the external port 8080
```
kubectl expose deployment nginx-deploy --port=8080 --target-port=80
```
!!! warning

    ClusterIP service will allow connecting from inside the kubernetes cluster.
    Used when we want to block connections from outside the kubernetes cluster but want the cluster itself to have access.

### Create a [NodePort](/kubernetes/services/#nodeport)
```
kubectl expose deployment k8s-hello --type=NodePort --port=3000
```


### List services
```
kubectl get services
```
Get Service Details
```
kubectl describe service nginx-deploy
```

Delete service
```
kubectl delete service nginx-deploy
```