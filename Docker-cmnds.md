# Docker Container
```bash
# Creates a new Docker container.
docker create <new-container-name>

# Runs a Docker container.
docker run <container-name> <command>
 
# Run container in background and print container ID
docker run -d <container-name> <command>

# Run Container with Port Mapping
docker run -d <container-name> <command> -p 80:8080

# Lists running containers on the host machine.
docker ps

# Lists all containers
docker ps -a

# Stops running container.
docker stop <container-name>

# Starts a stopped container.
docker start <container-name>

# Removes a stopped container.
docker rm <container-name>

# Run a command in a running container.
docker exec <container-name> <command>
```



### **Docker Image Commands**
```bash
# Lists docker images on the host machine.
docker images
docker image ls

# Builds image from Dockerfile.
docker build -t <image-name> <dockerfile-name>

# Builds image from 
# - Directory: "abhishekf5"
# - Dockerfile: "my-first-docker-image"
# - Image Tag "latest"
docker build -t abhishekf5/my-first-docker-image:latest .

# Runs a Docker container using an image. 
# - `docker run -d ` -> Run container in background and print container ID
# - `docker run -p` -> Port mapping
docker run <image-name> <command>

# Removes an image from the host machine.
docker rmi <image-name>>

# Downloads an image from the configured registry.
docker pull <image-name>

# Uploads an image to the configured registry.
docker push <image-name>
```



### **Docker Network Commands**
```bash
# Lists docker networks on the host machine.
docker network ls

# Creates a new Docker network.
docker network create <network-name>

# Connects a container to a Docker network.
docker network connect <network-name> <container-name>

# Disconnects a container from a Docker network.
docker network disconnect <network-name> <container-name>

# Removes a Docker network.
docker network remove <network-name>

# Inspect a Docker network.
docker network inspect <network-name>

# Updates a Docker network.
docker network update <network-name>

# Upgrades a Docker network.
docker network upgrade <network-name>

# Removes unused Docker networks.
docker network prune <network-name>

# Create and secure your containers and isolate them from the default bridge network
docker network create -d bridge my_bridge <network-name>

# This way, you can run multiple containers on a single host platform where one container is attached to the default network and The other is attached to the my_bridge network.
# These containers are completely isolated with their private networks and cannot talk to each other.
docker run -d --net=my_bridge --name db training/postgres <command>

# Attach the first container to my_bridge network and enable communication
docker network connect my_bridge web <network-name>

# Run a Docker container with the host network
docker run --network="host" <image_name> <command>
```
----------------------------------------------------------------


### **Docker Volume Commands**
```bash
# Lists docker volumes on the host machine.
docker volume ls 

# Creates a new Docker volume.
docker volume create <volume-name>

# Removes a Docker volume.
docker volume rm <volume-name>

# Inspect a Docker volume.
docker volume inspect <volume-name>

# Updates a Docker volume.
docker volume update <volume-name>

# Prunes unused Docker volumes.
docker volume prune <volume-name>

# Upgrades a Docker volume.
docker volume upgrade <volume-name>

# Mount a volume from the host file system into a container.
docker run -it -v <volume_name>:/data <image_name> /bin/bash

# Mount a directory from the host file system into a container.
docker run -it -v <host_path>:<container_path> <image_name> /bin/bash

# docker volume ls | grep ssh_host_keys
Verify the Volume Exists

# docker volume inspect ssh_host_keys
Inspect the Volume <Find Mount Path>
```

---

### **Docker Service commands**
```bash
# Lists docker services on the host machine.
docker service ls

# Creates a new Docker service.
docker service create <service-name>

# Removes a Docker service.
docker service rm <service-name>

# Inspect a Docker service.
docker service inspect <service-name>

# Updates a Docker service.
docker service update <service-name>

# Prunes unused Docker services.
docker service prune <service-name>


# Upgrades a Docker service.
docker service upgrade <service-name>

# Starts a Docker service.
docker service start <service-name>

# Stops a Docker service.
docker service stop <service-name>

# Restarts a Docker service.
docker service restart <service-name>

# Checks the status of a Docker service.
docker service status <service-name>

# Pauses a Docker service.
docker service pause <service-name>

# Unpauses a Docker service.
docker service unpause <service-name>

# Shows the logs of a Docker service.
docker service logs <service-name>

# Shows the top processes of a Docker service.
docker service top <service-name>
```

------------------------------------------------------------

Test:
```bash
# Grant Access to your user to run docker commands
docker login
# Login to Docker 
[Create an account with https://hub.docker.com/]

# Start a temporary container with access to the volume:
docker run --rm -it -v ssh_host_keys:/mnt busybox sh

# Inside the container, list files:
ls -l /mnt
```

### Restore Data to Another location:
```bash
# copy the volume data to a host directory, use:
docker run --rm -v ssh_host_keys:/data -v $(pwd):/backup busybox cp -r /data /backup
```

---
