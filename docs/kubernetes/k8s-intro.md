---
title: Kubernetes intro, overview & advantages
---

# Introduction to Kubernetes

- Kubernetes is container orchestrator making many servers act like one.
- Kubernetes usually run's on top of `docker` but can use other container runtimes like `containerd` or `crio`
- Kubernetes provides API/CLI to manage containers across servers
- Similar to linux distributions there are kubernetes distributions as well

### Advantages of Swarm
- Comes with docker, single vendor container platform
- Easiest to deploy & manage
- Can run anywhere docker does Eg: local, cloud, datacenter, ARM, windows 32-bit, 64-bit
- Secure by default (mutual tls authentication in swarm, encrypted control plane & database secrets)
- Easier to troubleshoot

### Advantages of Kubernetes
- Cloud/Infrastructure vendors will deploy/manage kubernetes
- Wide adoption in the community
- Flexible: cover a wide range of use cases




## Terminology
`kubectl` -  The CLI to configure & manage apps.

`node` - Single server kubernetes cluster.

`kubelet` - Kubernetes agent running on every node.

`control plane` - A set of container that manage the clusters(Also called master). Similar to manager in swarm.
  - Includes API server, scheduler, controller manager, etcd

#### Pod
- One or more container running together on one node

- Pod is a abstraction of the container

- Containers are always in pods


#### Controllers
- Used to for creating/updated pods & other objects

- Pods can be deployed directly (without containers) but not recommended

##### Types of controllers
- Deployment

- ReplicaSet

- StatefulSet

- Job