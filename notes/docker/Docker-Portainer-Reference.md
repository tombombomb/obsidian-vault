# Docker & Portainer Server Reference

## Server Information

- **Server IP**: 192.168.1.195
- **SSH User**: tom
- **OS**: Ubuntu 24.04 (Noble)
- **Hostname**: dockersvr

## Installation Summary

### Docker Installation (Completed: 2026-01-12)

**Docker Version**: 29.1.4

**Installed Components**:
- Docker CE (Community Edition)
- Docker CLI
- containerd.io
- Docker Buildx Plugin
- Docker Compose Plugin
- Docker CE Rootless Extras

**Installation Method**: Official Docker APT repository

### Portainer Installation

**Version**: Portainer CE (latest)
**Container ID**: 548c88897f10
**Status**: Running with auto-restart enabled

**Exposed Ports**:
- Port 9443: HTTPS web interface (recommended)
- Port 8000: Edge Agent communication

**Data Storage**:
- Volume: `portainer_data` (persists configuration and data)

## Access Information

### Portainer Web UI
- **URL**: https://192.168.1.195:9443
- **First-time setup required**: Create admin account on first access

## Common Docker Commands

### Container Management

```bash
# List all running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Start a container
docker start <container_name_or_id>

# Stop a container
docker stop <container_name_or_id>

# Restart a container
docker restart <container_name_or_id>

# Remove a container
docker rm <container_name_or_id>

# View container logs
docker logs <container_name_or_id>

# Follow container logs in real-time
docker logs -f <container_name_or_id>

# Execute command in running container
docker exec -it <container_name_or_id> /bin/bash
```

### Image Management

```bash
# List all images
docker images

# Pull an image
docker pull <image_name>:<tag>

# Remove an image
docker rmi <image_name_or_id>

# Remove unused images
docker image prune
```

### Volume Management

```bash
# List volumes
docker volume ls

# Create a volume
docker volume create <volume_name>

# Remove a volume
docker volume rm <volume_name>

# Remove unused volumes
docker volume prune
```

### System Management

```bash
# Show Docker disk usage
docker system df

# Remove all unused data (containers, networks, images, volumes)
docker system prune -a

# Show Docker system information
docker info

# Show Docker version
docker version
```

## Portainer-Specific Commands

### Managing Portainer Container

```bash
# Restart Portainer
docker restart portainer

# Stop Portainer
docker stop portainer

# Start Portainer
docker start portainer

# View Portainer logs
docker logs portainer

# Follow Portainer logs
docker logs -f portainer

# Update Portainer to latest version
docker stop portainer
docker rm portainer
docker pull portainer/portainer-ce:latest
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

## Docker Compose Basics

### Common Compose Commands

```bash
# Start services defined in docker-compose.yml
docker compose up -d

# Stop services
docker compose down

# View logs
docker compose logs

# Restart services
docker compose restart

# Pull latest images
docker compose pull

# View running services
docker compose ps
```

## SSH Connection

```bash
# Connect to server
ssh tom@192.168.1.195

# Connect and execute command
ssh tom@192.168.1.195 "docker ps"
```

## Troubleshooting

### Docker Service Issues

```bash
# Check Docker service status
sudo systemctl status docker

# Restart Docker service
sudo systemctl restart docker

# View Docker service logs
sudo journalctl -u docker -f
```

### Container Issues

```bash
# Inspect container details
docker inspect <container_name>

# Check container resource usage
docker stats

# Check container health
docker ps --filter health=unhealthy
```

### Network Issues

```bash
# List Docker networks
docker network ls

# Inspect network
docker network inspect <network_name>

# Create network
docker network create <network_name>
```

## Best Practices

1. **Regular Updates**: Keep Docker and containers updated
2. **Backups**: Backup Docker volumes and Portainer data regularly
3. **Resource Monitoring**: Monitor container resource usage via Portainer
4. **Security**:
   - Use HTTPS for Portainer access
   - Keep strong admin passwords
   - Regularly update images for security patches
5. **Logging**: Review container logs periodically for issues

## Useful Resources

- [Docker Documentation](https://docs.docker.com/)
- [Portainer Documentation](https://docs.portainer.io/)
- [Docker Hub](https://hub.docker.com/) - Container image registry

## Notes

- User `tom` is added to the docker group (sudo not required after logout/login)
- Docker service is enabled to start on boot
- Portainer is configured with `--restart=always` for automatic restart
- All Portainer data persists in the `portainer_data` volume

---

*Last Updated: 2026-01-12*
*Server: 192.168.1.195 (dockersvr)*
