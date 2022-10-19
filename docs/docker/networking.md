---
title: Docker Networking commands
summary: Managing docker containers with commands
date: 2022-07-14
---
# Docker Networks

## Network Drivers

#### Bridge
- This is the default. Whenever you start Docker, a bridge network gets created and all newly started containers will connect automatically to the default bridge network.

- Containers running in the same bridge network can communicate with each other (through IP address), and Docker uses iptables on the host machine to prevent access outside of the bridge.

- Isolation & name resolution is not possible in default bridge network. However, it is supported on custom bridge network.

#### Host
- Removes network isolation between the container and the Docker host, and uses the host’s networking directly.
- Host driver does not work on docker desktop, it works only on a linux host

#### Overlay
- Overlay networks connect multiple Docker daemons together and enable swarm services to communicate with each other.

#### macvlan
- Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network.

- Macvlan allows a single physical interface to have multiple mac and ip addresses using macvlan sub-interfaces.

- Macvlan allocate diffferent mac address for every container that's attached to the network

#### ipvlan
- Can operate in layer 2 & 3

- ipvlan is similar to macvlan with the difference being that the endpoints have the same mac address

#### None
- Disables all networking.
- Not available for swarm.

## Networking commands
- Show networks: `docker network ls`
- Inspect a network: `docker network inspect`
- Create a network: `docker network create --driver`
- Attach a network to a container: `docker network connect`
- Detach a network from container: `docker network disconnect`
