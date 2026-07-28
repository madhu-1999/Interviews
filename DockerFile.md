#docker #devops 
# Prerequisite
[Docker Image](Docker%20Image.md)
# Overview
+ Text document used to create a image
	+ Contains instructions on commands to run, files to copy etc.. to configure environment.
# Common Instructions
Reference: [Dockerfile Cheat Sheet](https://kapeli.com/cheat_sheets/Dockerfile.docset/Contents/Resources/Documents/index)
```Dockerfile title:Dockerfile
FROM python:3.13
WORKDIR /usr/local/app

# Install application dependencies
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Copy in source code
COPY src ./src
EXPOSE 8080

# Setup an app user so that container doesn't run as root user (default behavior)
RUN useradd app
USER app

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8080"]

```
## Explanation
+ `FROM <image>` : specify base image that will be extended.  
+ `WORKDIR <path>` :  specify **working directory in image** , where files will be copied and commands executed.
+ `COPY <host-path> <image-path>` : Copy files from host to given path in image working directory.
+ `RUN <command>` : executes specified command.
+ `ENV <name> <value>` : Set environment variable **inside container**.
+ `EXPOSE <port-number>`: Indicates the port that image (then container) will expose.
+ `USER <user/uid>` : Sets default user for running commands. Default is root.
+ `CMD ["<command>", "<arg1>"]` : Default command container runs after it is setup.

> [!note]
> + Dockerfile has __no extension__
> 	+ Extension can be added if required. 
> ```bash wrap ln:false
> docker build -f Dockerfile.prod -t my-app:production .
> ```
> __-f__: specifies name of Dockerfile to use
> __-t__: tags resulting image
> ==__.__== : sets build context to current directory.

# Creating an image

```bash ln:false
# run in same dir as Dockerfile
docker build .
# . => working dir in container 
```

+ When `docker build` is run, the Docker daemon converts every instruction in the Dockerfile to an image layer.
## Caching layers
+ To make subsequent rebuilds efficient, Docker caches image layers.
	+ If some layer changes, all layers above it are also invalidated and rebuilt from scratch to prevent inconsistencies.
	+ Since unchanged layers are cached, rebuilding is faster for those layers.
+ To check if image is created properly, use command: `docker images` which lists all images on host machine.
## .dockerignore
+ When building an image, everything in __build context__ (current dir on host machine) is sent to Docker daemon for packaging.
+ There may be some files in the directory that are not needed for the container to function properly.
+ Can ignore such files by adding them to __.dockerignore__ file. (like .gitignore)
+ Speeds up build process and reduces image size.

```title:.dockerignore
# Exclude version control directories
.git
.svn

# Exclude Node.js dependencies (assuming you install them during build)
node_modules

# Exclude Python cache and compiled files
__pycache__
*.pyc
*.pyo

# Exclude build and distribution directories
build
dist

# Exclude log files and temporary directories
*.log
tmp/

# Exclude OS-specific files
.DS_Store
Thumbs.db
```
