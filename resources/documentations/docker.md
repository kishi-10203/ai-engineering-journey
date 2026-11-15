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

Docker is an *open-source* containerization platform used to build, package, distribute, and run applications inside isolated environments called containers.

Docker packages an application along with all its dependencies, libraries, configuration files, and runtime requirements into a *single portable unit.*

This portable unit is called a **Docker Image**.

A running instance of a Docker image is called a **Docker Container**.

---

### 1.1 Why Docker

Before containerization, developers faced *environment-related issues.*
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
| **Portability** | Containers run consistently across different environments. |
| **Isolation** | Applications run in isolated containers. |
| **Lightweight** | Containers share the host operating system kernel. |
| **Scalability** | Multiple container instances can be created easily. |
| **Faster Deployment** | Applications can be packaged and deployed consistently. |
| **Resource Efficiency** | Containers generally consume fewer resources than full virtual machines. |
| **Environment Consistency** | Development, testing, and production environments can use the same image. |
| **Version Control** | Docker images can be tagged and versioned. |

---

### 1.2 Container vs Virtual Machine

#### 1.2.1 Virtual Machine
Before Containerization DevOps teams used a single highly capable Server and deployed multiple applications on it.

They split up the single server into multiple virtual machines with the help of a **Hypervisor.**

Each virtual machine generally contained:

- Guest Operating System
- System Libraries
- Application Dependencies
- Application

> #### Virtual Machine Architecture
> Hardware → Host OS → Hypervisor → Guest OS → Application


#### 1.2.2 Container 

A Container is a running instance of a *docker-image*.

it is comparatively lighter than VM's and shares the **Host OS kernel**

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
| **Virtualization** | Hardware-level | OS-level |
| **Operating System** | Separate guest OS | Shares host kernel |
| **Size** | Generally large | Generally smaller |
| **Startup** | Usually slower | Usually faster |
| **Resource Usage** | Higher | Lower |
| **Isolation** | Strong OS boundary | Process-level isolation |
| **Portability** | More heavyweight | Highly portable |
| Example | VMware VM | Docker Container |

> **Important:** Containers are lightweight, but they are not equivalent to virtual machines in terms of security isolation.

---

### 1.3 Docker Architecture
Docker follows a **Client-Server Architecture** consisting of:

| Component | Function |
|---|---|
| **Docker Client** | Accepts user commands (`docker run`, `docker pull`). |
| **Docker Daemon (`dockerd`)** | Processes commands and manages Docker objects. |
| **Docker Host** | Machine where Docker Daemon and containers run. |
| **Docker Registry** | Stores Docker images (e.g., Docker Hub, ECR). |
| **Docker Objects** | Images, containers, networks and volumes managed by Docker. |

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
| **Docker Image** | A read-only template used to create containers. |
| **Docker Container** | A running or stopped instance created from a Docker image. |
| **Docker Network** | Allows containers to communicate with one another and external systems. |
| **Docker Volume** | A mechanism used to persist data outside a container's writable layer. |

---

### 1.5 Docker Terminologies

| Terminology | Definition |
|---|---|
| **Docker Image** | A read-only template used to create containers. |
| **Docker Container** | A running or stopped instance created from a Docker image. |
| **Docker Network** | Allows containers to communicate with one another and external systems. |
| **Docker Volume** | A mechanism used to persist data outside a container's writable layer. |
| **Dockerfile** | A text file containing instructions used to build a Docker image. |
| **Docker Compose** | A tool used to define and manage multi-container applications through a YAML configuration file. |
| **Docker Registry** | A storage and distribution system for Docker images. |
| **Docker Repository** | A collection of related images, commonly organized by application or project. |
| **Docker Tag** | Identifies a particular version or variant of an image. |
| **Docker Engine** | Core technology responsible for building and running Docker containers. |
| **Docker CLI** | Command-line interface used to interact with Docker. |
| **Docker API** | Interface through which clients communicate with the Docker daemon. |
| **Docker Daemon (`dockerd`)** | Background service responsible for managing Docker objects. |
| **Docker Host** | Machine where Docker runs and containers are hosted. |
| **Docker Hub** | Cloud-based registry used to store and distribute Docker images. |
| **Image Layer** | Each Docker image consists of multiple layers representing filesystem changes. |
| **Container Layer** | Writable layer added on top of image layers when a container is created. |
| **Docker Context** | Defines the Docker endpoint and configuration used by the CLI to communicate with a Docker daemon. |
| **Docker Build** | Process of building a Docker image using a Dockerfile or other build instructions. |
| **Docker Pull** | Downloads an image from a registry. |
| **Docker Push** | Uploads an image to a registry. |
| **Bind Mount** | Mounts a file or directory from the host machine into a container. |
| **Port Mapping** | Maps a container port to a host port to allow external access. |
| **Environment Variables** | Pass configuration values to applications running inside containers. |
| **Container Runtime** | Software responsible for running containers. |
| **Restart Policy** | Defines whether a container should restart automatically under specified conditions. |
| **Health Check** | Checks whether an application inside a container is functioning correctly. |
| **Volume Driver** | Determines how and where Docker volumes are stored. |

---

### 1.6 Docker Commands

#### 1.6.1  Basic Docker Commands

| Command | Description | Example |
|---|---|---|
| `docker --version` | Displays the Docker CLI version. | `docker --version` |
| `docker version` | Displays Docker client and server version information. | `docker version` |
| `docker info` | Displays information about the Docker installation and daemon. | `docker info` |
| `docker help` | Displays available Docker commands. | `docker help` |
| `docker COMMAND --help` | Displays help for a specific command. | `docker run --help` |

---

#### 1.6.2  Docker Image Commands

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

#### 1.6.3  Docker Container Commands

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

#### 1.6.4  Docker Registry Commands

| Command | Description | Example |
|---|---|---|
| `docker login` | Authenticates with a Docker registry. | `docker login` |
| `docker logout` | Logs out from a Docker registry. | `docker logout` |
| `docker search` | Searches Docker Hub for images. | `docker search nginx` |
| `docker pull` | Downloads an image from a registry. | `docker pull nginx` |
| `docker push` | Uploads an image to a registry. | `docker push username/myapp:latest` |
| `docker manifest inspect` | Displays an image manifest. | `docker manifest inspect nginx:latest` |

