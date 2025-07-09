# Docker Commands Reference

This document provides a comprehensive list of Docker commands for managing containers, images, networks, volumes, and Docker Compose on a Linux system. The commands are organized by category for clarity and ease of use.

## Version and System Information

1. **docker --version**  
   Displays the installed Docker version.  
   ```bash
   docker --version
   ```

2. **docker version**  
   Displays detailed Docker version information for client and server.  
   ```bash
   docker version
   ```

3. **docker info**  
   Displays system-wide information about Docker.  
   ```bash
   docker info
   ```

## Image Management

4. **docker pull <image_name>**  
   Downloads a Docker image from a registry (e.g., Docker Hub).  
   ```bash
   docker pull ubuntu
   docker pull myrepo/myimage:v1
   ```

5. **docker images**  
   Lists all available images on the system.  
   ```bash
   docker images
   ```

6. **docker rmi <image_id>**  
   Removes a Docker image by its ID.  
   ```bash
   docker rmi abc123
   ```

7. **docker build -t <image_name> <Dockerfile_path>**  
   Builds an image from a Dockerfile in the specified path.  
   ```bash
   docker build -t my_image .
   ```

8. **docker tag <image_id> <repository_tag>**  
   Tags an image with a new repository and tag name.  
   ```bash
   docker tag abc123 myrepo/myimage:v1
   ```

9. **docker push <repository_tag>**  
   Pushes an image to a registry.  
   ```bash
   docker push myrepo/myimage:v1
   ```

10. **docker save -o <filename.tar> <image_name>**  
    Saves an image to a tar archive.  
    ```bash
    docker save -o my_image.tar my_image
    ```

11. **docker load -i <filename.tar>**  
    Loads an image from a tar archive.  
    ```bash
    docker load -i my_image.tar
    ```

12. **docker history <image_name>**  
    Displays the history of an image’s layers.  
    ```bash
    docker history ubuntu
    ```

13. **docker image prune**  
    Removes unused or dangling images.  
    ```bash
    docker image prune
    ```

## Container Management

14. **docker run <image_name>**  
    Starts a new container from the specified image. Use `-p` for port mapping, `-it` for interactive mode.  
    ```bash
    docker run -p 8080:8080 myimage
    docker run -it ubuntu /bin/bash
    ```

15. **docker ps**  
    Lists all running containers.  
    ```bash
    docker ps
    ```

16. **docker ps -a**  
    Lists all containers, including stopped ones.  
    ```bash
    docker ps -a
    ```

17. **docker start <container_id>**  
    Starts a stopped container.  
    ```bash
    docker start abc123
    ```

18. **docker stop <container_id>**  
    Stops a running container.  
    ```bash
    docker stop abc123
    ```

19. **docker restart <container_id>**  
    Restarts a container.  
    ```bash
    docker restart abc123
    ```

20. **docker rm <container_id>**  
    Removes a stopped container.  
    ```bash
    docker rm abc123
    ```

21. **docker rm $(docker ps -a -q)**  
    Removes all stopped containers.  
    ```bash
    docker rm $(docker ps -a -q)
    ```

22. **docker stop $(docker ps -q)**  
    Stops all running containers.  
    ```bash
    docker stop $(docker ps -q)
    ```

23. **docker exec -it <container_id> <command>**  
    Executes a command inside a running container, typically for interactive shell access.  
    ```bash
    docker exec -it abc123 /bin/bash
    docker exec abc123 ls /app
    ```

24. **docker logs <container_id>**  
    Displays logs from a container. Use `-f` for real-time logs.  
    ```bash
    docker logs abc123
    docker logs -f abc123
    ```

25. **docker inspect <container_id>**  
    Displays detailed information about a container.  
    ```bash
    docker inspect abc123
    ```

26. **docker diff <container_id>**  
    Shows changes made to a container’s filesystem.  
    ```bash
    docker diff abc123
    ```

27. **docker commit <container_id> <image_name>**  
    Creates a new image from a container’s changes.  
    ```bash
    docker commit abc123 my_custom_image
    ```

28. **docker export <container_id> -o <filename.tar>**  
    Exports a container’s filesystem as a tar archive.  
    ```bash
    docker export abc123 -o container_backup.tar
    ```

29. **docker import <filename.tar> <image_name>**  
    Imports a tar archive as a Docker image.  
    ```bash
    docker import container_backup.tar new_image_name
    ```

30. **docker pause <container_id>**  
    Pauses all processes within a container.  
    ```bash
    docker pause abc123
    ```

31. **docker unpause <container_id>**  
    Resumes all processes in a paused container.  
    ```bash
    docker unpause abc123
    ```

32. **docker rename <old_container_name> <new_container_name>**  
    Renames a running or stopped container.  
    ```bash
    docker rename my_old_container my_new_container
    ```

33. **docker update --cpu-shares <value> <container_id>**  
    Updates CPU shares for a container.  
    ```bash
    docker update --cpu-shares 512 abc123
    ```

