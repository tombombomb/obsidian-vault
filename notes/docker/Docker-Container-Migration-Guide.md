# Docker Container Migration Guide
## Transferring Docker Containers from Docker Desktop to Ubuntu Server

---

## Overview

This guide covers how to completely migrate Docker containers, volumes, networks, and configurations from Docker Desktop (Windows/Mac) to an Ubuntu server.

---

## Method 1: Save and Transfer Image (Simple Migration)

### On Docker Desktop Machine

**Save a single image:**
```bash
# Save the image to a tar file
docker save -o mycontainer.tar image_name:tag

# Or if you want to save a running container, first commit it to an image
docker commit container_name my_new_image:tag
docker save -o mycontainer.tar my_new_image:tag
```

**Transfer to Ubuntu server:**
```bash
# Using scp
scp mycontainer.tar user@ubuntu-server-ip:/path/to/destination/

# Or using rsync for large files (with progress bar)
rsync -avz --progress mycontainer.tar user@ubuntu-server-ip:/path/to/destination/
```

### On Ubuntu Server

```bash
# Load the image
docker load -i mycontainer.tar

# Verify it loaded
docker images

# Run the container
docker run -d [your_options] image_name:tag
```

---

## Method 2: Complete Migration (Everything)

This method transfers containers, volumes, networks, and all configurations.

### Step 1: Backup Everything on Docker Desktop

**List current resources:**
```bash
# See all containers (including stopped ones)
docker ps -a

# See all volumes
docker volume ls

# See all networks
docker network ls

# See all images
docker images
```

**Save all images:**
```bash
# Save all images to a single tar file
docker save -o all-images.tar $(docker images -q)

# Or save specific images
docker save -o my-images.tar image1:tag image2:tag image3:tag
```

**Backup all volumes:**
```bash
# Create a backup directory
mkdir docker-backup
cd docker-backup

# Backup each volume individually (repeat for each volume)
docker run --rm -v volume_name:/data -v $(pwd):/backup ubuntu tar czf /backup/volume_name.tar.gz -C /data .

# Or use this script to backup ALL volumes at once:
for volume in $(docker volume ls -q); do
  docker run --rm -v $volume:/data -v $(pwd):/backup ubuntu tar czf /backup/$volume.tar.gz -C /data .
done
```

### Step 2: Export Container Configurations

**If using Docker Compose (recommended):**
```bash
# Just copy your docker-compose.yml file
cp docker-compose.yml ~/docker-backup/
```

**If NOT using Docker Compose:**
```bash
# Get container configuration
docker inspect container_name > container_name-config.json

# Or use autocompose to generate docker-compose.yml from running containers
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  ghcr.io/red5d/docker-autocompose container_name > container_name-compose.yml
```

### Step 3: Transfer to Ubuntu Server

```bash
# Transfer everything (from your Docker Desktop machine)
scp -r docker-backup/ user@ubuntu-server-ip:/home/user/

# For large files, use rsync with compression and progress
rsync -avz --progress docker-backup/ user@ubuntu-server-ip:/home/user/docker-backup/
```

### Step 4: Restore on Ubuntu Server

**Load images:**
```bash
cd /home/user/docker-backup
docker load -i all-images.tar
```

**Restore volumes:**
```bash
# Create and restore each volume
for volume_backup in *.tar.gz; do
  volume_name="${volume_backup%.tar.gz}"
  docker volume create "$volume_name"
  docker run --rm -v "$volume_name":/data -v $(pwd):/backup ubuntu tar xzf /backup/"$volume_backup" -C /data
done
```

**Recreate containers:**
```bash
# If you have docker-compose.yml
docker compose up -d

# Or manually using the inspect JSON or recreate commands
docker run -d [options from your config] image_name
```

---

## Method 3: Docker Registry (Cloud Migration)

Best for frequent migrations or multiple destinations.

**Push from Docker Desktop:**
```bash
# Tag the image
docker tag image_name:tag your_dockerhub_username/image_name:tag

# Login to Docker Hub
docker login

# Push to Docker Hub
docker push your_dockerhub_username/image_name:tag
```

**Pull on Ubuntu server:**
```bash
# Login to Docker Hub
docker login

# Pull the image
docker pull your_dockerhub_username/image_name:tag

# Run the container
docker run -d [your_options] your_dockerhub_username/image_name:tag
```

