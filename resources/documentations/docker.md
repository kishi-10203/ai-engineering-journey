# Docker 

## Table of contents

1.  [What is Docker](#1-what-is-docker)
	- [1.1 Why Docker](#11-why-docker)
	- [1.2 Container vs Virtual Machine](#12-container-vs-virtual-machine)
	- [1.3 Docker Architecture](#13-docker-architecture)
	- [1.4 Docker Objects](#14-docker-objects)
	- [1.5 Docker Terminologies](#15-docker-terminologies)
	- [1.6 Docker Commands](#16-docker-commands)
		- [1.6.1 Basic Docker Commands](#161-basic-docker-commands)
		- [1.6.2 Docker Image Commands](#162-docker-image-commands)
		- [1.6.3 Docker Container Commands](#163-docker-container-commands)
		- [1.6.4 Docker Registry Commands](#164-docker-registry-commands)
		- [1.6.5 Docker Network Commands](#165-docker-network-commands)
		- [1.6.6 Docker Volume Commands](#166-docker-volume-commands)
		- [1.6.7 Docker System Commands](#167-docker-system-commands)
		- [1.6.8 Docker Build Commands](#168-docker-build-commands)
		- [1.6.9 Docker Compose Commands](#169-docker-compose-commands)
		- [1.6.10 Docker Context Commands](#1610-docker-context-commands)
		- [1.6.11 Docker Swarm Commands](#1611-docker-swarm-commands)
		- [1.6.12 Other Useful Docker Commands](#1612-other-useful-docker-commands)
		- [1.6.13  Cleanup Commands](#1613-cleanup-commands)
		- [Important Command Differences](#important-command-differences)

2.  [Docker Image](#2-docker-image)
	- [2.1 Image Layers and Union Filesystem](#21-image-layers-and-union-filesystem)
	- [2.2 Dockerfile](#22-dockerfile)
		- [2.2.1 Dockerfile Instructions](#221-dockerfile-instructions)
		- [2.2.2 Example Dockerfile](#222-example-dockerfile)
	- [2.3 Image Lifecycle](#23-image-lifecycle)
	- [2.4 Image Building Approaches](#24-image-building-approaches)
		- [2.4.1 Traditional Builds](#241-traditional-builds)
		- [2.4.2 Buildkit Builds](#242-buildkit-builds)
		- [2.4.3 Multi Stage Builds](#243-multistage-builds)
		- [2.4.4 Multi Platform Builds](#244-multi-platform-builds)
		- [2.4.5 Buildx Builds](#245-buildx-builds)
	- [2.5 BuildKit Capabilities](#25-buildkit-capabilities)
		- [2.5.1 Build Caching](#251-build-caching)
		- [2.5.2 Parallel Build Execution](#252-parallel-build-execution)
		- [2.5.3 Build Mounts](#253-build-mounts)
		- [2.5.4 Build Arguments and Variables](#254-build-arguments-and-variables)
		- [2.5.5 Build Secret Management](#255-build-secret-management)
		- [2.5.6 Build Attestations](#256-build-attestations)
		- [2.5.7 Efficient Build Context Handling](#257-efficient-build-context-handling)
		- [2.5.8 External Build Outputs](#258-external-build-outputs)
		- [2.5.9 SSH Forwarding](#259-ssh-forwarding)
		- [2.5.10 Remote & Distributed Builds](#2510-remote-distributed-builds)
	- [2.6 Image Security Hardening Practices](#26-image-security-hardening-practices)
	- [2.7 Versioning Docker Images](#27-versioning-docker-images)
		- [2.7.1 Semantic Versioning](#271-semantic-versioning)
		- [2.7.2 Git Commit SHA Versioning](#272-git-commit-sha-versioning)
		- [2.7.3 Build Number Versioning](#273-build-number-versioning)
		- [2.7.4 Image Digest Versioning](#274-image-digest-versioning)
	- [2.8 Enterprise Image Tagging Strategies](#28-enterprise-image-tagging-strategies)
	- [2.9 Image Promotion Across Environments](#29-image-promotion-across-environments)
	- [2.10 Secure Image versioning & Management practices](#210-secure-image-versioning-management-practices)

3.  [Docker Container](#3-docker-container)
	- [3.1 Image vs Container](#31-image-vs-container)
	- [3.2 Container Lifecycle and States](#32-container-lifecycle-and-states)
	- [3.3 Docker Compose](#33-docker-compose)
	- [3.4 Docker Compose commands](#34-docker-compose-commands)
	- [3.5 Container Health Checks](#35-container-health-checks)
	- [3.6 Docker Container Security](#36-docker-container-security)
		- [3.6.1 Linux Capabilities](#361-linux-capabilities)
		- [3.6.2 Root vs Non Root Containers](#362-root-vs-non-root-containers)
		- [3.6.3 Read only Root Filesystem](#363-read-only-root-filesystem)
		- [3.6.4 Seccomp](#364-seccomp)
		- [3.6.5 AppArmor](#365-apparmor)
		- [3.6.6 Resource Restrictions](#366-resource-restrictions)
		- [3.6.7 Container Vulnerability Scanning](#367-container-vulnerability-scanning)
	- [3.7 Container Monitoring & Logging](#37-container-monitoring-logging)
		- [3.7.1 Container Logs](#371-container-logs)
		- [3.7.2 Logging Drivers](#372-logging-drivers)
		- [3.7.3 Container Metrics](#373-container-metrics)
		- [3.7.4 Stats](#374-stats)
		- [3.7.5 Resource Monitoring](#375-resource-monitoring)
		- [3.7.6 Centralized Logging](#376-centralized-logging)
		- [3.7.7 Monitoring With Prometheus and Grafana](#377-monitoring-with-prometheus-and-grafana)
	- [3.8 Incident Management and Reporting](#38-incident-management-and-reporting)
	- [3.9 Automated Incident Remediation](#39-automated-incident-remediation)

4.  [Docker Networking](#4-docker-networking)
	- [4.1 Types of Docker Network](#41-types-of-docker-network)
	- [4.2 Docker Networking Commands](#42-docker-networking-commands)
	- [4.3 Port Mapping](#43-port-mapping)
	- [4.4 DNS Resolution between Containers](#44-dns-resolution-between-containers)
	- [4.5 Enterprise Grade Networking Concepts](#45-enterprise-grade-networking-concepts)
		- [4.5.1 Container IP Address](#451-container-ip-address)
		- [4.5.2 Subnet](#452-subnet)
		- [4.5.3 Gateway](#453-gateway)
		- [4.5.4 NAT](#454-nat)
		- [4.5.5 DNS](#455-dns)
		- [4.5.6 Firewall](#456-firewall)
		- [4.5.7 Reverse Proxy](#457-reverse-proxy)
		- [4.5.8 Load Balancer](#458-load-balancer)
		- [4.5.9 VLAN](#459-vlan)

5.  [Docker Storage](#5-docker-storage)
	- [5.1 Container Writable Layer](#51-container-writable-layer)
	- [5.2 Docker Volumes](#52-docker-volumes)
	- [5.3 Bind Mounts](#53-bind-mounts)
	- [5.4 tmpfs Mounts](#54-tmpfs-mounts)
	- [5.5 Volume Drivers](#55-volume-drivers)
	- [5.6 Volume Lifecycle](#56-volume-lifecycle)
	- [5.7 Storage Backup and Recovery](#57-storage-backup-and-recovery)
	- [5.8 Storage Performance Considerations](#58-storage-performance-considerations)

6.  [Docker Troubleshooting](#6-docker-troubleshooting)

7.  [Practice](#7-practice)

 
    
---

## 1. What is Docker?

Docker is an `open-source` containerization platform used to `build`, `package`, `distribute`, and `run applications` inside isolated environments called containers.

Docker packages an application along with all its `dependencies`, `libraries`, `configuration files`, and `runtime requirements` into a *single portable unit*.

This portable unit is called a **`Docker Image`**.

A running instance of a Docker image is called a **`Docker Container`**.

---

### 1.1 Why Docker

Before containerization, developers faced *`environment-related issues`.*
> "It works on my machine, but it doesn't work on the server."

This happened because different environments had different:
- Operating system versions
- Programming language versions
- Installed dependencies
- Library versions
- Environment variables
- System configurations

**Docker solves this problem by providing a standardized application environment.**

| Feature | Description |
|---|---|
| `Portability` | Containers run consistently across different environments. |
| `Isolation` | Applications run in isolated containers. |
| `Lightweight` | Containers share the host operating system kernel. |
| `Scalability` | Multiple container instances can be created easily. |
| `Faster Deployment` | Applications can be packaged and deployed consistently. |
| `Resource Efficiency` | Containers generally consume fewer resources than full virtual machines. |
| `Environment Consistency` | Development, testing, and production environments can use the same image. |
| `Version Control` | Docker images can be tagged and versioned. |

---

### 1.2 Container vs Virtual Machine

#### 1.2.1 Virtual Machine
Before Containerization DevOps teams used a single highly capable Server and deployed multiple applications on it.

They split up the single server into multiple virtual machines with the help of a **`Hypervisor`.**

Each virtual machine generally contained:

- Guest Operating System
- System Libraries
- Application Dependencies
- Application

> #### Virtual Machine Architecture
> Hardware → Host OS → Hypervisor → Guest OS → Application


#### 1.2.2 Container 

A Container is a running instance of a `docker-image`.

it is comparatively lighter than VM's and shares the `Host OS kernel`.

Containers package:

- Application code
- Runtime
- Libraries
- Dependencies
- Configuration

> #### Container Architecture
> Hardware → Host OS → Container Runtime → Containers → Applications

---

#### 1.2.3 Container vs VM's
| Feature | Virtual Machine | Container |
| --- | --- | --- |
| `Virtualization` | Hardware-level | OS-level |
| `Operating System` | Separate guest OS | Shares host kernel |
| `Size` | Generally large | Generally smaller |
| `Startup` | Usually slower | Usually faster |
| `Resource Usage` | Higher | Lower |
| `Isolation` | Strong OS boundary | Process-level isolation |
| `Portability` | More heavyweight | Highly portable |
| Example | VMware VM | Docker Container |

> **Important:** Containers are lightweight, but they are not equivalent to virtual machines in terms of security isolation.

---

### 1.3 Docker Architecture
Docker follows a **`Client-Server Architecture`** consisting of:

| Component | Function |
|---|---|
| `Docker Client` | Accepts user commands (`docker run`, `docker pull`). |
| `Docker Daemon` (dockerd)| Processes commands and manages Docker objects. |
| `Docker Host` | Machine where Docker Daemon and containers run. |
| `Docker Registry` | Stores Docker images (e.g., Docker Hub, ECR). |
| `Docker Objects` | Images, containers, networks and volumes managed by Docker. |

---

#### 1.3.1 Docker Architecture Workflow

1. User executes a Docker command through Docker CLI.
2. Docker Client sends the request to Docker Daemon through Docker API.
3. Docker Daemon processes the request.
4. If required, Docker Daemon communicates with Docker Registry to pull or push images.
5. Docker Daemon creates and manages Docker Objects (Images, Containers, Networks, Volumes).
6. Containers run the application using the specified image.

> `User → Docker Client → Docker Daemon → Docker Objects → Application Running`

---

### 1.4 Docker Objects

| Docker Object | Definition |
|---|---|
| `Docker Image` | A read-only template used to create containers. |
| `Docker Container` | A running or stopped instance created from a Docker image. |
| `Docker Network` | Allows containers to communicate with one another and external systems. |
| `Docker Volume` | A mechanism used to persist data outside a container's writable layer. |

---

### 1.5 Docker Terminologies

| Terminology | Definition |
|---|---|
| `Docker Image` | A read-only template used to create containers. |
| `Docker Container` | A running or stopped instance created from a Docker image. |
| `Docker Network` | Allows containers to communicate with one another and external systems. |
| `Docker Volume` | A mechanism used to persist data outside a container's writable layer. |
| `Dockerfile` | A text file containing instructions used to build a Docker image. |
| `Docker Compose` | A tool used to define and manage multi-container applications through a YAML configuration file. |
| `Docker Registry` | A storage and distribution system for Docker images. |
| `Docker Repository` | A collection of related images, commonly organized by application or project. |
| `Docker Tag` | Identifies a particular version or variant of an image. |
| `Docker Engine` | Core technology responsible for building and running Docker containers. |
| `Docker CLI` | Command-line interface used to interact with Docker. |
| `Docker API` | Interface through which clients communicate with the Docker daemon. |
| `Docker Daemon` (dockerd) | Background service responsible for managing Docker objects. |
| `Docker Host | Machine where Docker runs and containers are hosted. |
| `Docker Hub` | Cloud-based registry used to store and distribute Docker images. |
| `Image Layer` | Each Docker image consists of multiple layers representing filesystem changes. |
| `Container Layer` | Writable layer added on top of image layers when a container is created. |
| `Docker Context` | Defines the Docker endpoint and configuration used by the CLI to communicate with a Docker daemon. |
| `Docker Build` | Process of building a Docker image using a Dockerfile or other build instructions. |
| `Docker Pull` | Downloads an image from a registry. |
| `Docker Push` | Uploads an image to a registry. |
| `Bind Mount` | Mounts a file or directory from the host machine into a container. |
| `Port Mapping` | Maps a container port to a host port to allow external access. |
| `Environment Variables` | Pass configuration values to applications running inside containers. |
| `Container Runtime` | Software responsible for running containers. |
| `Restart Policy` | Defines whether a container should restart automatically under specified conditions. |
| `Health Check` | Checks whether an application inside a container is functioning correctly. |
| `Volume Driver` | Determines how and where Docker volumes are stored. |

---

### 1.6 Docker Commands

#### 1.6.1 Basic Docker Commands

| Command | Description | Example |
|---|---|---|
| `docker --version` | Displays the Docker CLI version. | `docker --version` |
| `docker version` | Displays Docker client and server version information. | `docker version` |
| `docker info` | Displays information about the Docker installation and daemon. | `docker info` |
| `docker help` | Displays available Docker commands. | `docker help` |
| `docker COMMAND --help` | Displays help for a specific command. | `docker run --help` |

---

#### 1.6.2 Docker Image Commands

| Command | Description | Example |
|---|---|---|
| `docker images` | Lists locally available images. | `docker images` |
| `docker image ls` | Lists Docker images. | `docker image ls` |
| `docker pull` | Downloads an image from a registry. | `docker pull ubuntu:24.04` |
| `docker push` | Uploads an image to a registry. | `docker push username/myapp:1.0` |
| `docker build` | Builds an image using a Dockerfile. | `docker build -t myapp .` |
| `docker image build` | Builds a Docker image. | `docker image build -t myapp .` |
| `docker rmi` | Removes one or more images. | `docker rmi myapp` |
| `docker image rm` | Removes Docker images. | `docker image rm myapp` |
| `docker image inspect` | Displays detailed image information. | `docker image inspect ubuntu` |
| `docker image history` | Displays the history and layers of an image. | `docker image history ubuntu` |
| `docker image tag` | Creates another tag for an image. | `docker tag myapp username/myapp:1.0` |
| `docker image save` | Saves images to a tar archive. | `docker image save -o myapp.tar myapp` |
| `docker image load` | Loads images from a tar archive. | `docker image load -i myapp.tar` |
| `docker image prune` | Removes unused images. | `docker image prune` |
| `docker image prune -a` | Removes all images not used by existing containers. | `docker image prune -a` |

---

#### 1.6.3 Docker Container Commands

| Command | Description | Example |
|---|---|---|
| `docker run` | Creates and starts a new container. | `docker run ubuntu` |
| `docker create` | Creates a container without starting it. | `docker create --name mycontainer ubuntu` |
| `docker start` | Starts a stopped container. | `docker start mycontainer` |
| `docker stop` | Gracefully stops a running container. | `docker stop mycontainer` |
| `docker restart` | Restarts a container. | `docker restart mycontainer` |
| `docker kill` | Immediately sends a signal to stop a container. | `docker kill mycontainer` |
| `docker pause` | Pauses all processes inside a container. | `docker pause mycontainer` |
| `docker unpause` | Resumes paused container processes. | `docker unpause mycontainer` |
| `docker ps` | Lists running containers. | `docker ps` |
| `docker ps -a` | Lists all containers, including stopped containers. | `docker ps -a` |
| `docker rm` | Removes one or more containers. | `docker rm mycontainer` |
| `docker rm -f` | Forcefully removes a running or stopped container. | `docker rm -f mycontainer` |
| `docker exec` | Executes a command inside a running container. | `docker exec -it mycontainer bash` |
| `docker attach` | Attaches to a running container's input/output streams. | `docker attach mycontainer` |
| `docker logs` | Displays container logs. | `docker logs mycontainer` |
| `docker logs -f` | Follows container logs continuously. | `docker logs -f mycontainer` |
| `docker inspect` | Displays detailed container information. | `docker inspect mycontainer` |
| `docker stats` | Displays live resource usage statistics. | `docker stats` |
| `docker top` | Displays processes running inside a container. | `docker top mycontainer` |
| `docker cp` | Copies files between the host and a container. | `docker cp file.txt mycontainer:/app/` |
| `docker diff` | Shows filesystem changes made inside a container. | `docker diff mycontainer` |
| `docker rename` | Renames a container. | `docker rename oldname newname` |
| `docker port` | Displays container port mappings. | `docker port mycontainer` |
| `docker wait` | Waits for a container to stop and returns its exit code. | `docker wait mycontainer` |
| `docker update` | Updates resource limits or configuration of a container. | `docker update --memory 512m mycontainer` |
| `docker commit` | Creates a new image from a container's changes. | `docker commit mycontainer myimage:v1` |
| `docker export` | Exports a container filesystem as a tar archive. | `docker export mycontainer -o container.tar` |
| `docker container prune` | Removes all stopped containers. | `docker container prune` |

---

#### 1.6.4 Docker Registry Commands

| Command | Description | Example |
|---|---|---|
| `docker login` | Authenticates with a Docker registry. | `docker login` |
| `docker logout` | Logs out from a Docker registry. | `docker logout` |
| `docker search` | Searches Docker Hub for images. | `docker search nginx` |
| `docker pull` | Downloads an image from a registry. | `docker pull nginx` |
| `docker push` | Uploads an image to a registry. | `docker push username/myapp:latest` |
| `docker manifest inspect` | Displays an image manifest. | `docker manifest inspect nginx:latest` |

---

#### 1.6.5 Docker Network Commands

| Command | Description | Example |
|---|---|---|
| `docker network ls` | Lists Docker networks. | `docker network ls` |
| `docker network create` | Creates a new Docker network. | `docker network create mynetwork` |
| `docker network inspect` | Displays detailed network information. | `docker network inspect mynetwork` |
| `docker network connect` | Connects a container to a network. | `docker network connect mynetwork mycontainer` |
| `docker network disconnect` | Disconnects a container from a network. | `docker network disconnect mynetwork mycontainer` |
| `docker network rm` | Removes a Docker network. | `docker network rm mynetwork` |
| `docker network prune` | Removes unused Docker networks. | `docker network prune` |

---

#### 1.6.6 Docker Volume Commands

| Command | Description | Example |
|---|---|---|
| `docker volume ls` | Lists Docker volumes. | `docker volume ls` |
| `docker volume create` | Creates a new Docker volume. | `docker volume create myvolume` |
| `docker volume inspect` | Displays detailed volume information. | `docker volume inspect myvolume` |
| `docker volume rm` | Removes a Docker volume. | `docker volume rm myvolume` |
| `docker volume prune` | Removes unused Docker volumes. | `docker volume prune` |

---

#### 1.6.7 Docker System Commands

| Command | Description | Example |
|---|---|---|
| `docker system df` | Displays Docker disk usage. | `docker system df` |
| `docker system info` | Displays Docker system information. | `docker system info` |
| `docker system events` | Displays real-time Docker events. | `docker system events` |
| `docker system prune` | Removes unused Docker resources. | `docker system prune` |
| `docker system prune -a` | Removes unused images, stopped containers, unused networks and build cache. | `docker system prune -a` |
| `docker system prune --volumes` | Also removes eligible unused anonymous volumes. | `docker system prune --volumes` |

---

#### 1.6.8 Docker Build Commands

| Command | Description | Example |
|---|---|---|
| `docker build` | Builds an image from a Dockerfile. | `docker build -t myapp .` |
| `docker build --no-cache` | Builds an image without using the build cache. | `docker build --no-cache -t myapp .` |
| `docker build --build-arg` | Passes build-time arguments. | `docker build --build-arg VERSION=1.0 -t myapp .` |
| `docker build -f` | Specifies a custom Dockerfile. | `docker build -f Dockerfile.dev -t myapp .` |
| `docker buildx build` | Builds images using Docker Buildx. | `docker buildx build -t myapp .` |
| `docker builder prune` | Removes unused build cache. | `docker builder prune` |
| `docker buildx ls` | Lists available Buildx builders. | `docker buildx ls` |
| `docker buildx inspect` | Displays information about a builder. | `docker buildx inspect` |

---

#### 1.6.9 Docker Compose Commands

Docker Compose is used to define and manage multi-container applications using a Compose YAML file.

| Command | Description | Example |
|---|---|---|
| `docker compose up` | Creates and starts services. | `docker compose up` |
| `docker compose up -d` | Starts services in the background. | `docker compose up -d` |
| `docker compose down` | Stops and removes Compose containers and networks. | `docker compose down` |
| `docker compose build` | Builds or rebuilds service images. | `docker compose build` |
| `docker compose pull` | Downloads service images. | `docker compose pull` |
| `docker compose push` | Pushes service images to a registry. | `docker compose push` |
| `docker compose ps` | Lists Compose containers. | `docker compose ps` |
| `docker compose logs` | Displays service logs. | `docker compose logs` |
| `docker compose logs -f` | Follows service logs. | `docker compose logs -f` |
| `docker compose exec` | Executes a command inside a running service container. | `docker compose exec web bash` |
| `docker compose run` | Runs a one-off command in a service container. | `docker compose run web sh` |
| `docker compose stop` | Stops services without removing containers. | `docker compose stop` |
| `docker compose start` | Starts existing stopped services. | `docker compose start` |
| `docker compose restart` | Restarts services. | `docker compose restart` |
| `docker compose rm` | Removes stopped service containers. | `docker compose rm` |
| `docker compose config` | Validates and displays the resolved Compose configuration. | `docker compose config` |
| `docker compose top` | Displays processes running in service containers. | `docker compose top` |
| `docker compose stats` | Displays resource usage of service containers. | `docker compose stats` |
| `docker compose pause` | Pauses services. | `docker compose pause` |
| `docker compose unpause` | Resumes paused services. | `docker compose unpause` |
| `docker compose version` | Displays the Compose version. | `docker compose version` |

---

#### 1.6.10 Docker Context Commands

Docker contexts allow you to manage connections to different Docker daemons.

| Command | Description | Example |
|---|---|---|
| `docker context ls` | Lists available Docker contexts. | `docker context ls` |
| `docker context show` | Displays the current context. | `docker context show` |
| `docker context create` | Creates a new Docker context. | `docker context create mycontext` |
| `docker context use` | Switches to another Docker context. | `docker context use mycontext` |
| `docker context inspect` | Displays context information. | `docker context inspect mycontext` |
| `docker context rm` | Removes a Docker context. | `docker context rm mycontext` |

---

#### 1.6.11 Docker Swarm Commands

Docker Swarm is Docker's native container orchestration functionality.

| Command | Description | Example |
|---|---|---|
| `docker swarm init` | Initializes a Swarm cluster. | `docker swarm init` |
| `docker swarm join` | Joins a node to a Swarm cluster. | `docker swarm join --token <token> <manager-ip>:2377` |
| `docker swarm leave` | Leaves a Swarm cluster. | `docker swarm leave` |
| `docker swarm join-token` | Displays a join token for workers or managers. | `docker swarm join-token worker` |
| `docker node ls` | Lists Swarm nodes. | `docker node ls` |
| `docker node inspect` | Displays node information. | `docker node inspect self` |
| `docker node ps` | Lists tasks running on a node. | `docker node ps self` |
| `docker service create` | Creates a Swarm service. | `docker service create --name web nginx` |
| `docker service ls` | Lists Swarm services. | `docker service ls` |
| `docker service ps` | Lists tasks belonging to a service. | `docker service ps web` |
| `docker service inspect` | Displays service information. | `docker service inspect web` |
| `docker service scale` | Scales service replicas. | `docker service scale web=3` |
| `docker service update` | Updates a service configuration. | `docker service update --replicas 3 web` |
| `docker service rm` | Removes a Swarm service. | `docker service rm web` |
| `docker stack deploy` | Deploys a stack using a Compose file. | `docker stack deploy -c compose.yaml mystack` |
| `docker stack ls` | Lists deployed stacks. | `docker stack ls` |
| `docker stack services` | Lists services belonging to a stack. | `docker stack services mystack` |
| `docker stack ps` | Lists tasks belonging to a stack. | `docker stack ps mystack` |
| `docker stack rm` | Removes a deployed stack. | `docker stack rm mystack` |

---

#### 1.6.12 Other Useful Docker Commands

| Command | Description | Example |
|---|---|---|
| `docker init` | Creates Docker starter files for a project. | `docker init` |
| `docker debug` | Opens a debugging shell for a container or image. | `docker debug mycontainer` |
| `docker scout` | Analyzes images for vulnerabilities and other security information. | `docker scout quickview myapp` |
| `docker plugin ls` | Lists installed Docker plugins. | `docker plugin ls` |
| `docker plugin install` | Installs a Docker plugin. | `docker plugin install <plugin-name>` |
| `docker trust inspect` | Inspects image trust information. | `docker trust inspect nginx` |
| `docker version` | Displays Docker version details. | `docker version` |
| `docker checkpoint ls` | Lists checkpoints for a container. | `docker checkpoint ls mycontainer` |

---

#### 1.6.13 Cleanup Commands

| Command | Description |
|---|---|
| `docker container prune` | Removes stopped containers. |
| `docker image prune` | Removes dangling images. |
| `docker image prune -a` | Removes images not used by existing containers. |
| `docker volume prune` | Removes unused volumes. |
| `docker network prune` | Removes unused networks. |
| `docker builder prune` | Removes unused build cache. |
| `docker system prune` | Removes unused containers, networks, dangling images and build cache. |
| `docker system prune -a --volumes` | Performs more extensive cleanup, including eligible unused volumes. |

> **Warning:** Cleanup commands can permanently delete data. Review what will be removed before confirming, especially when using `-a` or `--volumes`.

---

#### Important Command Differences

| Command | Difference |
|---|---|
| `docker run` vs `docker start` | `run` creates and starts a new container; `start` starts an existing stopped container. |
| `docker stop` vs `docker kill` | `stop` attempts graceful shutdown; `kill` sends a signal immediately (SIGKILL by default). |
| `docker rm` vs `docker rmi` | `rm` removes containers; `rmi` removes images. |
| `docker exec` vs `docker attach` | `exec` starts a new process inside a container; `attach` connects to its existing main process streams. |
| `docker pause` vs `docker stop` | `pause` suspends container processes; `stop` terminates the container process. |
| `docker build` vs `docker pull` | `build` creates an image from a build context; `pull` downloads an existing image from a registry. |
| `docker commit` vs `docker build` | `commit` creates an image from container changes; `build` creates an image using Dockerfile instructions. |
| `docker images` vs `docker ps` | `images` lists images; `ps` lists containers. |
| `docker volume` vs `docker bind mount` | Volumes are managed by Docker; bind mounts map a host filesystem path into a container. |

---

#### Important Notes

>1. Docker has both legacy shorthand commands and management commands.
>
>   For example:
>   - `docker ps` = `docker container ls`
>   - `docker images` = `docker image ls`
>   - `docker rmi` = `docker image rm`
>   - `docker rm` = `docker container rm`
>
>2. Docker Compose uses `docker compose`, with a space. The older standalone `docker-compose` command is a legacy alternative.
>
>3. Docker commands interact with the Docker daemon, which manages containers, images, networks and volumes.
>
>4. Use `docker COMMAND --help` whenever you want to see all available options for a particular command.
>

#### Official Documentation

- Docker CLI Reference: https://docs.docker.com/reference/cli/docker/
- Docker Compose Reference: https://docs.docker.com/reference/cli/docker/compose/
- Dockerfile Reference: https://docs.docker.com/reference/dockerfile/


---
---

## 2. Docker Image

A Docker image is an **`immutable artifact`** that contains everything required to run an application.

Images are generally built using a **`Dockerfile`**.

Images are **`layered filesystem template`**, they are built by layers defined in the `Dockerfile`.

---

### 2.1 Image Layers and Union Filesystem

Docker images are built using **`multiple read-only layers`** stacked on top of each other. Docker uses a technology called a *Union Filesystem (UnionFS)* to combine these layers and make them appear as one unified filesystem.

Conceptually, the image consists of:

```text
+----------------------------------+
| Layer 5: CMD                     |
| Container startup configuration  |
+----------------------------------+
| Layer 4: COPY                    |
| index.html                       |
+----------------------------------+
| Layer 3: RUN                     |
| Nginx installed                  |
+----------------------------------+
| Layer 2: RUN                     |
| Package indexes updated          |
+----------------------------------+
| Layer 1: FROM                    |
| Ubuntu base image                |
+----------------------------------+
```

**Important:** `Image layers` are read-only once created. Docker doesn't modify existing image layers when building a new image. Instead, it adds new layers.

Docker also has `container layers` that are writable.

> **Note:** Not every Dockerfile instruction creates a filesystem layer. For example, CMD and ENV generally contribute image configuration rather than filesystem layers.

---

#### 2.1.1 What Happens When a Container Starts 

When Docker creates a container from an image, it adds a special layer called the **`Container Writable Layer`** on top of the read-only **`image layers`**.

```text
+----------------------------------+
| Container Writable Layer         |
|                                  |
| Changes made while container     |
| is running                       |
+----------------------------------+
| Image Layer 3                    |
| Application files                |
+----------------------------------+
| Image Layer 2                    |
| Installed packages               |
+----------------------------------+
| Image Layer 1                    |
| Base operating system            |
+----------------------------------+
```

---

#### 2.1.2 Image Layers vs Container Writable Layer

| Feature | Image Layers | Container Writable Layer |
|---|---|---|
| `Access` | Read-only | Read/write |
| `Created during` | Image build | Container creation |
| `Shared between containers` | Yes | No, each container has its own |
| `Changes` | Cannot be modified in place | Can be modified |
| `Lifetime` | Until image/layer is removed | Until container is removed |
| `Persistence` | Image persists | Removed with container |

---

#### 2.1.3 Why Does Docker Use Image Layers?

There are several important advantages.

1. Image Reuse
2. Faster Image Downloads
3. Build Caching
4. Storage Efficiency

| Advantage | Explanation | Example |
|---|---|---|
| `Image Reuse` | Multiple Docker images can share common base and intermediate layers instead of creating duplicate copies. | Two applications using `ubuntu:22.04` and Nginx can reuse the same existing layers. |
| `Faster Image Downloads` | Docker downloads only the layers that are missing locally instead of downloading the entire image again. | If an image has 5 layers and your machine already has 4, Docker downloads only the remaining 1 layer. |
| `Build Caching` | Docker reuses previously built layers if the Dockerfile instructions and their dependencies haven't changed. | If the Ubuntu and Nginx installation layers are unchanged, Docker can reuse them during the next build. |
| `Storage Efficiency` | Multiple images can share identical layers, reducing duplicate storage on the host machine. | Two application images sharing the same Ubuntu base layer don't need separate copies of that layer. |


#### Summary

| Concept | Benefit |
|---|---|
| `Image Reuse` | Avoids duplicating common layers. |
| `Faster Downloads` | Downloads only missing layers. |
| `Build Caching` | Avoids rebuilding unchanged layers. |
| `Storage Efficiency` | Saves disk space by sharing layers. |

---

### 2.2 Dockerfile

A `Dockerfile` is a text file that contains a set of instructions used to build a Docker image.

It tells Docker:
- Which `base image` to use.
- Which `dependencies` to install.
- Which `application files` to copy.
- Which commands to execute during `image creation`.
- Which command to run when a `container starts`.

---

#### 2.2.1 Dockerfile Instructions 

| # | Instruction | Description | Example |
|---|---|---|---|
| 1 | `FROM` | Defines the base image. Every build stage starts with a FROM instruction. | `FROM ubuntu:24.04` |
| 2 | `WORKDIR` | Sets the working directory inside the image. | `WORKDIR /app` |
| 3 | `COPY` | Copies files and directories from the build context into the image. | `COPY . .` |
| 4 | `ADD` | Copies files and supports additional functionality, such as extracting local tar archives. Prefer COPY for ordinary file copying. | `ADD application.tar.gz /app/` |
| 5 | `RUN` | Executes commands during image building. | `RUN apt-get update` |
| 6 | `CMD` | Defines the default command or arguments executed when a container starts. Only the last CMD instruction takes effect. | `CMD ["python", "app.py"]` |
| 7 | `ENTRYPOINT` | Defines the main executable for the container. Can be combined with CMD to provide default arguments. | `ENTRYPOINT ["python"]` |
| 8 | `EXPOSE` | Documents the port on which the application listens. It does not automatically publish the port to the host. | `EXPOSE 8080` |
| 9 | `ENV` | Defines environment variables available in subsequent build instructions and containers. | `ENV APP_ENV=production` |
| 10 | `ARG` | Defines build-time variables that can be passed during image building. | `ARG APP_VERSION=1.0` |
| 11 | `USER` | Specifies the user for subsequent build instructions and the container's default runtime process. | `USER appuser` |
| 12 | `LABEL` | Adds metadata to a Docker image, such as version, author, or description. | `LABEL version="1.0"` |
| 13 | `VOLUME` | Creates a mount point for external or persistent data. | `VOLUME ["/data"]` |
| 14 | `HEALTHCHECK` | Defines a command to check whether the container is functioning correctly. | `HEALTHCHECK CMD curl -f http://localhost/ || exit 1` |
| 15 | `ONBUILD` | Defines an instruction that executes later when another image uses the current image as its base. | `ONBUILD COPY . /app` |
| 16 | `STOPSIGNAL` | Specifies the signal sent to the container when Docker stops it. | `STOPSIGNAL SIGTERM` |
| 17 | `SHELL` | Changes the default shell used by shell-form instructions such as RUN, CMD, and ENTRYPOINT. | `SHELL ["/bin/bash", "-c"]` |
| 18 | `MAINTAINER` | Specifies the image author or maintainer. Deprecated; use LABEL instead. | `MAINTAINER developer@example.com` |
| 19 | `#` (Comment) | Adds comments to a Dockerfile. Comments are ignored during normal instruction processing. | `# Install application dependencies` |


#### Notes 

> 1. `FROM`: Every build stage must begin with FROM. Global ARG declarations and comments can appear before the first FROM.
> 2. `CMD`: If multiple CMD instructions exist, only the last effective CMD is used.
> 3. `EXPOSE`: Does not automatically publish a container port. Use `-p` with `docker run` to publish it.
> 4. `RUN` vs `CMD`
>   - RUN executes commands while building the image.
>   - CMD defines the default command when starting a container.
> 5. `ENTRYPOINT` vs `CMD`:
>   - ENTRYPOINT defines the main executable.
>   - CMD provides default arguments or a default command.
>
> `MAINTAINER:` is Deprecated. Use LABEL to specify author or maintainer information.
>
> Example:
>   `LABEL maintainer="developer@example.com"`
>

---

#### 2.2.2 Example Dockerfile

file path : `resource\production-grade-examples\dockerfile`


``` dockerfile
# syntax=docker/dockerfile:1

# ============================================================
# STAGE 1: BUILD STAGE
# ============================================================

FROM python:3.12-slim AS builder

# Prevent Python from generating .pyc files
ENV PYTHONDONTWRITEBYTECODE=1

# Ensure Python logs are immediately sent to stdout/stderr
ENV PYTHONUNBUFFERED=1

# Configure UV
ENV UV_COMPILE_BYTECODE=1 \
    UV_LINK_MODE=copy \
    UV_PROJECT_ENVIRONMENT=/opt/venv

# Set working directory
WORKDIR /build

# Copy UV executable from official UV image
COPY --from=ghcr.io/astral-sh/uv:0.8.15 /uv /uvx /bin/

# Copy dependency definition and lockfile
COPY pyproject.toml uv.lock ./

# Install production dependencies into virtual environment
RUN uv sync \
    --locked \
    --no-dev \
    --no-install-project


# ============================================================
# STAGE 2: PRODUCTION RUNTIME STAGE
# ============================================================

FROM python:3.12-slim AS runtime

# Python runtime configuration
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    APP_ENV=production \
    PORT=8000 \
    PATH="/opt/venv/bin:$PATH"

# Set application directory
WORKDIR /app

# Create a dedicated non-root user
RUN groupadd --system app && \
    useradd --system \
    --gid app \
    --home-dir /app \
    --no-create-home \
    app

# Copy prepared virtual environment from builder stage
COPY --from=builder /opt/venv /opt/venv

# Copy application source code
COPY --chown=app:app app/ ./app/

# Ensure application directory ownership
RUN chown -R app:app /app

# Switch to non-root user
USER app

# Document the application port
EXPOSE 8000

# Container health check
HEALTHCHECK --interval=30s \
    --timeout=5s \
    --start-period=10s \
    --retries=3 \
    CMD ["python", "-c", "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health', timeout=3)"]

# Graceful termination signal
STOPSIGNAL SIGTERM

# Start application
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]
```

---

### 2.3 Image Lifecycle

```text

Developer Team
    |
    v
Push Code to Source Code Repository
(GitHub / GitLab / Bitbucket)
    |
    v
CI Pipeline Triggered
    |
    v
Checkout Source Code
    |
    v
Run Tests and Code Quality Checks
    |
    v
Build Docker Image
    |
    v
Tag Docker Image
    |
    v
Scan Docker Image
    |
    v
Verify Security Policies
    |
    v
Record Version and Build Metadata
    |
    v
Push Docker Image to Container Registry
(ECR / ACR / GAR / Docker Hub)
    |
    v
Capture Unique Image Digest
(sha256:...)
    |
    v
Generate / Verify Image Provenance
(Optional but Recommended)
    |
    v
CD Pipeline Triggered
    |
    v
Deploy Same Image Digest to DEV
    |
    v
Health Checks and Testing
    |
    v
Promote Same Image Digest to QA
    |
    v
Integration and Regression Testing
    |
    v
Promote Same Image Digest to STAGING
    |
    v
Pre-Production Validation
    |
    v
Promote Same Image Digest to PROD
    |
    v
Production Monitoring and Validation
    |
    v
Application Successfully Running
```

---

### 2.4 Image Building Approaches

Different approaches offers different capabilities related to `build performance`, `caching`, `image size`, `architecture compatibility`, and `build optimization`.

The five important approaches are:
1. Traditional Docker Builds
2. BuildKit Builds
3. Multi-Stage Builds
4. Multi-Platform Builds
5. Buildx Builds

---

#### 2.4.1 Traditional Builds
Traditional builds use the `Docker build process` to create images from a `Dockerfile`.

They execute instructions sequentially and use the `build cache` to *reuse previously built layers* when possible.

##### Characteristics:

- Executes Dockerfile instructions in build order.
- Uses layer-based build caching.
- Creates intermediate image layers.
- Supports standard Dockerfile instructions.
- Traditionally uses the legacy Docker builder.

#### Advantages:

- Simple and easy to understand.
- Suitable for basic Docker image builds.
- Widely supported.

#### Limitations:

- Less efficient build processing than modern BuildKit in many scenarios.
- Limited advanced caching and build capabilities.
- Does not offer the same advanced parallel execution and cache export features as BuildKit.

> **Note:** The legacy builder is deprecated in modern Docker Engine releases. Modern docker build generally uses BuildKit by default.

---

#### 2.4.2 BuildKit Builds
BuildKit is Docker's `modern` build engine designed to improve `image-building performance`, `caching`, and `flexibility`.

Advanced features such as `parallel execution`, `efficient caching`, and improved handling of `build secrets`.

Default in modern Docker installations.

#### Characteristics:

- Supports parallel execution of independent build operations.
- Provides improved build caching.
- Supports cache imports and exports.
- Supports secret and SSH mounts.
- Supports efficient handling of intermediate build stages.
- Supports advanced Dockerfile features.

#### Advantages:

- Faster builds in many scenarios.
- Better cache management.
- Improved security when handling build secrets.
- More efficient build resource utilization.

---

#### 2.4.3 Multi-Stage Builds

Primarily used to separate the application build environment from the final runtime environment.

Application compilation may require `compilers`, `development libraries`, and `build tools`. These dependencies are often unnecessary when running the application.

Multi-stage builds help exclude those unnecessary tools from the final image.

#### Single Stage Build vs Multi Stage Build

| Comparison | Single-Stage Build | Multi-Stage Build |
|---|---|---|
| `Build Process` | Build and runtime operations happen in the same stage. | Build and runtime operations happen in separate stages. |
| `Base Image` | Uses the same image for building and running. | Can use different base images for builder and runtime. |
| `Build Tools & Dependencies` | Build tools and dependencies may remain in the final image. | Build tools are restricted to the builder stage and excluded from the final image when not required at runtime. |
| `Runtime Dependencies` | Installed in the same stage. | Only required runtime dependencies are installed in the final stage. |
| `Docker Image Size` | Usually larger. | Usually smaller. |
| `Security` | Potentially larger attack surface due to unnecessary tools and packages. | Reduced attack surface by excluding unnecessary build tools. |
| `Artifact Management` | Artifacts are generated and used in the same stage. | Artifacts are transferred between stages using `COPY --from`. |
| `Build Isolation` | Less separation between build and runtime environments. | Better separation between build and runtime environments. |
| `Maintenance` | Simple for small applications. | Better organization for complex production applications. |
| `CI/CD Integration` | Can be integrated into CI/CD pipelines. | Well suited for optimized CI/CD pipelines. |
| `Production Usage` | Suitable for simple applications and basic use cases. | Preferred for many production-grade applications. |

---

#### Workflow

``` text
STAGE 1: BUILDER
  Python base image
       |
  Read requirements.txt
       |
  Download/build wheel packages
       |
  Store wheels in /wheels
       |
       | Copy only required artifacts
       v


STAGE 2: RUNTIME
  Fresh Python base image
       |
  Install packages from local wheels
       |
  Copy application source
       |
  Run as a non-root user
       |
  Health check + Uvicorn
```

* The `builder` stage `prepares dependencies`. 
* The `runtime` stage contains the `files needed to execute the application`. 

---

#### Builder Phase vs Runner (Runtime) Phase

| Aspect | Builder Phase | Runner (Runtime) Phase |
| --- | --- | --- |
| `Purpose` | Prepares everything required to run the application. | Executes the application in a production environment. |
| `Base Image Selection` | Pulls the required base image, such as Python, Node.js, or Java. | Starts with a fresh, clean, and lightweight base image, such as `python:3.12-slim`. |
| `Dependencies` | Installs build dependencies such as compilers, development libraries, and build tools. | Installs only the dependencies required to execute the application. |
| `Dependency Files` | Reads files such as `requirements.txt`, `package.json`, or `pom.xml` to identify application dependencies. | Uses the prepared artifacts from the Builder Phase. |
| `Package Management` | Downloads and builds packages, compiling them if required. | Installs the required application dependencies using the copied artifacts. |
| `Build Artifacts` | Generates artifacts such as Python wheel files (`.whl`), Java JAR files, or compiled binaries. | Copies the required build artifacts from the Builder Phase using `COPY --from=builder`. |
| `Application Source Code` | Generally focuses on preparing dependencies and build artifacts. | Copies application source code, configuration files, and other required resources. |
| `Security Configuration` | May require elevated build permissions to install tools and compile packages. | Creates a non-root user and runs the application with minimum required privileges. |
| `Health Checks` | Not typically responsible for application health monitoring. | Defines health checks to monitor whether the application is responding correctly. |
| `Execution` | Does not execute the application as the final runtime environment. | Starts the application using a server such as Uvicorn for FastAPI. |
| `Output` | Produces build artifacts and stores them in a directory for transfer to the next stage. | Produces the final Docker image containing the application and its runtime dependencies. |

---

#### Output of Each Phase

| Builder Phase Output | Runner Phase Output |
| --- | --- |
| `/wheels/` | Final Docker image |
| `fastapi.whl` | Python runtime |
| `uvicorn.whl` | Installed application dependencies |
| `sqlalchemy.whl` | Application source code |
| `other dependencies.whl` | Runtime configurations and security settings |

> **In simple terms**: Builder Phase builds the application dependencies and artifacts, whereas Runner Phase uses those artifacts to create the final production-ready Docker image and run the application.

---

#### 2.4.4 Multi-Platform Builds
Multi-platform builds allow you to build images for multiple `CPU architectures` and operating system platforms.

For example:
- Linux AMD64 (linux/amd64)
- Linux ARM64 (linux/arm64)
- Linux ARM/v7 (linux/arm/v7)

Docker builds the image for each specified platform and publishes a `multi-platform image index`(also called a manifest list in some contexts).

#### Conceptually 

```text

             Docker Image
                  |
          Multi-Platform Index
                  |
          +-------+-------+
          |               |
          v               v
      AMD64 Image      ARM64 Image
          |               |
          v               v
      Intel/AMD       ARM Processors
      Processors

```

When a user pulls the image, Docker selects the appropriate platform-specific image based on the target system.

---

#### Advantages:

- Supports different CPU architectures.
- Enables deployment across heterogeneous infrastructure.
- Simplifies image distribution.
- Maintains a common image name for multiple architectures.

> **Important:** Multi-platform builds may use emulation, native builders, or cross-compilation. Not every application can be cross-compiled without additional configuration.

---

#### 2.4.5 Buildx Builds
`Buildx` is a Docker `CLI plugin` that provides an extended build interface powered by `BuildKit`.

supports advanced `image-building capabilities`, including:
- Multi-platform builds.
- Advanced caching.
- Multiple builder instances.
- Remote builders.
- Build secrets and SSH forwarding.
- Cache export and import.
- Exporting build outputs to different destinations.

##### Multi-platform build command:
```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t username/myapp:v1 \
  --push .
```

#### Advantages:

- Supports multi-platform image builds.
- Provides advanced BuildKit features.
- Supports distributed and remote build environments.
- Offers flexible cache management.
- Supports different build output destinations.

> **Important:** Buildx is not a separate build engine. It is an extended CLI interface that uses BuildKit.

---

#### 2.4.6 Comparison of Image Building Approaches

| Approach | Main Purpose | Key Feature |
|---|---|---|
| `Traditional Builds` | Basic Docker image building. | Standard Docker build process. |
| `BuildKit Builds` | Optimized image building. | Advanced caching and parallel execution. |
| `Multi-Stage Builds` | Reduce final image size. | Separates build and runtime environments. |
| `Multi-Platform Builds` | Support multiple CPU architectures. | Builds AMD64, ARM64, etc. |
| `Buildx Builds` | Advanced image building. | Extended BuildKit capabilities through the CLI. |

---

### 2.5 BuildKit Capabilities








> **Remember :** An image cannot normally be removed if an existing container depends on it.