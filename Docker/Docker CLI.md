#docker #devops 
# Commonly used commands
## Run containers
1. Run container using  docker image `example-image:latest`. Process runs in the foreground (^C to exit in terminal).
```bash
docker run example-image:latest
```
2. `--entrypoint` : Runs command in argument right after container starts running.
```bash
docker run example-image:latest --entrypoint <command>
```
3. `--rm`: Delete container after exiting (^C on terminal).
```bash
docker run --rm example-image:latest
```
4. `-d`: Detach container from terminal i.e. runs in background
```bash
docker run -d example-image:latest
```
5. `--name`: give container a name
```bash
docker run --name mycontainer example-image:latest
```
6. `-p`: Port mapping from host to container.
```bash
docker run -p <host-port> : <container-port> example-image:latest
# Access application in container at localhost:8080, provided it is listening on port 80 inside the container.
docker run -p 8080:80 example-image:latest
```
8. `-v` : Bind mount host dir to container dir. Shared directory contents visible on both sides.
```bash
docker run -v <host-path>:<container-path> example-image:latest
```
## Manage containers
1. List all containers currently running on host.
```bash
docker ps
```
2. List all containers including stopped ones
```bash
docker ps -a
```
3. Save current state of container to a new image
```bash
docker commit <container-name/-id> new-image:latest
```
4. Force stop a container running in the foreground
```bash
docker kill <container-name/-id>
```
5. Start/stop a container
```bash
# Start a stopped container
docker start <container-name/-id>
# Stop a running container
docker stop <container-name/-id>
```
6. Delete a running container
```bash
docker rm <container-name/-id>
# Force delete a running container
docker rm -f <container-name/-id>
```
## Copying files
1. Copy to container
```bash
docker cp <path-to-file> <container-name/-id>:<path-to-folder>
# Copy example.txt to /data folder in mycontainer
docker cp example.txt mycontainer:/data
```
2. Copy from container to host
```bash
docker cp <container-name/-id>:<path-to-file> <path-to-file>
# Copy example.txt from container to host's example.txt file
docker cp mycontainer:/data/example.txt /demo/example.txt
```
## Execute commands inside container
1. Access container terminal. `-it` tag allows us to interact with the terminal
```bash
# Gives access to bash terminal inside container
# Can change /bin/bash to a different shell if required
docker exec -it <container-name/-id> /bin/bash
```
2. Run a command inside running container
```bash
docker exec <container-name/-id> <command>
```
## Manage images
1.  List all stored images on the host machine
```bash
docker images
```
2. Delete image by ID/tag
```bash
docker rmi <image-id/tag>
```
3. Pull an image from a registry
```bash
docker pull [registry-host/][username/]repository-name[:tag]

# For Docker hub no need to specify registry-host or create a account
docker pull mysql:8.0
# mysql - repository and 8.0 - tag

# For self hosted or third party registries
docker pull company-registry.com:5000/my-app:v2
# company-registry.com:5000 - registry
# my-app - repository and v2 - tag
```
4. Push an image to a registry
```bash
docker push [registry-host/][username/]repository-name[:tag]

# For Docker hub no need to specify registry-host or create a account
docker push myapp:v2
# mysql - repository and 8.0 - tag

# For self hosted or third party registries
docker push company-registry.com:5000/my-app:v2
# company-registry.com:5000 - registry
# my-app - repository and v2 - tag
```
## Cleaning up resources
1. Delete images
```bash
# Delete dangling images i.e. images with no tags
docker image prune

# Delete unused images + dangling images
docker image prune -a
```
2. Delete unused volumes
```bash
docker volume prune
```