---

#### 1.6.5  Docker Network Commands

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

#### 1.6.6  Docker Volume Commands

| Command | Description | Example |
|---|---|---|
| `docker volume ls` | Lists Docker volumes. | `docker volume ls` |
| `docker volume create` | Creates a new Docker volume. | `docker volume create myvolume` |
| `docker volume inspect` | Displays detailed volume information. | `docker volume inspect myvolume` |
| `docker volume rm` | Removes a Docker volume. | `docker volume rm myvolume` |
| `docker volume prune` | Removes unused Docker volumes. | `docker volume prune` |

---

#### 1.6.7  Docker System Commands

| Command | Description | Example |
|---|---|---|
| `docker system df` | Displays Docker disk usage. | `docker system df` |
| `docker system info` | Displays Docker system information. | `docker system info` |
| `docker system events` | Displays real-time Docker events. | `docker system events` |
| `docker system prune` | Removes unused Docker resources. | `docker system prune` |
| `docker system prune -a` | Removes unused images, stopped containers, unused networks and build cache. | `docker system prune -a` |
| `docker system prune --volumes` | Also removes eligible unused anonymous volumes. | `docker system prune --volumes` |

---

#### 1.6.8  Docker Build Commands

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

#### 1.6.9  Docker Compose Commands

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

#### 1.6.10  Docker Context Commands

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

#### 1.6.11  Docker Swarm Commands

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

#### 1.6.12  Other Useful Docker Commands

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

#### 1.6.13  Cleanup Commands

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

A Docker image is an immutable artifact that contains everything required to run an application.

Images are generally built using a `Dockerfile`.

Images are *layered filesystem template*, they are built by layers defined in the `Dockerfile`.

---

### 2.1 Image Layers and Union Filesystem

Docker images are built using *multiple read-only layers* stacked on top of each other. Docker uses a technology called a *Union Filesystem (UnionFS)* to combine these layers and make them appear as one unified filesystem.

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
| Access | Read-only | Read/write |
| Created during | Image build | Container creation |
| Shared between containers | Yes | No, each container has its own |
| Changes | Cannot be modified in place | Can be modified |
| Lifetime | Until image/layer is removed | Until container is removed |
| Persistence | Image persists | Removed with container |

---

#### 2.1.3 Why Does Docker Use Image Layers?

There are several important advantages.

1. Image Reuse
2. Faster Image Downloads
3. Build Caching
4. Storage Efficiency

| Advantage | Explanation | Example |
|---|---|---|
| **Image Reuse** | Multiple Docker images can share common base and intermediate layers instead of creating duplicate copies. | Two applications using `ubuntu:22.04` and Nginx can reuse the same existing layers. |
| **Faster Image Downloads** | Docker downloads only the layers that are missing locally instead of downloading the entire image again. | If an image has 5 layers and your machine already has 4, Docker downloads only the remaining 1 layer. |
| **Build Caching** | Docker reuses previously built layers if the Dockerfile instructions and their dependencies haven't changed. | If the Ubuntu and Nginx installation layers are unchanged, Docker can reuse them during the next build. |
| **Storage Efficiency** | Multiple images can share identical layers, reducing duplicate storage on the host machine. | Two application images sharing the same Ubuntu base layer don't need separate copies of that layer. |


#### Summary

| Concept | Benefit |
|---|---|
| Image Reuse | Avoids duplicating common layers. |
| Faster Downloads | Downloads only missing layers. |
| Build Caching | Avoids rebuilding unchanged layers. |
| Storage Efficiency | Saves disk space by sharing layers. |

---

### 2.2 Dockerfile

A `Dockerfile` is a text file that contains a set of instructions used to build a Docker image.

It tells Docker:
- Which `base image` to use.
- Which `dependencies` to install.
- Which `application files` to copy.
- Which commands to execute during image creation.
- Which command to run when a container starts.

---

#### 2.2.1 Dockerfile Instructions 

| # | Instruction | Description | Example |
|---|---|---|---|
| 1 | **FROM** | Defines the base image. Every build stage starts with a FROM instruction. | `FROM ubuntu:24.04` |
| 2 | **WORKDIR** | Sets the working directory inside the image. | `WORKDIR /app` |
| 3 | **COPY** | Copies files and directories from the build context into the image. | `COPY . .` |
| 4 | **ADD** | Copies files and supports additional functionality, such as extracting local tar archives. Prefer COPY for ordinary file copying. | `ADD application.tar.gz /app/` |
| 5 | **RUN** | Executes commands during image building. | `RUN apt-get update` |
| 6 | **CMD** | Defines the default command or arguments executed when a container starts. Only the last CMD instruction takes effect. | `CMD ["python", "app.py"]` |
| 7 | **ENTRYPOINT** | Defines the main executable for the container. Can be combined with CMD to provide default arguments. | `ENTRYPOINT ["python"]` |
| 8 | **EXPOSE** | Documents the port on which the application listens. It does not automatically publish the port to the host. | `EXPOSE 8080` |
| 9 | **ENV** | Defines environment variables available in subsequent build instructions and containers. | `ENV APP_ENV=production` |
| 10 | **ARG** | Defines build-time variables that can be passed during image building. | `ARG APP_VERSION=1.0` |
| 11 | **USER** | Specifies the user for subsequent build instructions and the container's default runtime process. | `USER appuser` |
| 12 | **LABEL** | Adds metadata to a Docker image, such as version, author, or description. | `LABEL version="1.0"` |
| 13 | **VOLUME** | Creates a mount point for external or persistent data. | `VOLUME ["/data"]` |
| 14 | **HEALTHCHECK** | Defines a command to check whether the container is functioning correctly. | `HEALTHCHECK CMD curl -f http://localhost/ || exit 1` |
| 15 | **ONBUILD** | Defines an instruction that executes later when another image uses the current image as its base. | `ONBUILD COPY . /app` |
| 16 | **STOPSIGNAL** | Specifies the signal sent to the container when Docker stops it. | `STOPSIGNAL SIGTERM` |
| 17 | **SHELL** | Changes the default shell used by shell-form instructions such as RUN, CMD, and ENTRYPOINT. | `SHELL ["/bin/bash", "-c"]` |
| 18 | **MAINTAINER** | Specifies the image author or maintainer. Deprecated; use LABEL instead. | `MAINTAINER developer@example.com` |
| 19 | **# (Comment)** | Adds comments to a Dockerfile. Comments are ignored during normal instruction processing. | `# Install application dependencies` |


