---
layout: article
title: "Docker Installation on Ubuntu 22.04 LTS"
description: Complete guide to install Docker Engine on Ubuntu 22.04.4 LTS server
category: Server Setup
date: 2025-10-28
---

> **Tested Environment:** Ubuntu 22.04.4 LTS

## Overview

This guide covers the installation of Docker Engine on Ubuntu 22.04 LTS using the official Docker repository. This method ensures you get the latest stable version with easy updates.

## Prerequisites

- Ubuntu 22.04.4 LTS (or compatible version)
- User account with sudo privileges
- Internet connection

## Installation Steps

### Step 1: Update System Packages

```bash
sudo apt update
sudo apt upgrade -y
```

### Step 2: Remove Old Docker Versions

If you have any older versions of Docker installed, remove them first:

```bash
sudo apt remove docker docker-engine docker.io containerd runc
```

### Step 3: Install Required Dependencies

```bash
sudo apt install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

### Step 4: Add Docker's Official GPG Key

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

### Step 5: Set Up Docker Repository

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Step 6: Install Docker Engine

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Step 7: Verify Installation

```bash
sudo docker --version
```

Expected output:
```
Docker version 24.x.x, build xxxxxxx
```

### Step 8: Test Docker Installation

```bash
sudo docker run hello-world
```

This command downloads a test image and runs it in a container. If successful, you'll see a "Hello from Docker!" message.

## Post-Installation Configuration

### Add User to Docker Group

To run Docker commands without `sudo`:

```bash
# Add current user to docker group
sudo usermod -aG docker $USER

# Apply group changes (or logout/login)
newgrp docker
```

Verify you can run Docker without sudo:

```bash
docker run hello-world
```

### Enable Docker to Start on Boot

```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

### Check Docker Service Status

```bash
sudo systemctl status docker
```

## Docker Compose Installation

Docker Compose is now included as a plugin. Verify installation:

```bash
docker compose version
```

Expected output:
```
Docker Compose version v2.x.x
```

## Useful Docker Commands

### Service Management

```bash
# Start Docker service
sudo systemctl start docker

# Stop Docker service
sudo systemctl stop docker

# Restart Docker service
sudo systemctl restart docker

# View Docker service logs
sudo journalctl -u docker.service
```

### Container Management

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Stop a container
docker stop <container_id>

# Remove a container
docker rm <container_id>

# Remove all stopped containers
docker container prune
```

### Image Management

```bash
# List downloaded images
docker images

# Pull an image from Docker Hub
docker pull <image_name>:<tag>

# Remove an image
docker rmi <image_id>

# Remove unused images
docker image prune
```

### System Information

```bash
# Display system-wide information
docker info

# Show Docker disk usage
docker system df

# Remove all unused data
docker system prune -a
```

## Testing with a Sample Application

### Run Nginx Container

```bash
docker run -d -p 8080:80 --name my-nginx nginx:latest
```

Access Nginx in your browser:
```
http://your-server-ip:8080
```

Stop and remove the container:
```bash
docker stop my-nginx
docker rm my-nginx
```

### Run Ubuntu Container

```bash
docker run -it ubuntu:22.04 bash
```

This starts an interactive Ubuntu container. Type `exit` to leave.

## Docker Configuration Files

### Main Configuration Locations

```bash
# Docker daemon configuration
/etc/docker/daemon.json

# Docker service configuration
/lib/systemd/system/docker.service

# Docker data directory
/var/lib/docker/
```

### Configure Docker Daemon

Create or edit `/etc/docker/daemon.json`:

```bash
sudo nano /etc/docker/daemon.json
```

Example configuration:

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "storage-driver": "overlay2"
}
```

Restart Docker after changes:

```bash
sudo systemctl restart docker
```

## Troubleshooting

### Permission Denied Error

If you get "permission denied" when running Docker:

```bash
# Check if user is in docker group
groups $USER

# If not in docker group, add user
sudo usermod -aG docker $USER

# Logout and login again, or run
newgrp docker
```

### Docker Service Won't Start

```bash
# Check service status
sudo systemctl status docker

# View detailed logs
sudo journalctl -xeu docker.service

# Check for configuration errors
sudo dockerd --validate
```

### Port Already in Use

If a port is already in use:

```bash
# Find process using port 8080
sudo lsof -i :8080

# Kill the process
sudo kill -9 <PID>
```

### Disk Space Issues

```bash
# Check disk usage
docker system df

# Clean up unused resources
docker system prune -a --volumes

# Remove specific items
docker container prune  # Remove stopped containers
docker image prune -a   # Remove unused images
docker volume prune     # Remove unused volumes
```

## Security Best Practices

### 1. Keep Docker Updated

```bash
sudo apt update
sudo apt upgrade docker-ce docker-ce-cli containerd.io
```

### 2. Use Official Images

Always prefer official images from Docker Hub:

```bash
docker pull nginx:latest  # Official image
```

### 3. Run Containers as Non-Root

Avoid running containers with root privileges when possible:

```bash
docker run --user 1000:1000 <image>
```

### 4. Limit Container Resources

```bash
# Limit memory
docker run --memory="512m" <image>

# Limit CPU
docker run --cpus="1.5" <image>
```

### 5. Enable Docker Content Trust

```bash
export DOCKER_CONTENT_TRUST=1
```

## Uninstalling Docker

If you need to uninstall Docker:

```bash
# Stop Docker service
sudo systemctl stop docker

# Remove Docker packages
sudo apt purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Remove Docker data (WARNING: This deletes all images, containers, volumes)
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

## Next Steps

After installing Docker, consider:

1. **Learn Docker Compose**: Create multi-container applications
2. **Explore Docker Hub**: Find pre-built images for your needs
3. **Set up private registry**: For production environments
4. **Configure monitoring**: Use tools like Prometheus and Grafana
5. **Implement CI/CD**: Integrate Docker into your deployment pipeline

## Additional Resources

- [Official Docker Documentation](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)

## Common Use Cases

### Run MySQL Database

```bash
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  -p 3306:3306 \
  mysql:8.0
```

### Run PostgreSQL Database

```bash
docker run -d \
  --name postgres-db \
  -e POSTGRES_PASSWORD=my-secret-pw \
  -p 5432:5432 \
  postgres:15
```

### Run Redis Cache

```bash
docker run -d \
  --name redis-cache \
  -p 6379:6379 \
  redis:latest
```

## Verification Checklist

- [ ] Docker service is running
- [ ] User added to docker group
- [ ] Can run `docker ps` without sudo
- [ ] `hello-world` container runs successfully
- [ ] Docker Compose is installed and working
- [ ] Docker starts automatically on boot

## References

- Docker Engine: Container runtime and daemon
- Docker Compose: Multi-container orchestration tool
- containerd: Industry-standard container runtime
- Default Docker port: 2375 (HTTP), 2376 (HTTPS)
