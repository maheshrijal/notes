---
title: Kubernetes keywords, terminology, Abbreviations & more
---

# Terminology

### Label

Example labels:
Labels are key-value pairs assigned to Kubernetes Objects like Pods, Service

Example labels:
```
"release" : "stable", "release" : "canary"
```
Labels can be used to:

- find all pods that have a value associated with the key
    ```
    kubectl get pods -l key=val,key2=val2
    ```
- merge and stream logs of the various pod that share the same label
    ```
    kubectl logs -l key=val
    ```

### Label selectors

Unlike names and UIDs, labels do not provide uniqueness. In general, we many objects can have the same label.

Label selectors can be used to identify a set of objects. The label selector is the core grouping primitive in Kubernetes.

The API currently supports two types of selectors:

1. equality-based
    - `environment = production`
    - `tier != frontend`

2. set-based
    - environment in (production, qa)
    - tier notin (frontend, backend)
    - partition
    - !partition


### Annotations
Annotations are used to store data about the resource itself

This usually consists of machine-generated data, and can even be stored in JSON form.

Examples:

last updated
managed by
sidecar injection configuration etc


### ReplicaSet
A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.

A ReplicaSet ensures that a specified number of pod replicas are running at any given time.


### kubectl abbreviations

| Long Name      | Short Name |
| ----------- | ----------- |
| services   | svc       |
| deployment   | deploy        |