#### Notes 

> 1. **FROM:** Every build stage must begin with FROM. Global ARG declarations and comments can appear before the first FROM.
> 2. **CMD:** If multiple CMD instructions exist, only the last effective CMD is used.
> 3. **EXPOSE:** Does not automatically publish a container port. Use `-p` with `docker run` to publish it.
> 4. **RUN vs CMD:**
>   - RUN executes commands while building the image.
>   - CMD defines the default command when starting a container.
> 5. **ENTRYPOINT vs CMD:**
>   - ENTRYPOINT defines the main executable.
>   - CMD provides default arguments or a default command.
>
> **`MAINTAINER:`** is Deprecated. Use LABEL to specify author or maintainer information.
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
| Build Process | Build and runtime operations happen in the same stage. | Build and runtime operations happen in separate stages. |
| Base Image | Uses the same image for building and running. | Can use different base images for builder and runtime. |
| Build Tools & Dependencies | Build tools and dependencies may remain in the final image. | Build tools are restricted to the builder stage and excluded from the final image when not required at runtime. |
| Runtime Dependencies | Installed in the same stage. | Only required runtime dependencies are installed in the final stage. |
| Docker Image Size | Usually larger. | Usually smaller. |
| Security | Potentially larger attack surface due to unnecessary tools and packages. | Reduced attack surface by excluding unnecessary build tools. |
| Artifact Management | Artifacts are generated and used in the same stage. | Artifacts are transferred between stages using `COPY --from`. |
| Build Isolation | Less separation between build and runtime environments. | Better separation between build and runtime environments. |
| Maintenance | Simple for small applications. | Better organization for complex production applications. |
| CI/CD Integration | Can be integrated into CI/CD pipelines. | Well suited for optimized CI/CD pipelines. |
| Production Usage | Suitable for simple applications and basic use cases. | Preferred for many production-grade applications. |

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

* The *builder* stage **prepares dependencies.** 
* The *runtime* stage contains the **files needed to execute the application.** 

---

#### Builder Phase vs Runner (Runtime) Phase

| Aspect | Builder Phase | Runner (Runtime) Phase |
| --- | --- | --- |
| Purpose | Prepares everything required to run the application. | Executes the application in a production environment. |
| Base Image Selection | Pulls the required base image, such as Python, Node.js, or Java. | Starts with a fresh, clean, and lightweight base image, such as `python:3.12-slim`. |
| Dependencies | Installs build dependencies such as compilers, development libraries, and build tools. | Installs only the dependencies required to execute the application. |
| Dependency Files | Reads files such as `requirements.txt`, `package.json`, or `pom.xml` to identify application dependencies. | Uses the prepared artifacts from the Builder Phase. |
| Package Management | Downloads and builds packages, compiling them if required. | Installs the required application dependencies using the copied artifacts. |
| Build Artifacts | Generates artifacts such as Python wheel files (`.whl`), Java JAR files, or compiled binaries. | Copies the required build artifacts from the Builder Phase using `COPY --from=builder`. |
| Application Source Code | Generally focuses on preparing dependencies and build artifacts. | Copies application source code, configuration files, and other required resources. |
| Security Configuration | May require elevated build permissions to install tools and compile packages. | Creates a non-root user and runs the application with minimum required privileges. |
| Health Checks | Not typically responsible for application health monitoring. | Defines health checks to monitor whether the application is responding correctly. |
| Execution | Does not execute the application as the final runtime environment. | Starts the application using a server such as Uvicorn for FastAPI. |
| Output | Produces build artifacts and stores them in a directory for transfer to the next stage. | Produces the final Docker image containing the application and its runtime dependencies. |

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







> **Remember :** An image cannot normally be removed if an existing container depends on it.

---

> ## 6. Understanding Docker Containers

### 6.1 Container Commands 
| Docker Command | Description | Example |
|---|---|---|
| **Create and Run a Container** | Creates a container from an image and starts it in the foreground. | `docker run nginx` |
| **Run Container in Detached Mode** | Runs the container in the background. The `-d` option enables detached mode. | `docker run -d nginx` |
| **Assign a Container Name** | Assigns a custom name to the container using the `--name` option. | `docker run -d --name my-nginx nginx` |
| **List Running Containers** | Displays currently running containers. | `docker ps` |
| **List All Containers** | Displays all existing containers, including running and stopped containers. | `docker ps -a` |
| **Stop a Container** | Gracefully stops a running container. | `docker stop my-nginx` |
| **Start a Stopped Container** | Starts an existing stopped container. | `docker start my-nginx` |
| **Restart a Container** | Stops and starts an existing container. | `docker restart my-nginx` |
| **Remove a Container** | Removes a stopped container from the system. | `docker rm my-nginx` |
| **Force Remove a Container** | Forcefully removes a container, including a running container. | `docker rm -f my-nginx` |


### 6.2 Execute Commands Inside a Container

```bash
docker exec -it my-nginx bash
```

Breakdown:

- `exec`: Execute a command in an existing container.
- `-i`: Keep standard input open.
- `-t`: Allocate a pseudo-terminal.
- `bash`: Start a Bash shell.

Some minimal images do not include Bash. In those cases:

```bash
docker exec -it my-nginx sh
```

### 6.3 View Container Logs

| Docker Command | Description | Example |
|---|---|---|
| **View Container Logs** | Displays the logs generated by a container. | `docker logs my-nginx` |
| **Follow Container Logs** | Displays logs continuously as new log entries are generated. The `-f` option enables follow mode. | `docker logs -f my-nginx` |
| **Display Latest Logs** | Displays only the specified number of latest log lines. | `docker logs --tail 100 my-nginx` |


### 6.4 Container Port Mapping

Containers have their own network interfaces and ports.

To access an Nginx container through the host machine:

```bash
docker run -d -p 8080:80 nginx
```

Here:

- Host port: 8080
- Container port: 80

Access:

```text
http://localhost:8080
```

> **Important:** Port mapping follows this convention:

```text
HOST_PORT:CONTAINER_PORT
```

---

