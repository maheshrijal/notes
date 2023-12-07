---
title: Kubernetes architecture
---


## Control Plane Components

The control plane is responsible for managing the state of the cluster. In production environments, the control plane usually runs on multiple nodes that span across several data center zones. Control plane manages worker nodes and the containers running on them.

### Kube-API Server

The front end for the kubernetes control plane. It's what nodes and othercluster elements interact with. Can be horizontally scaled for HA & performance.

### Kube Scheduler (Kubernetes Scheduler)

Identifies any pods within the cluster with no assigned node & assigns a node based on resource requirements, deadlines, affinity/anti-affinity, data locality, resource availability, taints/tolerations and any other constraints

### ETCD

Provides a highly-avaliable key value store used within the cluster. It's used as the main backing store for the cluster.

### Cloud Controller Manager

Optional component provides cloud specific control logic. It allows you to link kubernetes with a cloud providers API (Eg: AWS, Azure, GCP)

### Kube Controller Manager

A collection of process that perform the following activities:

**Node Controller** - monitoring and responding to node outages

**Job Controller** - Run one of pods to execute tasks(jobs)

**Endpoint Controller** - Populates endpoints in the cluster (Links Services <==> PODS)

**Service Account & Token Controllers** - Accounts/API tokens creation

## Node Components

### Container Runtime

A runtime to run the containers eg:  containerd, CRI-O, docker

### Kubelet

An agent that runs on each node in the cluster. It makes sure that containers are running in a Pod.

### Pods

Smallest unit of computing in kubernetes. Pods are ephemeral

### Kube-Proxy

A network proxy which runs on each node & coordinates networking with the control plane. It helps implement [services](#kubernetes-services) and configures rules allowing communications witht pods from inside or outside the cluster.

## Kubernetes Services

An abstract way to expose an application running on a set of Pods as a network service. These services enable communication within and outside the application.

### ClusterIP

ClusterIP is a type of service which is only accessible within a Kubernetes cluster, or the internal ("virtual") IP of components within a Kubernetes cluster.

- ClusterIP is the default and most common service type.

- Kubernetes will assign a cluster-internal IP address to ClusterIP service. This makes the service only reachable within the cluster.

- You cannot make requests to service (pods) from outside the cluster.

- You can optionally set cluster IP in the service definition file.

### NodePort
NodePort builds on top of the ClusterIP Service and provides a way to expose a group of Pods to the outside world.

- A ClusterIP Service, to which the NodePort Service routes, is automatically created.

- It exposes the service outside of the cluster by adding a cluster-wide port on top of ClusterIP.
NodePort exposes the service on each Node’s IP at a static port (the NodePort). Each node proxies that port into your Service. So, external traffic has access to fixed port on each Node. It means any request to your cluster on that port gets forwarded to the service.

- You can contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>.

- Node port must be in the range of 30000–32767. Manually allocating a port to the service is optional. If it is undefined, Kubernetes will automatically assign one.

### LoadBalancer
- LoadBalancer service is an extension of NodePort service. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.

- It integrates NodePort with cloud-based load balancers & exposes the Service externally using a cloud provider’s load balancer.

- The cloud providers assigns the external IP to the LoadBalancer automatically

- In local k8s cluster LoadBalancer behaves similar to NodePort