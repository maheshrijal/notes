Commands
To clean an unused/dangling image:
Docker image prune
To remove an image that is not used in a container:
Docker image prune -a
To prune the entire system:
Docker system prune
To leave a swarm:
Docker swarm leave
To remove a swarm:
Docker stack rm stack_name
To kill all running containers:
Docker kill $ (docker ps -q)
To delete all stopped containers:
docker rm $(docker ps -a -q)
To delete all images:
docker rmi $(docker images -q)