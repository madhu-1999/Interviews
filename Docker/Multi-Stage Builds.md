#docker #devops 
# Prerequisite
[[DockerFile]]
[[Docker Image]]
# Overview
+ Building an image from a [[DockerFile]] is divided into multiple stages.
+ Creates more optimized and smaller images than single stage build.
	+ Separate the build environment from the runtime environment.
# Use cases
## Removing bulky build dependencies
Below is an example of a single stage build Dockerfile.
To build (compile) `HelloWorld.java` , we download the [[JVM vs JRE vs JDK#JDK|JDK]] which contains compiler + [[JVM vs JRE vs JDK#JRE|JRE]]. The output (artifact) of the build stage is `HelloWorld.class` file.

```Dockerfile
# build
FROM openjdk:11-jdk
COPY HelloWorld.java .
RUN javac HelloWorld.java
# runtime
RUN java HelloWorld

# build using command:
# docker build -t helloworld:huge
```

To execute the artifact `HelloWorld.class` we only need the JRE. However, in the single stage build, our resulting image will include unnecessary files such as the Java compiler and source code, bloating image size.

Using multi-stage builds we can keep only the required files in the final stage, removing bulky build dependencies and reducing image size.

```Dockerfile
# AS <stage-name> => give stage a name
FROM openjdk:11-jdk AS build 
COPY HelloWorld.java .
RUN javac HelloWorld.java

# New parent image for new stage
FROM openjdk:11-jre AS run
# Copy only necessary files from a previous stage
# --from=<stage-name> copy files from given stage 
COPY --from=build HelloWorld.class .
RUN java HelloWorld

# build using command:
# docker build -t helloworld:small
```

By using different parent images for `build` and `run` stages, we are able to customize environment to the needs of a particular stage.
	For compiling we need JDK but for execution we only need the JRE.
The bulky build dependencies were removed in the `run` stage, by copying only required files from the `build` stage. All files not included are discarded and don't make it into the final image.

We can see the size difference between both images.
![docker images](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fjogwyigmabu9du10g3wo.png)
## Separately run different environments
In single stage builds, we would have to create different Dockerfiles for say the _dev_ and _prod_ environments. Some code in both files is duplicated and we have to manage multiple files.

With multi-stage builds, we can specify both environments in same file. The common environment needed by both _dev_ and _prod_ would be in its own stage.
```Dockerfile
# Common/base stage
FROM node:14-alpine as base
WORKDIR /src
COPY package.json package-lock.json /src/
EXPOSE 3000

# Prod environment
FROM base as production
ENV NODE_ENV=production
RUN npm ci
COPY . /src
CMD ["node", "bin/www"]

# Dev environment
FROM base as dev
ENV NODE_ENV=development
RUN npm install -g nodemon && npm install
COPY . /src
CMD ["nodemon", "bin/www"]
```
We use `--target` to specify which stage is the final stage, to create separate images for _dev_ and _prod_ environments.
```bash
# Production image
docker build --target=production -t myapp:prod 

# Dev image
docker build --target=dev -t myapp:dev
```

## Testing in separate stage
Same as [[#Separately run different environments]], in single stage builds we would need two different Dockerfiles, one to test the application and other to deploy, if we want to keep production and testing environments separate.

With multi-stage builds, we can do this in the same file. Testing is done in its own stage (with its own dependencies that don't carry over the next stage) each time we have to deploy the application.

```Dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# Testing stage
FROM builder AS tester
RUN npm test

# Production runtime
# Final image only contains files specified in this stage
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
CMD ["node", "dist/server.js"]
```