> ## 7. Dockerfile Fundamentals

### 7.1 What is a Dockerfile?

A Dockerfile is a text-based instruction file used to automate Docker image creation.

It defines:

- Base image
- Working directory
- Application dependencies
- Application files
- Exposed ports
- Startup commands

### 7.2 Common Dockerfile Instructions

| Dockerfile Instruction | Description | Example |
|---|---|---|
| **FROM** | Defines the base image. Every normal Docker build begins with a `FROM` instruction. | `FROM ubuntu:24.04` |
| **WORKDIR** | Sets the working directory inside the image. Subsequent relative paths are interpreted from this directory. | `WORKDIR /app` |
| **COPY** | Copies files and directories from the build context into the image. | `COPY . .` |
| **ADD** | Copies files and supports additional functionality, such as extracting certain local tar archives. Prefer `COPY` for ordinary file copying. | `ADD application.tar.gz /app/` |
| **RUN** | Executes commands during image building. | `RUN apt-get update` or `RUN pip install flask` |
| **CMD** | Defines the default command or arguments executed when a container starts. A Dockerfile can have multiple `CMD` instructions, but only the last effective one is used. | `CMD ["python", "app.py"]` |
| **ENTRYPOINT** | Defines the main executable for the container. It can be combined with `CMD` to provide default arguments. | `ENTRYPOINT ["python"]` |
| **EXPOSE** | Documents the port on which the application is expected to listen. It does not automatically publish the port to the host. | `EXPOSE 8080` |
| **ENV** | Defines environment variables that are available in subsequent build instructions and containers. | `ENV APP_ENV=production` |
| **ARG** | Defines build-time variables that can be passed during image building. | `ARG APP_VERSION=1.0` |
| **USER** | Specifies the user used for subsequent build instructions and the container's default runtime process. | `USER appuser` |
| **LABEL** | Adds metadata to a Docker image. | `LABEL maintainer="development-team"` |


Additional examples:

**ENTRYPOINT + CMD**

dockerfile

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

**Resulting command:**

bash

```bash
python app.py
```

**ARG usage during build**

bash

```bash
docker build --build-arg APP_VERSION=2.0 -t myapp .
```

---

> ## 9. Build Your First Docker Image

Let's create a simple Python application and package it using Docker.

### 9.1 Project Structure

```text
my-python-app/
│
├── Dockerfile
├── requirements.txt
└── app.py
```

### 9.2 Create app.py

python

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Docker!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

Notice that we use `0.0.0.0` as the host.

This allows Flask to listen on the container's network interfaces rather than only its loopback interface.

### 9.3 Create requirements.txt

```text
flask
```

### 9.4 Create Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

### 9.5 Build the Docker Image

Run this command from the project directory:

```bash
docker build -t my-python-app:1.0 .
```

Explanation:

- `docker build`: Builds an image.
- `-t`: Assigns an image name and tag.
- `my-python-app:1.0`: Image name and version.
- `.`: Current directory is the build context.

### 9.6 Verify the Image

```bash
docker images
```

### 9.7 Run the Container

```bash
docker run -d \
  --name python-container \
  -p 5000:5000 \
  my-python-app:1.0
```

### 9.8 Test the Application

Open:

```text
http://localhost:5000
```

Expected output:

```text
Hello from Docker!
```

Congratulations\! We have successfully built and executed our first Dockerized application.

---

> ## 10. Docker Image Layers and Caching

### 10.1 What are Docker Image Layers?

Docker images are composed of multiple filesystem layers.

Most Dockerfile instructions create or contribute to image layers.

Example:

```dockerfile
FROM ubuntu:24.04

RUN apt-get update

RUN apt-get install -y python3

COPY app.py /app/

CMD ["python3", "/app/app.py"]
```

Conceptually, the image contains:

1. Base Ubuntu image
2. Package index update layer
3. Python installation layer
4. Application file layer
5. Image configuration for the default command

Image layers are reused wherever possible.

### 10.2 What is Docker Layer Caching?

Docker uses cached build results to avoid repeating unchanged build operations.

If an instruction and its relevant inputs have not changed, Docker may reuse its previous build result.

This makes image building faster.

### 10.3 Example of Inefficient Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt
```

Problem:

If any application source file changes, the `COPY . .` layer may be invalidated, causing the dependency installation layer to be rebuilt.

### 10.4 Optimized Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

Why is this better?

- Dependency files are copied separately.
- Dependencies are installed before application source code is copied.
- Changes to application code do not necessarily invalidate the dependency installation layer.

### 10.5 Docker Build Context

The build context is the set of files made available to the Docker build process.

Example:

```bash
docker build -t myapp .
```

Here, `.` represents the current directory as the build context.

### 10.6 .dockerignore

A `.dockerignore` file specifies files and directories that should be excluded from the build context.

Example:

```text
.git
.gitignore
node_modules
__pycache__
*.log
.env
venv
dist
```

Benefits:

- Smaller build context
- Faster builds
- Reduced risk of including unnecessary files
- Better protection against accidentally copying sensitive files

---

> ## 11. Docker Volumes and Storage

### 11.1 Why is Docker Storage Required?

Containers are generally designed to be disposable.

Data stored only in a container's writable layer can be lost when the container is removed.

For example:

Suppose a PostgreSQL database runs inside a container.

If the container is deleted without persistent storage, the database files stored only in its writable layer may be lost.

Docker provides persistent storage mechanisms to solve this problem.

### 11.2 Types of Docker Storage

Docker commonly provides three storage approaches:

1. Volumes
2. Bind Mounts
3. tmpfs Mounts

### 11.3 Docker Volumes

Volumes are managed by Docker and are the preferred mechanism for persistent container data in many use cases.

| Docker Volume Command | Description | Example |
|---|---|---|
| **Create a Volume** | Creates a new Docker volume. | `docker volume create mydata` |
| **List Volumes** | Displays all Docker volumes available on the system. | `docker volume ls` |
| **Inspect a Volume** | Displays detailed information about a specific volume. | `docker volume inspect mydata` |
| **Remove a Volume** | Removes a Docker volume that is not in use. | `docker volume rm mydata` |
| **Use a Volume** | Mounts a Docker volume inside a container to persist data. | `docker run -d --name db-container -v mydata:/var/lib/postgresql/data postgres` |

Here:

- `mydata`: Docker volume.
- `/var/lib/postgresql/data`: Container data directory.

The volume persists independently of the container lifecycle.

### 11.4 Bind Mounts

A bind mount connects a specific directory or file on the host machine to a location inside the container.

Example:

```bash
docker run -d \
  --name web-container \
  -v /home/user/project:/app \
  python:3.12-slim