---

## Automated Scripts

### Source Machine Backup Script

Save this as `backup-docker.sh`:

```bash
#!/bin/bash
BACKUP_DIR="docker-migration"
mkdir -p $BACKUP_DIR

echo "Starting Docker backup..."

# Save all images
echo "Backing up images..."
docker save -o $BACKUP_DIR/images.tar $(docker images -q)

# Backup all volumes
echo "Backing up volumes..."
for volume in $(docker volume ls -q); do
  echo "  - Backing up volume: $volume"
  docker run --rm -v $volume:/data -v $(pwd)/$BACKUP_DIR:/backup \
    ubuntu tar czf /backup/$volume.tar.gz -C /data .
done

# Copy docker-compose files if they exist
find . -maxdepth 2 -name "docker-compose.yml" -exec cp {} $BACKUP_DIR/ \;

echo "Backup complete in $BACKUP_DIR/"
echo "Total size:"
du -sh $BACKUP_DIR
```

**Usage:**
```bash
chmod +x backup-docker.sh
./backup-docker.sh
```

### Destination Server Restore Script

Save this as `restore-docker.sh`:

```bash
#!/bin/bash
BACKUP_DIR="docker-migration"

echo "Starting Docker restoration..."

# Load images
echo "Loading images..."
docker load -i $BACKUP_DIR/images.tar

# Restore volumes
echo "Restoring volumes..."
for volume_file in $BACKUP_DIR/*.tar.gz; do
  volume_name=$(basename "$volume_file" .tar.gz)
  echo "  - Restoring volume: $volume_name"
  docker volume create "$volume_name"
  docker run --rm -v "$volume_name":/data -v $(pwd)/$BACKUP_DIR:/backup \
    ubuntu tar xzf /backup/$volume_name.tar.gz -C /data
done

echo "Restoration complete!"
echo "To start containers, run: docker compose up -d"
```

**Usage:**
```bash
chmod +x restore-docker.sh
./restore-docker.sh
```

---

## Important Notes

### Volume Data
- Volumes contain your persistent data (databases, uploaded files, etc.)
- Always backup volumes before migration
- Test restored volumes before deleting originals

### Port Mappings
- Remember to replicate port mappings on the new server
- Check for port conflicts on the Ubuntu server
- Update firewall rules if necessary

### Environment Variables
- Export environment variables from original containers
- Check `.env` files and include them in backup
- Verify environment-specific configs

### Networks
- Custom networks may need to be recreated
- Check network configurations with `docker network inspect network_name`
- Recreate networks before starting containers if needed

### Permissions
- Ensure proper file permissions on Ubuntu server
- Volume mounts may need user/group adjustments
- Check SELinux or AppArmor policies if applicable

---

## Verification Checklist

After migration, verify:

- [ ] All images loaded: `docker images`
- [ ] All volumes restored: `docker volume ls`
- [ ] Containers start successfully: `docker ps`
- [ ] Application works as expected
- [ ] Data persists correctly
- [ ] Networking functions properly
- [ ] Logs show no errors: `docker logs container_name`

---

## Troubleshooting

### Container won't start
```bash
# Check logs
docker logs container_name

# Inspect container config
docker inspect container_name

# Check for port conflicts
netstat -tulpn | grep PORT_NUMBER
```

### Volume data missing
```bash
# Verify volume exists
docker volume ls

# Inspect volume
docker volume inspect volume_name

# Check volume contents
docker run --rm -v volume_name:/data ubuntu ls -la /data
```

### Permission issues
```bash
# Fix volume permissions
docker run --rm -v volume_name:/data ubuntu chown -R 1000:1000 /data

# Or match your user ID
docker run --rm -v volume_name:/data ubuntu chown -R $(id -u):$(id -g) /data
```

---

## Additional Resources

- [Docker Save Documentation](https://docs.docker.com/engine/reference/commandline/save/)
- [Docker Volume Backup Guide](https://docs.docker.com/storage/volumes/#back-up-restore-or-migrate-data-volumes)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

---

*Created: 2026-01-12*
*Tags: #docker #migration #devops #ubuntu #containers*
