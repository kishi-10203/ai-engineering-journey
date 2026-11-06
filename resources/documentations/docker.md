# Docker Fundamentals

> ## Contents
> 
> 1. Introduction to Docker
> 2. Why Docker?
> 3. Virtual Machines vs Containers
> 4. Docker Architecture
> 5. Important Docker Terminologies
> 6. Docker Installation
> 7. Understanding Docker Images
> 8. Understanding Docker Containers
> 9. Dockerfile Fundamentals
> 10. Docker Image Layers and Caching
> 11. Docker Volumes and Storage
> 12. Docker Networking
> 13. Docker Registry and Docker Hub
> 14. Docker Compose
> 15. Docker Environment Variables
> 16. Docker Container Lifecycle
> 17. Docker Resource Management
> 18. Docker Logs and Debugging
> 19. Docker Security Fundamentals
> 20. Docker Best Practices
> 21. Frequently Used Docker Commands
> 22. Practical Hands-on Exercises
> 23. Common Docker Interview Questions
> 24. Quick Revision Cheat Sheet
> 

---

> ## 1. Introduction to Docker

### 1.1 What is Docker?

Docker is an open-source containerization platform used to build, package, distribute, and run applications inside isolated environments called containers.

Docker allows developers to package an application along with all its dependencies, libraries, configuration files, and runtime requirements into a single portable unit.

This portable unit is called a **Docker Image**.

A running instance of a Docker image is called a **Docker Container**.

#### Simple Definition

Docker is a platform that enables applications to run consistently across different environments by packaging the application and its dependencies into containers.

#### Example

Consider an application developed using Python.

The application requires:

- Python 3.12
- Flask framework
- PostgreSQL client libraries
- Application source code
- Environment configurations

Without Docker, these dependencies must be installed and configured manually on every machine.

With Docker, we package everything into a Docker image.

The same image can then run on:

- Developer's laptop
- Testing environment
- Staging environment
- Production server
- Cloud platforms

This ensures consistency across environments.

### 1.2 Why Was Docker Introduced?

Before containerization became popular, developers frequently faced environment-related issues.

A common problem was:

"It works on my machine, but it doesn't work on the server."

This happened because different environments had different:

- Operating system versions
- Programming language versions
- Installed dependencies
- Library versions
- Environment variables
- System configurations

Docker solves this problem by providing a standardized application environment.

### 1.3 Benefits of Docker

1. **Portability:** Containers can run consistently across compatible Docker environments.
2. **Isolation:** Applications run in isolated containers.
3. **Lightweight:** Containers share the host operating system kernel.
4. **Scalability:** Multiple container instances can be created easily.
5. **Faster Deployment:** Applications can be packaged and deployed consistently.
6. **Resource Efficiency:** Containers generally consume fewer resources than full virtual machines.
7. **Environment Consistency:** Development, testing, and production environments can use the same image.
8. **Version Control:** Docker images can be tagged and versioned.

---

> ## 2. Virtual Machines vs Containers

Before understanding Docker architecture, we must understand the difference between Virtual Machines and Containers.

### 2.1 What is a Virtual Machine?

A Virtual Machine (VM) is a software-based computer that runs an operating system on top of virtualized hardware.

A hypervisor is responsible for creating and managing virtual machines.

Examples:

- VMware
- VirtualBox
- Microsoft Hyper-V

Each virtual machine generally contains:

- Guest Operating System
- System Libraries
- Application Dependencies
- Application

### 2.2 What is a Container?

A container is an isolated process that runs an application using the host operating system kernel.

Containers package:

- Application code
- Runtime
- Libraries
- Dependencies
- Configuration

Unlike virtual machines, containers do not require a separate guest operating system kernel.

### 2.3 VM vs Container Architecture

#### Virtual Machine Architecture

Hardware → Host OS → Hypervisor → Guest OS → Application

#### Container Architecture

Hardware → Host OS → Container Runtime → Containers → Applications

### 2.4 Difference Between VM and Container

| Feature | Virtual Machine | Container |
| --- | --- | --- |
| Virtualization | Hardware-level | OS-level |
| Operating System | Separate guest OS | Shares host kernel |
| Size | Generally large | Generally smaller |
| Startup | Usually slower | Usually faster |
| Resource Usage | Higher | Lower |
| Isolation | Strong OS boundary | Process-level isolation |
| Portability | More heavyweight | Highly portable |
| Example | VMware VM | Docker Container |

> **Important:** Containers are lightweight, but they are not equivalent to virtual machines in terms of security isolation.

---

> ## 3. Docker Architecture

Docker follows a client-server architecture.

Its major components are:

1. Docker Client
2. Docker Daemon
3. Docker Host
4. Docker Registry
5. Docker Objects

### 3.1 Docker Client

The Docker Client is the interface through which users interact with Docker.

It accepts commands and communicates with the Docker daemon through the Docker API.

Example:

```bash
docker run nginx
docker ps
docker images
docker pull ubuntu
```