```

Here:

- Host path: `/home/user/project`
- Container path: `/app`

Changes made to the mounted directory can be reflected on both sides.

Bind mounts are especially useful during development.

### 11.5 tmpfs Mounts

A tmpfs mount stores data in memory rather than persistent disk storage.

Example:

```bash
docker run -d \
  --name temporary-container \
  --tmpfs /app/cache \
  nginx
```

Characteristics:

- Data is temporary.
- Data is not persisted after the container stops.
- Useful for temporary data and certain sensitive runtime files.

### 11.6 Volume vs Bind Mount

| Feature | Volume | Bind Mount |
| --- | --- | --- |
| Managed By | Docker | Host OS |
| Host Path | Docker-managed | User-defined |
| Portability | Generally easier | Host-path dependent |
| Development Usage | Supported | Very common |
| Persistence | Yes | Yes |
| Backup | Supported | Host filesystem tools |

> **Important:** A volume is not the same as a container's writable layer.

---

> ## 12. Docker Networking

### 12.1 What is Docker Networking?

Docker Networking allows containers to communicate with:

- Other containers
- The Docker host
- External networks
- Internet services

### 12.2 Types of Docker Networks

Docker provides several network drivers.

#### Bridge Network

The default networking driver for many standalone Docker containers.

Containers connected to the same user-defined bridge network can communicate using container names.

Create a bridge network:

```bash
docker network create my-network
```

Run a container:

```bash
docker run -d \
  --name container1 \
  --network my-network \
  nginx
```

Run another container:

```bash
docker run -d \
  --name container2 \
  --network my-network \
  busybox sleep 3600
```

Inspect the network:

```bash
docker network inspect my-network
```

#### Host Network

The container shares the host's network namespace.

Example:

```bash
docker run --network host nginx
```

In host networking mode, port publishing with `-p` is generally unnecessary and ignored.

Host networking support and behavior differ by platform.

#### None Network

Disables normal container networking.

Example:

```bash
docker run --network none nginx
```

#### Overlay Network

Overlay networks allow containers connected across Docker hosts to communicate.

They are commonly used with Docker Swarm services.

### 12.3 Docker Network Commands

| Docker Network Command | Description | Example |
|---|---|---|
| **List Networks** | Displays all Docker networks available on the system. | `docker network ls` |
| **Create a Network** | Creates a new Docker network. | `docker network create my-network` |
| **Inspect a Network** | Displays detailed information about a specific network. | `docker network inspect my-network` |
| **Connect a Container** | Connects an existing container to a Docker network. | `docker network connect my-network my-container` |
| **Disconnect a Container** | Disconnects a container from a Docker network. | `docker network disconnect my-network my-container` |
| **Remove a Network** | Removes a Docker network that is not in use. | `docker network rm my-network` |


### 12.4 Container-to-Container Communication

Consider two containers:

- Backend
- Database

If both containers are attached to the same user-defined bridge network, the backend can generally connect to the database using its container name as the hostname.

Example:

```text
Database hostname: postgres-db
Database port: 5432
```

Connection string:

```text
postgresql://username:password@postgres-db:5432/mydb
```

> **Important:** Inside a container, `localhost` refers to that same container, not another container.

---

> ## 13. Docker Registry and Docker Hub

### 13.1 What is Docker Hub?

Docker Hub is a cloud-based container image registry.

It allows users to:

- Store images
- Download images
- Share images
- Manage repositories
- Publish application images

Website:

[https://hub.docker.com/](<https://hub.docker.com/>)

| Docker Command | Description | Example |
|---|---|---|
| **Login to Docker Hub** | Authenticates the user with Docker Hub. | `docker login` |
| **Tag an Image** | Assigns a repository name and tag to an existing image. | `docker tag my-python-app:1.0 username/my-python-app:1.0` |
| **Push an Image** | Uploads a Docker image to Docker Hub. | `docker push username/my-python-app:1.0` |
| **Pull an Image** | Downloads a Docker image from Docker Hub to the local machine. | `docker pull username/my-python-app:1.0` |
| **Build an Image** | Builds a Docker image from a Dockerfile in the current directory. | `docker build -t my-python-app:1.0 .` |

---

> ## 14. Docker Compose

### 14.1 What is Docker Compose?

Docker Compose is a tool used to define and manage multi-container applications.

Instead of manually creating multiple containers using separate Docker commands, we can define all application services inside one YAML file.

Common use cases:

- Microservices applications
- Web applications
- Database-backed applications
- Development environments
- Local testing environments

### 14.2 Why Docker Compose?

Consider an application with:

1. Frontend
2. Backend
3. PostgreSQL database
4. Redis cache

Without Compose, we must manually:

- Create containers
- Configure networks
- Configure environment variables
- Configure port mappings
- Configure volumes
- Manage startup dependencies

Docker Compose simplifies this process.

### 14.3 Docker Compose YAML Structure

The primary configuration file is commonly named:

```text
compose.yaml
```

A Compose file contains services, networks, volumes, and other configuration.

### 14.4 Example: Python Application with PostgreSQL

Project structure:

```text
my-project/
│
├── app.py
├── requirements.txt
├── Dockerfile
└── compose.yaml
```

Create `compose.yaml`:

```yaml
services:

  web:
    build: .
    container_name: python-web
    ports:
      - "5000:5000"
    environment:
      DB_HOST: db
      DB_PORT: 5432
      DB_NAME: mydb
      DB_USER: admin
      DB_PASSWORD: example-password
    depends_on:
      - db

  db:
    image: postgres:17
    container_name: postgres-db
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: example-password
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```

This example illustrates the Compose structure. A real application must also implement the database connection and retry logic.

markdown

### Docker Compose Commands

| Docker Compose Command | Description | Example |
|---|---|---|
| **Start Services** | Creates and starts all services defined in the `compose.yaml` or `docker-compose.yml` file. | `docker compose up` |
| **Start Services in Detached Mode** | Starts services in the background. The `-d` option enables detached mode. | `docker compose up -d` |
| **Stop Services** | Stops and removes containers and networks created by Docker Compose. | `docker compose down` |
| **Stop and Remove Volumes** | Stops and removes containers, networks, and Compose-managed named volumes. **Warning:** Persistent database data stored in these volumes can be permanently deleted. | `docker compose down -v` |
| **List Services** | Displays the status of services and their containers. | `docker compose ps` |
| **View Logs** | Displays logs from all Compose services. | `docker compose logs` |
| **Follow Logs** | Displays logs continuously as new log entries are generated. | `docker compose logs -f` |
| **View Specific Service Logs** | Displays and follows logs for a specific service. | `docker compose logs -f web` |
| **Rebuild Services** | Rebuilds images before starting the services. | `docker compose up --build` |

### 14.11 Docker Compose Important Concepts

| Docker Compose Key | Description | Example |
|---|---|---|
| **services** | Defines the containers or application services. | `services:` |
| **build** | Specifies how to build the Docker image. | `build: .` |
| **image** | Specifies the Docker image to use. | `image: nginx:latest` |
| **ports** | Defines host-to-container port mapping. | `ports: ["8080:80"]` |
| **environment** | Defines environment variables for a service. | `environment: ["APP_ENV=production"]` |
| **volumes** | Defines persistent storage and volume mounts. | `volumes: ["mydata:/app/data"]` |
| **networks** | Defines communication networks between services. | `networks: ["my-network"]` |
| **depends_on** | Controls service startup ordering and can define dependency conditions. | `depends_on: ["db"]` |

> **Important:** `depends_on` does not automatically guarantee that a database is ready to accept connections. Use health checks and application-level retry logic when readiness matters.

---

> ## 15. Docker Environment Variables

### 15.1 What are Environment Variables?

Environment variables are key-value pairs used to configure applications at runtime.

Examples:

- Database hostname
- Database username
- Application environment
- API endpoints
- Logging level

### 15.2 Set Environment Variables During Container Creation

```bash
docker run -d \
  --name my-app \
  -e APP_ENV=production \
  -e APP_PORT=5000 \
  my-python-app:1.0
