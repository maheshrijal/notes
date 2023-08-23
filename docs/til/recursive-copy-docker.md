---
title: Recursivley copy files to & from docker container
date: 2023-08-23
---


## Copy from Host to Docker

```
docker cp src/. container_id:/target
```

## Copy from Docker to Host

```
docker cp container_id:/src/. target
```