34. **docker update --memory <value> <container_id>**  
    Updates memory limit for a container.  
    ```bash
    docker update --memory 512m abc123
    ```

35. **docker attach <container_id>**  
    Attaches to a running container’s standard input, output, and error streams.  
    ```bash
    docker attach abc123
    ```

36. **docker cp <container_id>:<source_path> <destination_path>**  
    Copies files from a container to the host or vice versa.  
    ```bash
    docker cp abc123:/app/file.txt /tmp
    docker cp /tmp/file.txt abc123:/app
    ```

37. **docker wait <container_id>**  
    Blocks until a container stops, then prints its exit code.  
    ```bash
    docker wait abc123
    ```

38. **docker kill <container_id>**  
    Forcefully stops a running container.  
    ```bash
    docker kill abc123
    ```

39. **docker top <container_id>**  
    Shows running processes within a container.  
    ```bash
    docker top abc123
    ```

40. **docker port <container_id>**  
    Displays a list of port mappings for a container.  
    ```bash
    docker port abc123
    ```

41. **docker stats**  
    Displays a live stream of resource usage statistics for running containers.  
    ```bash
    docker stats
    ```

42. **docker events**  
    Displays real-time events from the Docker daemon.  
    ```bash
    docker events
    ```

## Network Management

43. **docker network ls**  
    Lists all available Docker networks.  
    ```bash
    docker network ls
    ```

44. **docker network create <network_name>**  
    Creates a new network for containers to communicate.  
    ```bash
    docker network create my_network
    ```

45. **docker network rm <network_name>**  
    Removes an existing Docker network.  
    ```bash
    docker network rm my_network
    ```

46. **docker network inspect <network_name>**  
    Shows detailed information about a specific network.  
    ```bash
    docker network inspect my_network
    ```

47. **docker network prune**  
    Removes unused networks.  
    ```bash
    docker network prune
    ```

## Volume Management

48. **docker volume ls**  
    Lists all Docker volumes.  
    ```bash
    docker volume ls
    ```

49. **docker volume create <volume_name>**  
    Creates a new Docker volume.  
    ```bash
    docker volume create my_volume
    ```

50. **docker volume rm <volume_name>**  
    Removes a Docker volume.  
    ```bash
    docker volume rm my_volume
    ```

51. **docker volume inspect <volume_name>**  
    Shows detailed information about a specific volume.  
    ```bash
    docker volume inspect my_volume
    ```

52. **docker volume prune**  
    Removes unused volumes.  
    ```bash
    docker volume prune
    ```

## Docker Compose

53. **docker-compose up**  
    Starts containers defined in a `docker-compose.yml` file.  
    ```bash
    docker-compose up
    ```

54. **docker-compose down**  
    Stops and removes containers, networks, images, and volumes created by `docker-compose up`.  
    ```bash
    docker-compose down
    ```

55. **docker-compose ps**  
    Lists all containers in a Docker Compose environment.  
    ```bash
    docker-compose ps
    ```

56. **docker-compose build**  
    Builds or rebuilds services defined in the `docker-compose.yml` file.  
    ```bash
    docker-compose build
    ```

57. **docker-compose stop**  
    Stops running containers without removing them.  
    ```bash
    docker-compose stop
    ```

58. **docker-compose restart**  
    Restarts all services in a Docker Compose setup.  
    ```bash
    docker-compose restart
    ```

59. **docker-compose logs**  
    Displays output from running containers managed by Docker Compose.  
    ```bash
    docker-compose logs
    ```

60. **docker-compose rm**  
    Removes stopped service containers.  
    ```bash
    docker-compose rm
    ```

61. **docker-compose pull**  
    Pulls images for services defined in the `docker-compose.yml` file.  
    ```bash
    docker-compose pull
    ```

62. **docker-compose exec <service_name> <command>**  
    Executes a command inside a running service container.  
    ```bash
    docker-compose exec web ls /app
    ```

63. **docker-compose scale <service_name>=<num_instances>**  
    Scales the number of containers for a service.  
    ```bash
    docker-compose scale web=3
    ```

## Registry Management

64. **docker login**  
    Logs into a Docker registry (e.g., Docker Hub or private).  
    ```bash
    docker login
    ```

65. **docker logout**  
    Logs out from a Docker registry.  
    ```bash
    docker logout
    ```

## System Cleanup

66. **docker system prune**  
    Cleans up unused containers, networks, images (dangling), and volumes.  
    ```bash
    docker system prune
    ```

## Daemon Management

67. **docker daemon**  
    Runs the Docker daemon manually (typically done automatically).  
    ```bash
    docker daemon
    ```



## Notes
- Replace `<container_id>`, `<image_id>`, `<image_name>`, `<network_name>`, `<volume_name>`, `<repository_tag>`, `<filename.tar>`, `<source_path>`, `<destination_path>`, `<old_name>`, `<new_name>`, `<service_name>`, `<num_instances>`, and `<value>` with appropriate values.
- Some commands (e.g., `docker daemon`) may require root privileges or specific configurations.
- Use `docker <command> --help` for detailed options and usage.

---