```

### 15.3 Use an Environment File

Create `.env`:

```text
APP_ENV=production
APP_PORT=5000
DB_HOST=localhost
```

Run:

```bash
docker run --env-file .env my-python-app:1.0
```

### 15.4 Environment Variables in Docker Compose

```yaml
services:
  web:
    image: my-python-app:1.0
    environment:
      APP_ENV: production
      APP_PORT: 5000
```

**Security Note:** Environment variables are convenient for configuration, but they are not a secure secret-management system. Avoid committing credentials to source control or baking them into images. Use Docker secrets or an appropriate external secret manager for sensitive values.

---

> ## 16. Docker Container Lifecycle

A Docker container moves through different lifecycle states.

### 16.1 Docker Container States 

| Container State | Description | Example / Command |
|---|---|---|
| **Created** | Container has been created but has not started. | `docker create nginx` |
| **Running** | Container is actively executing its main process. | `docker start container-name` |
| **Paused** | Container processes are temporarily suspended. | `docker pause container-name` |
| **Unpaused** | Resumes a previously paused container. | `docker unpause container-name` |
| **Exited** | The container's main process has terminated. | `docker stop container-name` |
| **Dead** | The container is in a failed or unrecoverable state. Further inspection may be required. | Inspect using `docker inspect container-name` |

### 16.2 Container Restart Policies

Docker supports restart policies.

| Policy | Meaning |
| --- | --- |
| no | Do not automatically restart |
| always | Always attempt to restart |
| unless-stopped | Restart unless explicitly stopped |
| on-failure | Restart when the process exits unsuccessfully |

Example:

```bash
docker run -d \
  --restart unless-stopped \
  --name my-app \
  nginx
```

---

> ## 17. Docker Resource Management

Containers share host resources, but Docker allows resource limits to be configured.

### 17.1 CPU Limits

Example:

```bash
docker run -d \
  --cpus="1.5" \
  nginx
```

Limits the container to approximately 1.5 CPU cores worth of CPU time.

### 17.2 Memory Limits

```bash
docker run -d \
  --memory="512m" \
  nginx
```

Limits container memory usage to 512 MB.

### 17.3 CPU and Memory Together

```bash
docker run -d \
  --cpus="1.0" \
  --memory="512m" \
  nginx
```

### 17.4 Monitor Container Resources

```bash
docker stats
```

Displays real-time resource usage.

For a specific container:

```bash
docker stats my-container
```

### 17.5 Inspect Resource Configuration

```bash
docker inspect my-container
```

Resource limits help prevent one container from consuming excessive host resources.

---

> ## 18. Docker Logs and Debugging

Debugging is an important part of Docker administration.

### Docker Container Debugging Commands – Quick Reference Table

| Docker Command | Description | Example |
|---|---|---|
| **View Container Logs** | Displays logs generated by a container. | `docker logs container-name` |
| **Follow Logs** | Displays logs continuously as new log entries are generated. | `docker logs -f container-name` |
| **View Recent Logs** | Displays only the specified number of latest log lines. | `docker logs --tail 50 container-name` |
| **Inspect a Container** | Displays detailed information about a container, including its configuration, state, network settings, and mounts. | `docker inspect container-name` |
| **Execute Shell Inside Container** | Opens an interactive shell session inside a running container. | `docker exec -it container-name sh` |
| **View Container Processes** | Displays the processes currently running inside a container. | `docker top container-name` |
| **Check Container Resource Usage** | Displays real-time CPU, memory, network, and disk I/O usage of running containers. | `docker stats` |

Useful information includes:

- Container ID
- Image
- Network settings
- IP address
- Mounts
- Environment variables
- Runtime configuration
- State


### 18.2 Common Docker Issues

#### Issue 1: Container Exits Immediately

Possible reasons:

- Main process completed.
- Application crashed.
- Incorrect startup command.
- Missing dependencies.

Troubleshooting:

```bash
docker ps -a
docker logs container-name
```

#### Issue 2: Port Already Allocated

Error:

```text
Bind for 0.0.0.0:8080 failed: port is already allocated
```

Possible reason:

Another process or container is already using that host port.

Solution:

Use another host port:

```bash
docker run -d -p 8081:80 nginx
```

#### Issue 3: Image Not Found

Error:

```text
Unable to find image locally
```

Possible reasons:

- Incorrect image name.
- Incorrect tag.
- Registry authentication issue.
- Image does not exist.

Solution:

```bash
docker pull nginx
```

#### Issue 4: Container Cannot Connect to Database

Possible reasons:

- Wrong database hostname.
- Incorrect credentials.
- Different Docker networks.
- Database is not ready.
- Incorrect port.

Check:

```bash
docker network ls
docker inspect container-name
docker logs database-container
```

#### Issue 5: Permission Denied

Possible reasons:

- Incorrect filesystem permissions.
- User mismatch.
- Docker socket permission issues.
- Bind mount ownership problems.

Investigate the actual permission boundary instead of blindly running everything as root.

---

> ## 19. Docker Security Fundamentals

Security is a critical part of containerization.

### 19.1 Run Containers as Non-Root Users

By default, some images run processes as root.

Prefer using a dedicated non-root user.

Example:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

RUN useradd --create-home appuser

COPY --chown=appuser:appuser . .

USER appuser

CMD ["python", "app.py"]
```

