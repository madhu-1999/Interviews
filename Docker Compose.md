#docker #devops 
# Need
+ Simplifies managing multiple containers in a single file. (.yaml/.yml)
	+ Real applications include:
		+ Frontend
		+ Backend
		+ Database
		+ Load Balancer
		+ Cache
		typically, each one in its own container, along with managing networking, [[Persisting Data#Volumes (Preferred)|volumes]] and port mappings.
+ Ensures consistency across environments ([[AWS CloudFormation#Infrastructure As Code (IaC)|IaC]]). Can use env variables for things that change.
+ Valuable tool for local development, testing, and simpler deployments.
# Supported filenames
Default path : working directory
1. `compose.yaml / compose.yml` (Preferred)
2. `docker-compose.yaml / compose.yml`
3. For custom file name:
```bash
# If Docker desktop is installed
docker compose -f <custom name>.yml up
# docker-compose installed
docker-compose -f <custom name>.yml up
```
# Structure of a Compose file
1. `services` : Defines all the containers and their configurations (ports, volumes, networks, env variables and dependencies on other services) in an application.
2. `networks`: Defines network for communication between services. If not specified, a default network is created.
3. `volumes`: Define named volumes for persistent data storage shared among services.
4. `configs`: Define non-sensitive config data like app settings, dependencies or db schemas. Mounted into container as a file.
5. `secrets`: Sensitive data like passwords, API keys, private keys and TLS certificates

~~~tabs
tab: no comments
```yaml
services:
	frontend:
		image: example/webapp
		ports:
			- "8080:80"
		networks:
			- front-tier
			- back-tier
		configs:
			- httpd-config
		secrets:
			- server-certificate

	backend:
		build: .
		image: backend:v1
		ports:
			- "5000:5000"
		environment:
			- DATABASE_URL=${DB_URL}
		networks:
			- back-tier
		depends_on:
			- db

	db:
		image: postgres:13
		env_file:
			- .env
			- ./config/web.env
		volumes:
			- db_data:/var/lib/postgresql/data
		networks:
			- back-tier

networks:
	front-tier:
	back-tier:

volumes:
	db_data:
		
configs:
	httpd-config:
		external: true
	
secrets:
	server-certificate:
		external: true	
```

tab: comments

```yaml
services:
	# Name of service: frontend
	frontend:
		# example/webapp : path to image used to 
		# create a container
		image: example/webapp
		# mapping host port 8080 to container port 80
		ports:
			- "8080:80"
		# `frontend` service can communicate with other 
		# services present on specified networks
		networks:
			- front-tier
			- back-tier
		# Specifies config data needed by `frontend`
		configs:
			- httpd-config
		# Specifies secrets object to be used by `frontend`
		secrets:
			- server-certificate

	backend:
		# Specify path/url to Dockerfile to be used to 
		# create the image, which will be used to create a 
		# container.
		# Here path (.) => Dockerfile in current dir
		build: .
		# Name to give image that will be created
		image: backend:v1
		ports:
			- "5000:5000"
		# Specify env variables needed to run service
		# ${DB_URL} is a env variable on host machine that is
		# set in .env file of current dir / host's env
		# (.bash_profile)
		environment:
			- DATABASE_URL=${DB_URL}
		networks:
			- back-tier
		# Specifies services that `backend` is dependent on
		# thus, `backend` service is created after `db`
		# service.
		depends_on:
			- db

	db:
		image: postgres:13
		# Specifies env file paths to read env variables from 
		env_file:
			- .env
			- ./config/web.env
		# Specifies named volume to attach (`db_data`) and 
		# its path in the host machine
		volumes:
			- db_data:/var/lib/postgresql/data
		networks:
			- back-tier
# Just specifying names of networks is enough
# Docker creates a separate network for each custom named network
networks:
	front-tier:
	back-tier:
# Specify just the names of the volumes
# If not specified then volume mounted on a service is treated 
# as a bind mount
volumes:
	db_data:
# Specify all configs required by all services
# `external:true` implies the config object/file already exists 
# If not specified, docker creates and uses the config object	
configs:
	httpd-config:
		external: true
# Specify all the secrets required by all services	
secrets:
	server-certificate:
		external: true	
```
~~~
# Key commands
1. To build, create and start all services. Use `-d` for detached mode.
```bash
# If Docker desktop is installed
docker compose up
# If using only docker cli, need to download docker-compose separately
docker-compose up
```
2. Restart services
```bash
# If Docker desktop is installed
docker compose start
# docker-compose installed
docker-compose start
```
3. Stop and delete all services
```bash
# If Docker desktop is installed
docker compose down
# docker-compose installed
docker-compose down
```
4. Stop all services
```bash
# If Docker desktop is installed
docker compose stop
# docker-compose installed
docker-compose stop
```
5. List all containers in the application
```bash
# If Docker desktop is installed
docker compose logs
# docker-compose installed
docker-compose logs
```
6. Execute command inside a specific container
```bash
# If Docker desktop is installed
docker compose exec <service> <command>
# docker-compose installed
docker-compose exec <service> <command>
```

