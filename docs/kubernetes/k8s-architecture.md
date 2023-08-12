---
title: Kubernetes architecture
date: 2023-07-09
---

#

## Control Plane Components

### etcd

- `etctd` is a highly available key value pair database

### Kube Scheduler

- Assign pods to nodes based on resource availability, taints/tolerations, affinityrules & other user-defined policies.

### Node Controller