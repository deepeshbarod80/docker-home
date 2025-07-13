
# Docker Prune: A Comprehensive Guide

## What is Docker Prune?

Docker prune is a set of commands that help clean up unused Docker objects, reclaiming disk space and maintaining an efficient Docker environment. The `docker prune` family of commands removes stopped containers, unused networks, dangling images, and other resources that are no longer needed.

## Basic Prune Commands

### 1. `docker system prune`
- Removes all stopped containers, dangling images, and unused networks.

```bash
docker system prune
```

### 2. `docker container prune`
- Removes all stopped containers:

```bash
docker container prune
```

### 3. `docker image prune`
Removes dangling images (images not referenced by any container):

```bash
docker image prune
```

- To remove all unused images (not just dangling ones):

```bash
docker image prune -a
```

### 4. `docker volume prune`
Removes unused volumes:

```bash
docker volume prune
```

### 5. `docker network prune`
Removes unused networks:

```bash
docker network prune
```

## Advanced Prune Options

### Filtering with `--filter`
You can filter what gets pruned using various criteria:

```bash
# Prune images older than 24 hours
docker image prune -a --filter "until=24h"

# Prune containers with specific label
docker container prune --filter "label=environment=test"
```

### Force removal without prompt
Use `-f` or `--force` to skip confirmation:

```bash
docker system prune -f
```

### Pruning everything including build cache
To include builder cache in pruning:

```bash
docker system prune -a --volumes
```

## Use Cases in DevOps Environments

### 1. CI/CD Pipelines
In continuous integration environments, prune between builds to maintain clean environments:

```bash
# In your build script
docker system prune -f
```

### 2. Production Environment Maintenance
Schedule regular pruning in production during low-traffic periods:

```bash
# Daily at 2 AM via cron
0 2 * * * /usr/bin/docker system prune -f --filter "until=48h"
```

### 3. Development Environments
For developers to keep their local environments clean:

```bash
# Add to shell aliases
alias docker-clean='docker system prune -f && docker volume prune -f'
```

### 4. Kubernetes Nodes
On Kubernetes worker nodes to clean up unused Docker resources:

```bash
# As part of node maintenance scripts
docker system prune -af --volumes
```

## Best Practices for Different Environments

### Development
- Run `docker system prune` weekly or when disk space is low
- Consider using `docker image prune -a` monthly to clean up old development images
- Be cautious with volume pruning if you have important data

### Staging
- Schedule pruning after each deployment
- Keep recent images for rollback purposes:

```bash
docker image prune -a --filter "until=72h"
```

### Production
- Always use filters to retain recent resources for rollback
- Schedule pruning during maintenance windows
- Consider keeping more resources than in other environments:

```bash
# Keep resources from last 7 days
docker system prune --filter "until=168h" -f
```

### CI/CD Systems
- Prune aggressively between jobs:

```bash
# In your pipeline
docker system prune -a -f --volumes
```

## Safety Considerations

1. **Backup volumes** before pruning if they might contain important data
2. **Verify what will be removed** first with dry-run (Docker 20.04+):

```bash
docker system prune --dry-run
```

3. **Exclude specific resources** using labels:

```bash
# Mark resources to keep
docker run --label "do.not.prune=true" my-image

# Then prune excluding these
docker container prune --filter "label!=do.not.prune=true"
```

## Automation Examples

### Cron Job for Regular Pruning
```bash
# /etc/cron.daily/docker-prune
#!/bin/sh
docker system prune -f --filter "until=48h"
docker volume prune -f
```

### Ansible Playbook for Cluster-Wide Pruning
```yaml
- name: Prune Docker nodes
  hosts: docker_nodes
  tasks:
    - name: Prune Docker system
      command: docker system prune -f --filter "until=72h"
      
    - name: Prune volumes
      command: docker volume prune -f
```

## Monitoring Prune Impact

Check disk usage before and after pruning:

```bash
# Before
docker system df

# After pruning
docker system df -v
```

## Conclusion

Docker prune commands are essential tools for maintaining healthy Docker environments across all stages of DevOps workflows. By implementing targeted pruning strategies in development, staging, and production environments, teams can optimize resource usage while maintaining the ability to roll back when needed. Always combine pruning with proper monitoring and backup strategies to ensure system reliability.