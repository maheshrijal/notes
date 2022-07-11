To initialize the swarm mode and listen to a specific interface:
Docker swarm init --advertise-addr 10.1.0.2
To join an existing swarm as a manager node:
Docker swarm join --token<manager-token> 10.1.0.2:2377
To join a swarm as a worker node:
Docker swarm join --token<worker-token> 10.1.0.2:2377
To list all nodes in the swarm:
Docker node ls
To create a service from an image on the existing port and deploy three instances:
Docker service create --replicas 3 -p 80:80 name -webngix
To list services running in a swarm:
Docker service ls
To scale a service:
Docker service scale web=5
To list the tasks of a service:
Docker service ps web

## HEAD

| Method      | Description                          |
| :---------: | :----------------------------------: |
| `GET`       | :material-check:     Fetch resource  |
| `PUT`       | :material-check-all: Update resource |
| `DELETE`    | :material-close:     Delete resource |