### 19.2 Use Trusted Base Images

Use official or otherwise trusted images.

Avoid downloading images from unknown sources without verifying their origin.

### 19.3 Keep Images Updated

Regularly update:

- Base images
- Application dependencies
- Operating system packages
- Runtime versions

### 19.4 Do Not Store Secrets in Dockerfiles

Avoid:

```dockerfile
ENV DB_PASSWORD=mysecretpassword
```

Secrets baked into images can remain accessible through image metadata, layers, or build history.

Use appropriate secret-management mechanisms.

### 19.5 Use Minimal Base Images

Smaller base images can reduce:

- Image size
- Unnecessary packages
- Attack surface
- Download time

Examples:

```dockerfile
FROM python:3.12-slim
```

Distroless images can also be considered where appropriate.

### 19.6 Scan Images for Vulnerabilities

Use image-scanning tools such as:

- Docker Scout
- Trivy

Example:

```bash
docker scout quickview my-python-app:1.0
```

### 19.7 Avoid Privileged Containers

Avoid using:

```bash
docker run --privileged nginx
```

unless there is a specific, justified requirement.

Privileged mode grants substantially expanded access to host resources.

### 19.8 Apply the Principle of Least Privilege

Containers should receive only the permissions, capabilities, mounts, and resources required for their intended purpose.

---

> ## 20. Docker Best Practices

1. Use official and trusted base images.
2. Prefer small and minimal base images.
3. Use explicit image versions instead of relying on `latest`.
4. Use `.dockerignore`.
5. Optimize Dockerfile instruction ordering.
6. Combine related package-management operations where appropriate.
7. Avoid unnecessary packages and files.
8. Run applications using non-root users.
9. Keep containers focused on one primary responsibility.
10. Persist important data using volumes.
11. Use user-defined bridge networks for application communication.
12. Apply CPU and memory limits where appropriate.
13. Use environment variables for runtime configuration.
14. Never bake secrets into Docker images.
15. Scan images regularly.
16. Keep images and dependencies updated.
17. Use Docker Compose for local multi-container applications.
18. Monitor container logs and resource usage.
19. Use health checks where application readiness matters.
20. Keep production images immutable and versioned.

---

> ## 21. Frequently Used Docker Commands

### Image Commands

| Command | Description |
| --- | --- |
| `docker images` | List images |
| `docker pull nginx` | Download image |
| `docker build -t app .` | Build image |
| `docker rmi nginx` | Remove image |
| `docker image inspect nginx` | Inspect image |
| `docker tag app:v1 user/app:v1` | Tag image |
| `docker push user/app:v1` | Push image |

### Container Commands

| Command | Description |
| --- | --- |
| `docker run nginx` | Create and run container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop container` | Stop container |
| `docker start container` | Start container |
| `docker restart container` | Restart container |
| `docker rm container` | Remove container |
| `docker logs container` | View logs |
| `docker exec -it container sh` | Access container shell |
| `docker inspect container` | Inspect container |
| `docker stats` | Monitor resources |

### Volume Commands

| Command | Description |
| --- | --- |
| `docker volume ls` | List volumes |
| `docker volume create data` | Create volume |
| `docker volume inspect data` | Inspect volume |
| `docker volume rm data` | Remove volume |

### Network Commands

| Command | Description |
| --- | --- |
| `docker network ls` | List networks |
| `docker network create app-net` | Create network |
| `docker network inspect app-net` | Inspect network |
| `docker network connect app-net container` | Connect container |
| `docker network rm app-net` | Remove network |

### Compose Commands

| Command | Description |
| --- | --- |
| `docker compose up` | Start services |
| `docker compose up -d` | Start in background |
| `docker compose down` | Stop and remove services |
| `docker compose ps` | List services |
| `docker compose logs` | View logs |
| `docker compose build` | Build services |
| `docker compose restart` | Restart services |
| `docker compose down -v` | Remove services and volumes |

### Cleanup Commands

| Command | Description | Example |
|---|---|---|
| **Remove Stopped Containers** | Removes all stopped containers from the system. | `docker container prune` |
| **Remove Unused Images** | Removes unused Docker images that are not referenced by existing containers. | `docker image prune` |
| **Remove Unused Networks** | Removes unused Docker networks that are not connected to any containers. | `docker network prune` |
| **Remove Unused Volumes** | Removes unused Docker volumes that are not referenced by any containers. | `docker volume prune` |
| **Remove Unused Docker Objects** | Removes unused containers, networks, dangling images, and build cache. | `docker system prune` |

**Warning:** Cleanup commands can delete resources you may still need. Review their impact before executing them, especially on production machines.

---

> ## 22. Practical Hands-on Exercises

The best way to understand Docker is through practice.

### Exercise 1: Create a Custom Docker Image

Tasks:

1. Create a Python application.
2. Write a Dockerfile.
3. Build an image.
4. Run a container.
5. Access the application through a browser.

### Exercise 2: Persistent Storage

Tasks:

1. Create a Docker volume.
2. Start a container using that volume.
3. Write a file inside the mounted directory.
4. Remove the container.
5. Create another container using the same volume.
6. Verify that the file still exists.

Commands:

```bash
docker volume create my-volume
```

```bash
docker run --name test1 \
  -v my-volume:/data \
  busybox sh -c "echo Hello Docker > /data/test.txt"
