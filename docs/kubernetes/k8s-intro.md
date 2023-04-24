---
title: Kubernetes intro, overview & advantages
---

# Kubernetes introduction and architecture

- Kubernetes is container orchestrator making many servers act like one.
- Kubernetes usually run's on top of `docker` but can use other container runtimes like `containerd` or `crio`
- Kubernetes provides API/CLI to manage containers across servers
- Similar to linux distributions there are kubernetes distributions as well

### Advantages of Docker Swarm
- Comes with docker, single vendor container platform
- Easiest to deploy & manage
- Can run anywhere docker does Eg: local, cloud, datacenter, ARM, windows 32-bit, 64-bit
- Secure by default (mutual tls authentication in swarm, encrypted control plane & database secrets)
- Easier to troubleshoot

### Advantages of Kubernetes
- Cloud/Infrastructure vendors will deploy/manage kubernetes
- Wide adoption in the community
- Flexible: cover a wide range of use cases


## Kubernetes architecture

### Pod

- Kubernetes does not run containers directly, it runs pods (Containers encapsulated in pods)
- Pod is the smallest unit of deployment in kubernetes
- Pod is a single instance of an application
- A single pod can have multiple containers but they are usually not multiple containers of the same application Eg: `Healthcheck container`, `Helper container`. However, this is a rare use case
- Containers in a pod share the same IP address and port space (Can communicate with each other using localhost)

```
kubectl run nginx --image nginx
```

1. Creates a Pod

2. Deploys nginx docker container inside the pod

### Cluster

- A cluster is a set of nodes grouped together

<!-- `kubelet` - Kubernetes agent running on every node.

`control plane` - A set of container that manage the clusters(Also called master). Similar to manager in swarm.
  - Includes API server, scheduler, controller manager, etcd
-->

## Controllers

- Components in the Kubernetes system that monitor and manage the state of the resources running in a Kubernetes cluster.

### Types of controllers

#### Replication Controller

- Run multiple instances of a pod across the cluster providing fault tolerance and high availability
- Even in a single node cluster, replication controller can be used to restart a pod if it fails

!!! warning

    [Replication controller](/kubernetes/k8s-yaml/#replication-controller) is deprecated in favor of ReplicaSet


#### ReplicaSet

<!-- - ReplicaSet is a controller that manages a set of pods
- ReplicaSet ensures that a specified number of pod replicas are running at any given time
- ReplicaSet is the next generation Replication Controller
- ReplicaSet is the recommended way to manage pods -->



<!-- - Deployment

- ReplicaSet

- StatefulSet

- Job -->