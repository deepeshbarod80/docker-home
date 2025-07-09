
# Docker concepts

## Components of Docker
* Docker Client
* Docker Engine
* docker Daemon (Container Runtime or dockerd)
* Dockerfile
* Images
* Networks (bridge, host, overlay)
* Volumes
* Containers
* Services
* Compose
* Registry (Docker Hub, ECR, ACR)
* Environment Variables
* Container Lifecycle (pause, stop, kill)
* Logs and monitoring
* Troubleshooting
* Port Mapping
* Image Layering and Caching
* Volumes and Bind Mounts
* Resource Constraints
* Multi-Stage Builds
* Container Networking
* Container Security
* Docker Context
* Checkpoint and Restore
* Docker plugins
* Buildkit
* Docker Events
* Rootless Docker



## Docker Lifecycle
Docker provides commands to manage the state of containers: pause, stop, and kill. Each command serves a distinct purpose in the container lifecycle.

### Docker pause and unpause:
- This command suspends all processes running inside the specified container. 
- It utilizes the cgroups freezer on Linux, effectively freezing the container's processes without terminating them. 
- The container remains in memory and retains its state, but it does not consume CPU cycles. This is useful for temporarily halting a container for debugging or resource management without losing its current state. 

```bash
docker pause <container_name_or_id>
docker unpause <container_name_or_id>
```

### Docker start, restart, and stop:
docker stop <container_name_or_id>:
* This command initiates a graceful shutdown of the specified container. 
* It sends a SIGTERM signal to the main process inside the container, allowing the application to perform cleanup operations and exit gracefully. 
* Docker then waits for a default timeout (typically 10 seconds) for the container to exit. If the container does not exit within this timeout, a SIGKILL signal is sent to forcibly terminate the process. 
* This command is suitable for planned shutdowns where data integrity and proper resource release are important.

```bash
docker start <container_name_or_id>
docker restart <container_name_or_id>
docker stop <container_name_or_id>
```

### Docker kill:
- This command immediately terminates the specified container by sending a SIGKILL signal to its main process. 
- Unlike docker stop, docker kill does not allow for a graceful shutdown, as the SIGKILL signal cannot be caught or ignored by the process. 
- This command is used when a container needs to be stopped immediately, for example, if it is unresponsive or causing issues, and a graceful shutdown is not possible or desired. 
- While SIGKILL is the default, the --signal option can be used to send a different signal if needed.
```bash
docker kill <container_name_or_id>
```

