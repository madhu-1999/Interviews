#docker #devops 
# Data persistence in Docker
When a container is created from an image, a __temporary__ writeable layer is created on top of the image layer. Thus, when a container is __deleted__, all the data in the writeable layer is lost.

>[!note] 
>When a container is stopped, it still exists and can be restarted, so the data is not destroyed.

To persist data in a container Docker provides two options
# Bind volumes
+ A file or directory on host machine is mounted from host onto container.
+ Any changes made in mounted file/dir are reflected in both host and container.
	+ If host makes a change, visible in container and vice versa
+ Can make mount readonly, so that container can only read files in the directory.
+ If mount point in the container already has some files, on mounting those files will be __obscured__ from view. They will become visible again after mount is disabled.
## Creating a bind volume

```bash
# Creates a bind mount
# Directory specified in <container-path> must already exist
docker run -v <host-path>:<container-path> <image-name>

docker run --mount type=bind,src=<host-path>,dst=<container-path> <image-name>

# Read only bind mount
docker run -v <host-path>:<container-path>:ro <image-name>
docker run --mount type=bind,src=<host-path>,dst=<container-path>,readonly <image-name>
 ```

>[!note]
>Since a Dockerfile specifies how to build a image, not how to use it, we cannot specify bind mounts in it.

```bash
# [[Docker Compose]] file
version: '3.1'

services:
  mycontainer:
    image: myimage
    build: .
    volumes:
      - '/path/on/docker/host:/path/inside/container'  
```
## Disadvantages
+ Since bind mounts depend on a specific directory structure on the host, a container configured with a bind mount may fail to run on a different machine that lacks that path.
+ Since a bind mount gives a container free access to add/delete/change anything within the mounted directory, it can become a security issue if a container is running some malicious code.
+  Because the data is stored on the host, managing (ex: backups) has to be done with host-specific tools and processes, not with Docker commands.
# Volumes (Preferred)
+ Persistent data stores created and managed by Docker
	+ When a volume is created, Docker stores its contents in a directory on the host machine. On Linux, typically `/var/lib/docker/volumes`.
	+ A volume is isolated from the host machine and managed by Docker daemon.
	+ To modify files in the volume from host machine, it is recommended to use [[Docker CLI]].
+ Data stored in volumes does not increase container size, since it exists only on the host machine.
	+ When mounted, volume is associated with a container directory. Shared directory contents same as bind mount.
	+ If _empty_ volume and non-empty container dir, container dir files and folders automatically copied to volume.
	+ If specified volume does not exist, a new volume is created by default.
+ It can be mounted onto multiple containers simultaneously.
+ Host machine for volume can be remote or cloud storage.
+ Can be made readonly so container cannot modify.
## Named volumes
+ Docker assigns a unique name (unique to host) to volume on creation, if a name is not provided. Such a volume is called _anonymous volume_.
+ If name is provided, the volume is called a _named volume_.
## Commands related to volume
```bash
# Create a volume
docker volume create my-vol

# Mount volume
docker run --mount type=volume,src=<volume-name>,dst=<mount-path>
docker run --volume <volume-name>:<mount-path>

# Mount read only volume
docker run --mount type=volume,src=myvolume,dst=/data,ro
docker run -v myvolume:/data:ro

# List all volumes
docker volume ls

# List all info for a specific volume
docker volume inspect <volume-name>

# Deleting a volume
# Unmounting happens implicitly when container is deleted
docker volume rm my-vol

# Delete any unused volumes i.e. volumes not being referenced by any container
docker volume prune
```
