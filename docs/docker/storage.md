---
title: Storage & data persistence in docker
---

## Data Volumes
 - Volumes are files/directories outside of the default Union File System
 - Volumes exist as normal directories and files on the host filesystem.


### Creating volume

```sh
docker container run -d --name mysqlnamedvol -e MYSQL_ALLOW_EMPTY_PASSWORD=TRUE -v mysql-db:/var/lib/mysql mysql
```
!!! callout ""
    -v : specify new/existing volumes on docker run

Custom drivers & driver options can be specified only if docker volume is created with the volume command.
Eg: docker volume create

### List volumes

```sh
docker volume ls
```

## Bind Mounts
- Maps the host files/directories to a container files/directory i.e. Two locations pointing to the same files.
- Bind mounts cannot be used in Dockerfile, must be used at container run
- Bind mount starts as / whereas volume mount starts without

Mac/Linux:
```sh
... run -v /Users/mhs/stuff:/path/container
```

Windows:
```sh
... run -v //c/Users/mhs/stuff:/path/container
```

!!! tip "How docker differentiates between volumes & bind mounts?"

    Bind mounts start with a```/``` in linux & ```//``` in windows.