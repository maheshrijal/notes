---
title: Kubernetes services and types
---

# Kubernetes Services

An abstract way to expose an application running on a set of Pods as a network service. These services enable communication within and outside the application.


## ClusterIP

ClusterIP is a type of service which is only accessible within a Kubernetes cluster, or the internal ("virtual") IP of components within a Kubernetes cluster.

- ClusterIP is the default and most common service type.

- Kubernetes will assign a cluster-internal IP address to ClusterIP service. This makes the service only reachable within the cluster.

- You cannot make requests to service (pods) from outside the cluster.

- You can optionally set cluster IP in the service definition file.

## NodePort
NodePort builds on top of the ClusterIP Service and provides a way to expose a group of Pods to the outside world.

- A ClusterIP Service, to which the NodePort Service routes, is automatically created.

- It exposes the service outside of the cluster by adding a cluster-wide port on top of ClusterIP.
NodePort exposes the service on each Node’s IP at a static port (the NodePort). Each node proxies that port into your Service. So, external traffic has access to fixed port on each Node. It means any request to your cluster on that port gets forwarded to the service.

- You can contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>.

- Node port must be in the range of 30000–32767. Manually allocating a port to the service is optional. If it is undefined, Kubernetes will automatically assign one.

## LoadBalancer
- LoadBalancer service is an extension of NodePort service. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.

- It integrates NodePort with cloud-based load balancers & exposes the Service externally using a cloud provider’s load balancer.

- The cloud providers assigns the external IP to the LoadBalancer automatically

- In local k8s cluster LoadBalancer behaves similar to NodePort


<!--
## Services present on all nodes

### kubelet

### kube-proxy

### Container runtime

## Master Node Services

Services running in the master node

### scheduler

### kube-controller-manager

### cloud-controller-manager
- A single load balancer IP address for a single kubernetes cluster, used to connect to all pods.
- These are assiged by cloud services providers

### etcd

### dns for name resolution

## Worker Node Services

Services running in the worker node -->
