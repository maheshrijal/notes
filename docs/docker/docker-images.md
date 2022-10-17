---
title: Managing docker images
date: 2022-07-16
---
# Docker images
!!! tip ""
    Image contains applications binaries, dependencies for the app  along with metadata about how to run it

## Usage: ```docker image command```

#### Image details

`ls` - List downloaded images

`build` — Build an image.

`history` — See intermediate image info. Eg: `docker image history nginx:latest`

`inspect` — Inspect the image, including the layers. Eg: `docker image inspect nginx`


#### Tag image
```shell
docker image tag <source-img:tag> <taget-image:tag>
```

#### Upload image to docker hub
```shell
docker image push [OPTIONS] NAME[:TAG]
```

#### Cleaning up images

Remove dangling images

```shell
docker image prune
```

Remove images that are not in use
```
docker image prune -a
```

Cleanup everything
```
docker system prune
```