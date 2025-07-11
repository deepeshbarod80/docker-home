Container Management:
```bash
# Run a container from an image (e.g., docker run nginx).
docker run [image] 
# List all containers (including stopped).
docker ps - List running containers.

# List all containers (including stopped).
docker ps -a

# Stop a running container.
docker stop <container_id>/<container_name>

# Start a stopped container.
docker start <container_id>/<container_name>

# Restart a container.
docker restart <container_id>/<container_name>

# Remove a stopped container.
docker rm <container_id>/<container_name>

# Run a command in a running container (e.g., docker exec -it my_container bash).
docker exec -it <container_id>/<container_name> <command>

# Blocks until a container stops, then prints the exit code.
docker wait <container_id>/<container_name>

# Forcefully stops a running container.
docker kill <container_id>/<container_name>

# Attaches to a running container to view logs or interact with it.
docker attach <container_id>/<container_name>

# Pauses all processes within a container.
docker pause <container_id>/<container_name>

# Resumes all processes within a paused container.
docker unpause <container_id>/<container_name>

# Shows running processes within a container.
docker top <container_id>/<container_name>

# Displays a list of port mappings for a container.
docker port <container_id>/<container_name>

# Opens an interactive shell inside a running container.
docker exec -it <container_id>/<container_name> /bin/bash

# Run a container inside a running container
docker 