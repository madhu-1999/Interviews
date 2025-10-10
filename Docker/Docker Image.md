#docker #devops 
# Overview
+ Standardized package that includes all the files, binaries, libraries and configurations needed to run a [[Container|container]].
+ They are **immutable**.
	+ To make changes to an existing image, you have to create a new image with the changes. (versioning)
	+ When a file is modified/deleted in a new layer, a copy of updated file is added to it (update) or it is masked from view (deletion). The old file stills exists in its layer. (copy-on-write).

# Advantages
+ Immutable image guarantees that the software and its environment is **reproducible** on any machine.
+ Immutability of image makes it less susceptible to tampering.
+ If new image version has a bug, can easily revert to previous version.
# Anatomy of an Image
+ Made up of layers.
	+ Each layer is an intermediate image, dependent on the layers below it.
+ When a change is made to a layer, the layer itself + all dependent layers (all layers above it) are rebuilt.
	+ Frequently changing layers should be high up in the stack.
## Container Layer
+ When a container is launched from an image, a thin writable layer, known as container layer is created on top of the image.
+ All changes made to the container during its runtime are stored here.
+ Multiple containers (on same host) can share the same image, since container layer is created on top of the image layers (immutable).
![Graphic showing the Docker containers and other layers that make up a Docker image.](https://jfrog--c.documentforce.com/servlet/servlet.ImageServer?id=01569000008kqFO&oid=00D20000000M3v0&lastMod=1631619804000)
# Disadvantages
+ Every time a change is made to an image, the layers will be **rebuilt** and will need to be and containers using the old image would have to be **redeployed** with the new image, which can be tedious.
+ Immutable nature of images requires persistent data to be stored outside the container, typically on a separate volume or database.
+ Since a new layer has to be created to modify an image, **image size keeps increasing** over time.
# Base Image
+ Image with no parent. Marks the beginning of the layer stack.
	+ Can have an empty first layer or a first layer with a minimal OS environment.
	+ `FROM scratch` creates smallest possible image (empty image).
	+ `FROM alpine` or `FROM debian` create lightweight base images (minimal OS env).
+ Allows you to build Docker images from scratch.
+ Full control over the contents of image.
# Parent image
+ First layer of Docker image, which you build upon to create your own custom image.
+ Can be a ready-made image or your own custom image.

```DockerFile
FROM python: 3.10-slim # parent image
RUN pip install requests 
```

+ Saves time since custom image inherits preconfigured environment from parent image.
# Docker manifest
+ Description of image in JSON format and image tags. Acts as a **blueprint** for a image.
	+ Other info like digital signature and how to configure container on different platforms.
+ Consider a set of images with the same high-level configuration (say mysql 8.0 setup) with each image supporting a different platform architecture such as `linux/amd64`, `linux/arm64` and `windows/amd64` .
	+  We can organize this set of images under a single image tag, say `myapp:v1`.
	+ The manifest contains details for all supported architectures under the `myapp:v1` tag.
	+ When `docker pull myapp:v1`  command is run, the docker client automatically inspects the manifest and pulls correct image for user's OS and processor.
## Workflow
1. Build individual images with a separate tag for each architecture (`myimage:v1-amd64`, `myimage:v1-arm64`) .
2. Push them to a registry (public or private).
3. Create manifest using `docker manifest create` to list different architecture specific images under a single tag `myimage:v1`
4. Push manifest to registry using `docker manifest push`.
# Container Registry
+ Catalog of container images.
+ Can `push` and `pull` images to it.
+ Can be public or private
## Types
+ __Docker Hub__
	+ Docker's official container registry (public) with over 100,000 images.
	+ Can also host and manage private images.
+ __Third party registry__ 
	+ Services where you can store, manage and secure private / public images.
	+ Eliminates operational overhead of maintaining on-premises registry.
	+ Ex: Google Container Registry, [[Amazon ECR]].
+ __Self hosted registry__
	+ On-premises hosting of images.
		+ Typically due to security or compliance requirements or to reduce latency.
	+ Need to host, manage and secure images yourself.
# Container Registry
+ Collection of related images within a registry.
+ Each image is referenced by a different tag and represents a different version of the same container deployment (not to be confused with same image different architecture).
+ Ex: `mysql` is a repository on Docker Hub that contains different versions of images for MYSQL db. (like 8.0, 11.2, 17.0)
# `docker pull` command

+ Used to pull (download) an image from a container registry.

```bash
# Naming format
docker pull [registry-host/][username/]repository-name[:tag]

# For Docker hub no need to specify registry-host or create a account
docker pull mysql:8.0
# mysql - repository and 8.0 - tag

# For self hosted or third party registries
docker pull company-registry.com:5000/my-app:v2
# company-registry.com:5000 - registry
# my-app - repository and v2 - tag
```

## Working
1. Docker client parses arguments to the `docker pull` command and recognizes `company-registry.com:5000`  is the registry name ([[Domain Name System (DNS)|Fully qualified domain name]])
2. It tries to establish a HTTPS connection to it using given address and port.
3. If registry is private, client uses credentials stored from a previous `docker login company-registry.com:5000` command.
4. Client sends request to registry to pull image `my-app:v2`.
5. Registry returns image manifest and layer data to the client, which assembles the image locally.
>[!note]
>If no registry is specified, it defaults to Docker hub servers.

# Creating images
## [[Docker CLI]]
## [[DockerFile]] (recommended)
