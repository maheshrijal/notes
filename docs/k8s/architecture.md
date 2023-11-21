---
title: Kubernetes architecture
date: 2023-07-09
---

#

## Control Plane Components

The control plane is responsible for managing the state of the cluster. In production environments, the control plane usually runs on multiple nodes that span across several data center zones. Control plane manages worker nodes and the containers running on them.


### Kubernetes API Server
### etcd

- `etctd` is a highly available key value pair database

### Kube Scheduler (Kubernetes Scheduler)

- Assign pods to nodes based on resource availability, taints/tolerations, affinityrules & other user-defined policies.
<!--
### Node Controller -->