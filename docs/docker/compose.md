---
title: Docker Compose
---
- Docker-compose reads configuration data from a YAML file.
- Compose is not a production grade tool but ideal for local development & test.


### Setup volumes/networks & start all containers
```sh
docker-compose up
```

### Stop all containers & remove containers/volume/networks
```sh
docker-compose down
```

### List all running containers
```sh
docker-compose ps
```

### View processes running inside a container
```sh
docker-compose top
```

### Show help for docker compose
```sh
docker-compose --help
```

## Docker Compose Examples

``` YAML title="template.yaml"
version: '3.1' # If no version is specified then v1 is assumed.

services: #same as docker run i.e. containers
    servicename: #A friendly name that is also a DNS name (Smiliar to --name)
        image: #optional if using build
        command: # Optional, run a command
        environment: # Similar to -e in docker run
        volumes: # Optional, same as -v in docker run

    servicename2:

volumes: # Optional, same as docker volume create

network: # Optional, same as docker network create
```

``` YAML title="hello-world.yaml"
version: '3'

services:
    web:
        build: .
        image: web-client
        depends_on:
        - server
        ports:
        - "8080:8080"
    server:
        image: docker/helloworld
        volumes:
        - "/app" # Anonymous volume
        - "data:/data" # Named volume
        - "mydata:/data" # External volume

volumes:
    data:
    mydata:
        external: true
```