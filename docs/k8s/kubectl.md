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
### SSH into a pod
```
kubectl exec -it hello -- /bin/sh
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

### Create/scale deployment
```
kubectl create deployment nginx-deploy --image=nginx
```

### Get deployment details
```
kubectl describe deployment nginx-deploy
```

### Scale deployment
```
kubectl scale deployment nginx-deploy --replicas=5
```

### Update deployment with new image
```
kubectl set image deployment k8s-hello k8s-hello=nginx:latest
```

### Check rollout status
```
kubectl  rollout status deploy k8s-hello
```

### Remove deployment
```
kubectl delete deployment nginx-deploy
```

## Rollout

Get rollout status
```
kubectl rollout status name
```

Get rollout history/revision

```
kubectl rollout history name
```

Undo rollout deployment

```
 kubectl rollout undo deployment myapp-deployment
```

### Deployment strategies

#### Recreate

This strategy will terminate all the old pods and then create new ones with the new configuration. This will cause downtime.

#### Rolling Update

This strategy will create new pods with the new configuration and then terminate the old ones gradually. It will create new pods with the new configuration and then terminate the old ones gradually. This will not cause downtime. This is the default strategy.

## [Services](services.md#kubernetes-services)

### Create a [ClusterIP](services.md#clusterip)

Expose a internal port 80 to the external port 8080
```
kubectl expose deployment nginx-deploy --port=8080 --target-port=80
```
!!! warning

    ClusterIP service will allow connecting from inside the kubernetes cluster.
    Used when we want to block connections from outside the kubernetes cluster but want the cluster itself to have access.

### Create a [NodePort](services.md#nodeport)
```
kubectl expose deployment k8s-hello --type=NodePort --port=3000
```
!!! info

    NodePort service will allow connecting from outside the kubernetes cluster

### Create a [LoadBalancer](services.md#loadbalancer)
```
kubectl expose deployment k8s-hello --type=LoadBalancer --port=3000
```
!!! info

    NodePort service will allow connecting from outside the kubernetes cluster




### List services
```
kubectl get svc
```
Get Service Details
```
kubectl describe service nginx-deploy
```

Delete service
```
kubectl delete service nginx-deploy
```

Get all objects in the default namespace
```
kubectl get all
```

### Cleanup

Delete all resources the default namespace
```
kubectl delete all --all
```
!!! tip

    This also deletes the default kubernetes(ClusterIP) service but it is re-created automatically


## Replicas

### Replication Controller
Get Replication Controller
```
kubectl get rc
```

### Replica Set

Get Replica Set
```
kubectl get rs
```

Scale Replica Set
```
kubectl scale --replicas=5 -f replicaset replicaset.yml
```

```
kubectl scale rs --replicas=5 replicaset
```

!!! warning

    This will create 5 replicas of the pods defined in the replicaset.yml file but does not update the replicaset.yml file

Edit Replica Set Inline

```
kubectl edit rs replicaset
```

!!! info

    This will open the replicaset.yml file in the default editor and any changes made will be applied immediately to the replicaset

## YAML Configuration

Every kubernetes configuration file has 3 parts

- metadata Eg: `name` & `labels`

- specification Eg: `spec`

- status, automatically generated by kubernetes



### Apply configuration
```
kubectl apply -f filename.yml
```

### Delete configuration
```
kubectl delete -f filename.yml
```