The Docker CLI is commonly used as the client.

### 3.2 Docker Daemon (dockerd)

The Docker Daemon is a background service responsible for managing Docker objects.

Its responsibilities include:

- Building Docker images
- Creating containers
- Running containers
- Managing networks
- Managing volumes
- Communicating with registries

The daemon process is generally called `dockerd`.

### 3.3 Docker Host

The Docker Host is the machine where Docker runs.

It contains the Docker daemon and the resources required to run containers.

### 3.4 Docker Registry

A Docker Registry stores and distributes Docker images.

Examples:

- Docker Hub
- Amazon Elastic Container Registry (ECR)
- GitHub Container Registry (GHCR)
- Google Artifact Registry
- Azure Container Registry (ACR)

### 3.5 Docker Architecture Workflow

When we execute:

```bash
docker run nginx
```

The following process takes place:

1. Docker CLI receives the command.
2. Docker Client communicates with Docker Daemon.
3. Docker Daemon checks whether the requested image exists locally.
4. If the image does not exist, Docker pulls it from a registry.
5. Docker creates a container using the image.
6. Docker starts the container.
7. The application inside the container starts running.

> **Important:** Docker Client and Docker Daemon are separate architectural components, even when they run on the same machine.

---

> ## 4. Important Docker Terminologies

Understanding these terms is essential before working with Docker.

| Docker Concept | Definition | Example / Purpose |
|---|---|---|
| **Docker Image** | A read-only template used to create containers. | Contains base OS files, application code, dependencies, libraries, runtime, and configuration. Example: `docker pull nginx` downloads the Nginx image. |
| **Docker Container** | A running or stopped instance created from a Docker image. | Example: `docker run nginx` creates and starts a container using the Nginx image. |
| **Dockerfile** | A text file containing instructions used to build a Docker image. | Defines instructions such as `FROM`, `WORKDIR`, `COPY`, `RUN`, and `CMD`. |
| **Docker Volume** | A mechanism used to persist data outside a container's writable layer. | Allows data to survive container deletion and recreation. |
| **Docker Network** | A mechanism that allows containers to communicate with one another and with external systems. | Enables communication between frontend, backend, and database containers. |
| **Docker Compose** | A tool used to define and manage multi-container applications through a YAML configuration file. | Allows us to define frontend, backend, and database services in one file. |
| **Docker Registry** | A storage and distribution system for Docker images. | Used to store and distribute Docker images. |
| **Docker Repository** | A collection of related images, commonly organized by application or project. | Examples: `nginx`, `ubuntu`, `python`, `redis`, `postgres`. |
| **Docker Tag** | Used to identify a particular version or variant of an image. | Examples: `nginx:latest`, `nginx:1.27`, `python:3.12-slim`, `ubuntu:24.04`. |

---

> ## 5. Understanding Docker Images

### 5.1 What is a Docker Image?

A Docker image is an immutable, layered filesystem template that contains everything required to run an application.

Images are generally built using a Dockerfile.

| Docker Command | Description | Example |
|---|---|---|
| **Pull an Image** | Downloads a Docker image from the configured registry. | `docker pull ubuntu` |
| **Pull a Specific Version** | Downloads a specific version of a Docker image. | `docker pull ubuntu:24.04` |
| **List Docker Images** | Displays all locally available Docker images. | `docker images` or `docker image ls` |
| **Inspect an Image** | Displays detailed metadata about an image or container, depending on the object supplied. | `docker inspect nginx` |
| **Inspect an Image Specifically** | Displays detailed metadata of a Docker image. | `docker image inspect nginx` |
| **Remove an Image** | Removes a Docker image from the local system. | `docker rmi nginx` or `docker image rm nginx` |

> **Remember :** An image cannot normally be removed if an existing container depends on it.

### 5.2 Image Naming Convention

Docker image names generally follow this structure:

```text
registry/namespace/repository:tag
```

Example:

```text
docker.io/library/nginx:latest
```

Breakdown:

- Registry: docker.io
- Namespace: library
- Repository: nginx
- Tag: latest

### 5.3 What is the Latest Tag?

The `latest` tag is simply a conventional tag name.

It does not guarantee that the image contains the newest software version.

For production deployments, prefer explicit version tags or immutable image digests.

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

## 19. Docker Security Fundamentals

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

## Conclusion

Docker simplifies application packaging, distribution, and deployment by providing a consistent containerized environment.

The most important concepts to understand thoroughly are:

- Docker Architecture
- Images vs Containers
- Dockerfile Instructions
- Image Layers and Caching
- Volumes and Persistent Storage
- Docker Networking
- Docker Compose
- Container Lifecycle
- Security and Best Practices

Once these fundamentals are clear, learning Kubernetes, container orchestration, and container-based CI/CD pipelines becomes significantly easier.

**Remember:** Do not just memorize Docker commands. Understand what happens internally when you build an image, create a container, configure networking, mount storage, and deploy an application.