```

```bash
docker rm test1
```

```bash
docker run --rm \
  -v my-volume:/data \
  busybox cat /data/test.txt
```

Expected output:

```text
Hello Docker
```

### Exercise 3: Container Networking

Tasks:

1. Create a user-defined bridge network.
2. Run an Nginx container.
3. Run another container in the same network.
4. Test name-based communication.

### Exercise 4: Docker Compose

Tasks:

1. Create a Python application.
2. Create a Dockerfile.
3. Create a Compose file.
4. Add a PostgreSQL service.
5. Add persistent database storage.
6. Start both services using Compose.
7. Inspect the running services.
8. Stop the application.

### Exercise 5: Docker Image Optimization

Tasks:

1. Build an application image.
2. Check its size.
3. Optimize the Dockerfile.
4. Add a `.dockerignore`.
5. Rebuild the image.
6. Compare the results.

---

> ## 23. Common Docker Interview Questions

### Beginner-Level Questions

#### 1. What is Docker?

Docker is a containerization platform that packages applications and their dependencies into portable containers.

#### 2. What is a Docker Image?

A Docker image is an immutable template used to create containers.

#### 3. What is a Docker Container?

A container is an isolated running or stopped instance of an image.

#### 4. What is a Dockerfile?

A Dockerfile contains instructions used to build Docker images.

#### 5. What is Docker Hub?

Docker Hub is a cloud-based container registry used to store and distribute images.

#### 6. What is the difference between an image and a container?

An image is the template, whereas a container is an instance created from that image.

#### 7. What is Docker Compose?

Docker Compose is used to define and manage multi-container applications through YAML configuration.

#### 8. What is a Docker Volume?

A Docker volume provides persistent storage that exists independently of a container's writable layer.

### Intermediate-Level Questions

#### 9. What is the difference between CMD and ENTRYPOINT?

- CMD provides the default command or default arguments.
- ENTRYPOINT configures the main executable.
- They can be combined to create a configurable container command.

#### 10. What is the difference between COPY and ADD?

- COPY copies files and directories.
- ADD supports additional functionality such as extracting certain local tar archives and handling remote URLs.

COPY is generally preferred for simple file copying.

#### 11. What is the difference between RUN and CMD?

- RUN executes commands during image building.
- CMD specifies the default command or arguments when a container starts.

#### 12. What is a Docker Layer?

A Docker image layer is a filesystem change or reusable image component that contributes to the final image.

#### 13. What is Docker Layer Caching?

Docker reuses previously built layers when the relevant build instructions and inputs have not changed.

#### 14. What is a Bind Mount?

A bind mount maps a specific host filesystem path into a container.

#### 15. What is the difference between EXPOSE and -p?

- EXPOSE documents an expected container port.
- `-p` publishes a container port through the host.

#### 16. What is a Docker Network?

A Docker network provides communication between containers and other network endpoints.

#### 17. Why should containers not depend on localhost to communicate with each other?

Because localhost inside a container refers to that container itself.

Containers on the same suitable network can communicate using their service or container names.

#### 18. What is a multi-stage build?

A multi-stage build uses multiple `FROM` instructions to separate build-time dependencies from the final runtime image.

#### 19. What is a restart policy?

A restart policy controls whether Docker attempts to restart a container after it exits.

#### 20. How do you persist data in Docker?

By using Docker volumes or bind mounts.

---

> ## 24. Quick Revision Cheat Sheet

### Docker Core Concepts

- Docker = Containerization platform.
- Image = Read-only application template.
- Container = Image instance.
- Dockerfile = Image build instructions.
- Docker Daemon = Background service managing Docker objects.
- Docker Client = Interface for Docker commands.
- Docker Registry = Image storage and distribution.
- Docker Volume = Persistent storage.
- Docker Network = Container communication.
- Docker Compose = Multi-container application management.

### Important Commands

```bash
# Version
docker --version
docker version

# Images
docker pull nginx
docker images
docker build -t myapp .
docker rmi nginx

# Containers
docker run -d -p 8080:80 nginx
docker ps
docker ps -a
docker stop container
docker start container
docker restart container
docker rm container

# Logs and debugging
docker logs container
docker logs -f container
docker inspect container
docker exec -it container sh
docker stats

# Volumes
docker volume ls
docker volume create mydata
docker volume inspect mydata

# Networks
docker network ls
docker network create mynetwork
docker network inspect mynetwork

# Docker Compose
docker compose up -d
docker compose ps
docker compose logs -f
docker compose down
```

---

> ## 25. Learning Roadmap

After completing Docker fundamentals, follow this learning sequence:

1. **Docker Fundamentals**
   - Images
   - Containers
   - Dockerfile
   - Volumes
   - Networking
2. **Advanced Docker**
   - Multi-stage builds
   - BuildKit
   - Image optimization
   - Health checks
   - Resource constraints
   - Container security
3. **Docker Compose**
   - Multi-container applications
   - Service dependencies
   - Networks
   - Volumes
   - Environment configurations
4. **Container Orchestration**
   - Docker Swarm
   - Kubernetes
   - Pods
   - Deployments
   - Services
   - ConfigMaps
   - Secrets
5. **DevOps Integration**
   - Docker in CI/CD
   - GitHub Actions
   - Jenkins
   - Container registries
   - Image scanning
   - Automated deployments

---

> ## Conclusion

Docker simplifies application packaging, distribution, and deployment by providing a consistent containerized environment.

> The most important concepts to understand thoroughly are:
> 
> - Docker Architecture
> - Images vs Containers
> - Dockerfile Instructions
> - Image Layers and Caching
> - Volumes and Persistent Storage
> - Docker Networking
> - Docker Compose
> - Container Lifecycle
> - Security and Best Practices
> 

Once these fundamentals are clear, learning Kubernetes, container orchestration, and container-based CI/CD pipelines becomes significantly easier.

**Remember:** Do not just memorize Docker commands. Understand what happens internally when you build an image, create a container, configure networking, mount storage, and deploy an application.
