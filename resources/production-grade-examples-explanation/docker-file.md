# Production-Grade Dockerfile: Complete Explanation and Advanced Concepts

## Table of contents

1.  [What this Dockerfile builds](#1-what-this-dockerfile-builds)
2.  [Project structure and sample
    application](#2-project-structure-and-sample-application)
3.  [The complete supplied
    Dockerfile](#3-the-complete-supplied-dockerfile)
4.  [Dockerfile explained line by
    line](#4-dockerfile-explained-line-by-line)
5.  [Build, run, inspect, and test](#5-build-run-inspect-and-test)
6.  [Advanced concepts missing from the
    example](#6-advanced-concepts-missing-from-the-example)
7.  [A stronger production-oriented
    Dockerfile](#7-a-stronger-production-oriented-dockerfile)
8.  [Docker Compose: run the API with PostgreSQL and
    Redis](#8-docker-compose-run-the-api-with-postgresql-and-redis)
9.  [Production deployment
    checklist](#9-production-deployment-checklist)
10. [Practical exercises](#10-practical-exercises)
11. [Knowledge assessment](#11-knowledge-assessment)

------------------------------------------------------------------------

# 1. What this Dockerfile builds

The Dockerfile builds a container image for a Python web
application. The application is served by **Uvicorn**, an ASGI server,
and uses **FastAPI** as its web framework.

At a high level:

``` text
Browser / API client
        |
        | HTTP request
        v
Host port 8000
        |
        | Docker port mapping
        v
Container port 8000
        |
        v
Uvicorn ASGI server
        |
        v
FastAPI application
        |
        v
Python routes and business logic
```

The Dockerfile uses a **multi-stage build**:

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

> The final image is based on the last stage; the builder filesystem is not automatically included.

## 1.1 Important vocabulary

| Term           | Meaning |
|----------------|---------|
| Build context  | Files made available to Docker during a build, usually the directory passed to `docker build`. |
| Layer          | An image filesystem change associated with a build instruction. |
| Build stage    | A stage that starts with a `FROM` instruction. |
| Registry       | A service that stores and distributes images, such as Docker Hub or Amazon ECR. |
| Runtime        | The environment and processes used when the container runs. |
| Artifact       | A build output that is transferred to a later stage, such as a wheel file or compiled binary. |

## 1.2 Single Stage Build vs Multi Stage Build

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

## 1.3 Multi-Stage Build

Multi-stage build generally consists of two main stages:

**Builder Phase** – Responsible for preparing the application and its dependencies.

**Runner (Runtime) Phase** – Responsible for running the application in a clean, optimized production environment.

> The main objective is to keep build-time tools and unnecessary dependencies out of the final production Docker image.

### 1.3.1 Builder Phase
The Builder Phase is responsible for preparing everything required to run the application.

#### Functions of Builder Phase

1. **Base Image Selection:** Pulls the required base image, such as Python, Node.js, or Java.

2. **Install Build Dependencies:** Installs compilers, development libraries, and other tools required to build the application.

3. **Read Dependency Files:** Reads files such as requirements.txt, package.json, or pom.xml to identify application dependencies.

4. **Download and Build Packages:** Downloads dependencies and compiles packages if required.

5. **Generate Artifacts:** Produces the necessary build artifacts, such as Python wheel files (.whl), Java JAR files, or compiled application binaries.

6. **Prepare Artifacts for Transfer:** Stores the generated artifacts in a directory so they can be copied into the next stage.


#### Output of Builder Phase 

```text
/wheels/
    fastapi.whl
    uvicorn.whl
    sqlalchemy.whl
    other dependencies.whl
```


### 1.3.2 Runner - Runtime Phase 
The Runner Phase is responsible for executing the application.

It starts with a fresh, clean base image and takes only the required artifacts from the builder phase.

#### Functions of the Runner Phase

1. **Fresh Base Image:** Starts with a clean and lightweight base image, such as python:3.12-slim.

2. **Copy Build Artifacts:** Copies only the required artifacts from the builder phase using COPY --from=builder.

3. **Install Runtime Dependencies:** Installs the required application dependencies using the copied artifacts.

4. **Copy Application Source Code:** Copies the application code, configuration files, and other required resources.

5. **Configure Security:** Creates a non-root user and runs the application with minimum required privileges.

6. **Configure Health Checks:** Defines health checks to monitor whether the application is responding correctly.

7. **Start the Application:** Executes the application using a server such as Uvicorn for FastAPI.


#### Output of the Runner Phase 
```text 
A final production-ready Docker image containing:
* Python runtime.
* Required application dependencies.
* Application source code.
* Runtime configuration.
* Non-root user configuration.
* Application startup command.
```

------------------------------------------------------------------------

# 2. Project structure and sample application

Create this project:

``` text
python-production-api/
├── Dockerfile
├── .dockerignore
├── requirements.txt
└── app/
    ├── __init__.py
    └── main.py
```

## 2.1 requirements.txt

For this learning exercise, use pinned direct dependencies:

``` text
fastapi==0.115.12
uvicorn[standard]==0.34.2
```

These versions are illustrative. For an actual deployment, select
currently supported versions, test them, and lock the complete
dependency graph, including transitive dependencies.

## 2.2 app/main.py

``` python
from datetime import datetime, timezone

from fastapi import FastAPI

app = FastAPI(
    title="Production Learning API",
    version="1.0.0",
)


@app.get("/")
async def root():
    return {
        "message": "Application is running",
        "environment": "production",
    }


@app.get("/health/live")
async def liveness():
    return {"status": "alive"}


@app.get("/health/ready")
async def readiness():
    # In a real service, check required dependencies here,
    # for example a database connection.
    return {"status": "ready"}


@app.get("/api/users")
async def get_users():
    return {
        "users": [
            {"id": 1, "name": "John"},
            {"id": 2, "name": "Alex"},
        ]
    }
```

The sample endpoints are:

| Endpoint       | Purpose |
|----------------|---------|
| `/`            | Basic application response |
| `/health/live` | Basic process/application liveness check |
| `/health/ready`| Readiness check; expand it to validate required dependencies |
| `/api/users`   | Example business endpoint |
| `/docs`        | FastAPI interactive API documentation |

A production readiness check should not simply return success
unconditionally. It should verify that the application can safely accept
traffic. Avoid making liveness depend on every external service: a
temporary database outage should not necessarily trigger endless
application restarts.

------------------------------------------------------------------------

# 3. The complete supplied Dockerfile

This is the Dockerfile being explained:

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

# Set working directory
WORKDIR /build

# Upgrade pip and install dependencies
COPY requirements.txt .

RUN python -m pip install --upgrade pip && \
    pip wheel --no-cache-dir \
    --wheel-dir /wheels \
    -r requirements.txt


# ============================================================
# STAGE 2: PRODUCTION RUNTIME STAGE
# ============================================================

FROM python:3.12-slim AS runtime

# Python runtime configuration
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    APP_ENV=production \
    PORT=8000

# Set application directory
WORKDIR /app

# Create a dedicated non-root user
RUN groupadd --system app && \
    useradd --system \
    --gid app \
    --home-dir /app \
    --no-create-home \
    app

# Copy built dependencies from builder stage
COPY --from=builder /wheels /wheels
COPY requirements.txt .

# Install dependencies without accessing package repositories
RUN python -m pip install \
    --no-cache-dir \
    --no-index \
    --find-links=/wheels \
    -r requirements.txt && \
    rm -rf /wheels

# Copy application source code
COPY --chown=app:app app/ ./app/

# Change ownership of the application directory
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
    CMD ["python", "-c", "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health/live', timeout=3)"]

# Graceful termination signal
STOPSIGNAL SIGTERM

# Start application
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]
```

The health-check URL above uses `/health/live`, matching the sample
application. If retaining the original `/health` route, keep the
Dockerfile and application route consistent.

------------------------------------------------------------------------

# 4. Dockerfile explained line by line

## 4.1 Dockerfile syntax directive

``` dockerfile
# syntax=docker/dockerfile:1
```

This selects the Dockerfile frontend syntax version. It enables modern
Dockerfile syntax and BuildKit features.

It is a parser directive, not a command executed inside the container.
Ordinary comments also begin with `#`.

## 4.2 `FROM`: choose a base image and begin a stage

``` dockerfile
FROM python:3.12-slim AS builder
```

-   `FROM` starts a build stage and selects its base image.
-   `python` is the image repository.
-   `3.12` selects the Python 3.12 image line.
-   `slim` selects a reduced Debian-based variant.
-   `AS builder` names this stage so later instructions can refer to it.

The Python base image already includes the Python interpreter and
supporting userspace. You do not need to install Python manually.

### Why `slim`?

It is smaller than the standard Python image and is often a practical
starting point for production. It may omit system libraries and
compilers needed by packages that compile native extensions.

Alpine is not automatically smaller or better for every Python
application. Alpine uses musl libc rather than Debian's glibc, and some
packages have compatibility or compilation complications.

### What does `AS builder` mean?

It gives the stage a stable name. Later:

``` dockerfile
COPY --from=builder /wheels /wheels
```

copies files from that named stage.

Every new `FROM` begins a new stage with its own filesystem. Files are
not shared automatically between stages.

## 4.3 `ENV`: configure Python

``` dockerfile
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
```

`ENV` sets environment variables in the image. They are available to
later build instructions and to processes in containers created from the
image, unless overridden.

### `PYTHONDONTWRITEBYTECODE=1`

Python normally writes imported-module bytecode files, usually under
`__pycache__`, as `.pyc` files.

Setting this variable prevents Python from writing those files. It does
not disable Python's in-memory execution or compilation.

Why use it?

-   Avoids bytecode files being written into the container filesystem.
-   Helps keep an immutable runtime filesystem cleaner.
-   Can be useful when the application directory is read-only.

It is a policy choice, not a major performance optimization.

### `PYTHONUNBUFFERED=1`

Python may buffer stdout and stderr. In a container, logs are commonly
collected from these streams by Docker or the platform.

Unbuffered output makes log messages appear promptly, which helps when
diagnosing failures.

## 4.4 `WORKDIR`: set the current directory

``` dockerfile
WORKDIR /build
```

This sets the working directory for subsequent instructions and the
default working directory for the container, unless a later `WORKDIR`
changes it.

If the directory does not exist, Docker creates it.

It is conceptually similar to:

``` bash
cd /build
```

but it is a Dockerfile instruction that persists as image configuration.

## 4.5 `COPY`: add the dependency manifest

``` dockerfile
COPY requirements.txt .
```

General syntax:

``` dockerfile
COPY <source> <destination>
```

The source is taken from the build context. The destination is inside
the image.

Because the current working directory is `/build`, the destination `.`
means `/build`.

Result:

``` text
/build/requirements.txt
```

### Why copy requirements separately?

Docker can reuse cached build layers when the inputs to those layers
have not changed.

If application source code changes but `requirements.txt` remains
unchanged, Docker may reuse the dependency preparation layer instead of
repeating it.

This is a fundamental Docker build optimization: copy files that change
infrequently before files that change frequently.

## 4.6 `RUN`: execute build-time commands

``` dockerfile
RUN python -m pip install --upgrade pip && \
    pip wheel --no-cache-dir \
    --wheel-dir /wheels \
    -r requirements.txt
```

`RUN` executes a command during image construction. Its filesystem
changes become part of an image layer.

The backslash continues the shell command onto the next Dockerfile line.
`&&` runs the next command only if the previous command succeeds.

### `python -m pip`

Runs pip through the selected Python interpreter. This reduces ambiguity
when multiple Python installations or environments exist.

### `pip install --upgrade pip`

Upgrades pip in the builder stage. This affects the builder's pip; it
does not automatically upgrade pip in the separate runtime stage.

For reproducible builds, consider pinning the build tooling version
rather than always installing whichever pip version is newest at build
time.

### `pip wheel`

Downloads or builds wheel distributions for the dependencies in
`requirements.txt`.

A wheel is a built Python package distribution with the `.whl`
extension. It can be installed without repeating the package's source
build process, provided it is compatible with the target Python version,
operating system, architecture, and ABI.

### `--no-cache-dir`

Disables pip's local package cache. It helps avoid retaining an
additional pip cache in the builder layer.

### `--wheel-dir /wheels`

Stores the resulting wheel files in `/wheels`.

### `-r requirements.txt`

Reads the requested dependencies from the requirements file.

### Native dependency caveat

If a dependency has no compatible wheel, pip may need to build it from
source. The builder may then require compilers, headers, and system
development libraries.

The supplied builder does not install those tools. Add only the build
dependencies that your selected packages actually need. If a compiled
package also needs shared libraries at runtime, install the required
runtime libraries in the final stage.

## 4.7 Start the runtime stage

``` dockerfile
FROM python:3.12-slim AS runtime
```

This starts a fresh stage from the Python base image.

The builder's installed files are not inherited. Only files explicitly
copied from the builder are transferred.

This is the key to multi-stage builds: the final stage can omit build
tools and temporary build artifacts.

## 4.8 Runtime environment configuration

``` dockerfile
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    APP_ENV=production \
    PORT=8000
```

Multiple variables can be declared in one `ENV` instruction.

| Variable                        | Purpose |
|---------------------------------|---------|
| `PYTHONDONTWRITEBYTECODE`       | Prevents Python from writing `.pyc` files. |
| `PYTHONUNBUFFERED`              | Makes Python stdout/stderr output promptly visible. |
| `PIP_NO_CACHE_DIR`              | Disables pip's cache. |
| `PIP_DISABLE_PIP_VERSION_CHECK` | Suppresses pip's periodic version-check notification. |
| `APP_ENV`                       | Custom application environment setting. |
| `PORT`                          | Custom default port setting. |

Docker does not give `APP_ENV` or `PORT` special application behaviour.
Your code or startup command must read them.

**Important detail:** The supplied `CMD` hardcodes port 8000. Therefore,
setting `PORT=9000` alone will not change Uvicorn's listening port. A
configurable entrypoint is shown in the improved example later.

## 4.9 Create a dedicated Linux user

``` dockerfile
RUN groupadd --system app && \
    useradd --system \
    --gid app \
    --home-dir /app \
    --no-create-home \
    app
```

By default, many images execute as root unless the Dockerfile or runtime
configuration selects another user.

Running a network-facing application as root increases the potential
impact of an application compromise.

This command:

1.  Creates a system group named `app`.
2.  Creates a system user named `app`.
3.  Assigns the user to the `app` group using `--gid app`.
4.  Sets `/app` as the configured home directory.
5.  Uses `--no-create-home` to avoid creating a separate home directory.
6.  Names the user `app`.

The principle is **least privilege**: give the application only the
permissions it needs.

Non-root execution is important, but it is only one layer of container
security.

## 4.10 Copy wheel artifacts from the builder

``` dockerfile
COPY --from=builder /wheels /wheels
```

This is cross-stage copying.

-   `--from=builder` identifies the source stage.
-   The first `/wheels` is the source path in that stage.
-   The second `/wheels` is the destination path in the runtime stage.

The runtime stage now has the prepared wheel files, but it still has not
installed them.

## 4.11 Copy the dependency manifest into runtime

``` dockerfile
COPY requirements.txt .
```

This copies the manifest to `/app/requirements.txt`, because the runtime
working directory is `/app`.

Pip will use this manifest to install the local wheels.

## 4.12 Install dependencies offline and remove temporary artifacts

``` dockerfile
RUN python -m pip install \
    --no-cache-dir \
    --no-index \
    --find-links=/wheels \
    -r requirements.txt && \
    rm -rf /wheels
```

Breakdown:

| Argument or command          | Meaning |
|------------------------------|---------|
| `python -m pip install`       | Install packages into the runtime Python environment. |
| `--no-cache-dir`              | Do not retain pip's cache. |
| `--no-index`                  | Do not query package indexes such as PyPI. |
| `--find-links=/wheels`        | Look for distributions in the local wheel directory. |
| `-r requirements.txt`         | Install the requested requirements. |
| `rm -rf /wheels`              | Delete the temporary wheel directory after successful installation. |

The `&&` is important: the cleanup runs only if installation succeeds.

Because installation and deletion are in the same `RUN`, the wheel files
do not remain in a previous runtime-stage layer. This is better than
copying wheels in one layer and deleting them in a later layer, because
deleting in a later layer does not erase the bytes from the earlier
immutable layer.

### Does `--no-index` make the build fully reproducible?

No. It avoids package-index access in this installation step, but
reproducibility also depends on:

-   Fully pinned direct and transitive dependencies.
-   Package hashes where appropriate.
-   Compatible wheels for the target platform.
-   A controlled base image.
-   Controlled build tooling.
-   Controlled external build inputs.

## 4.13 Copy the application source with ownership

``` dockerfile
COPY --chown=app:app app/ ./app/
```

This copies the local `app/` directory to `/app/app/`.

`--chown=app:app` sets the copied files' owner and group to the non-root
application user.

The resulting structure is:

``` text
/app/
├── requirements.txt
└── app/
    ├── __init__.py
    └── main.py
```

This is useful because the application runs as `app`, not root.

## 4.14 Recursive ownership change

``` dockerfile
RUN chown -R app:app /app
```

This recursively changes ownership of `/app`.

In the supplied example, this is largely redundant for the application
files because `COPY --chown=app:app` already sets their ownership.

The requirements file can remain root-owned if the application only
needs to read it. It is generally better not to make every file writable
by the application user without a reason.

A cleaner Dockerfile can omit this recursive `chown`, or explicitly
assign ownership only to directories that need runtime writes.

## 4.15 Switch to the non-root user

``` dockerfile
USER app
```

This sets the default user for subsequent build instructions and for the
container's default process.

The Uvicorn process started by `CMD` therefore runs as `app`.

Verify it with:

``` bash
docker exec -it my-python-api whoami
docker exec -it my-python-api id
```

Expected username:

``` text
app
```

## 4.16 Document the application port

``` dockerfile
EXPOSE 8000
```

`EXPOSE` documents the port that the application is expected to listen
on.

It does **not**:

-   Start the server.
-   Publish the port to the host.
-   Open a host firewall.
-   Automatically make the application publicly accessible.

To publish the port, use:

``` bash
docker run -p 8000:8000 python-production-api:1.0
```

The syntax is:

``` text
-p HOST_PORT:CONTAINER_PORT
```

For example:

``` bash
docker run -p 9000:8000 python-production-api:1.0
```

The host listens on port 9000 and forwards traffic to container port
8000.

## 4.17 Health check

``` dockerfile
HEALTHCHECK --interval=30s \
    --timeout=5s \
    --start-period=10s \
    --retries=3 \
    CMD ["python", "-c", "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health/live', timeout=3)"]
```

A running container is not necessarily a healthy application. The health
check periodically executes a command and records whether it succeeds.

 | Setting            | Meaning |
|--------------------|---------|
| `--interval=30s`   | Run checks approximately every 30 seconds. |
| `--timeout=5s`     | A check that takes longer than five seconds is considered failed. |
| `--start-period=10s` | Allows an initial startup grace period. |
| `--retries=3`      | Consecutive failures after the start period can mark the container unhealthy. |
| `CMD [...]`        | The command Docker executes for each check. |

The command uses Python's standard-library HTTP client to call the
application's loopback address.

`127.0.0.1` is appropriate because the check runs inside the container
and calls the application in that same container.

If the endpoint responds with a successful HTTP status, the command
exits successfully. HTTP errors and connection errors cause the check to
fail.

Inspect status:

``` bash
docker ps
docker inspect --format='{{json .State.Health}}' my-python-api
```

### Health-check limitation

Docker marks a container as `healthy` or `unhealthy`; it does not
automatically restart an unhealthy container just because its health
check fails. A deployment platform or operator must decide how to react.

Also, health-check design matters:

-   **Liveness:** Is the process/application functioning?
-   **Readiness:** Can it safely receive traffic?
-   **Startup:** Has initialization completed?

A database outage should not automatically make a process liveness check
fail if restarting the application will not fix the database outage.

## 4.18 Graceful termination signal

``` dockerfile
STOPSIGNAL SIGTERM
```

This sets the signal Docker sends to the container's main process when
stopping it.

`SIGTERM` means that the process is being asked to terminate.

A well-behaved server should stop accepting new work, finish or safely
cancel in-flight requests, close resources, and exit within the
configured shutdown grace period.

Docker normally uses `SIGTERM` by default for Linux containers, so this
instruction is explicit documentation of the intended behaviour rather
than a major change from the default.

Uvicorn supports graceful shutdown. Your application can also use
FastAPI lifespan handlers to initialize and clean up resources such as
connection pools.

## 4.19 Start the application with `CMD`

``` dockerfile
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]
```

`CMD` specifies the default command executed when a container starts.

### Uvicorn

Uvicorn is an ASGI server. ASGI stands for Asynchronous Server Gateway
Interface. It provides the server interface that allows an ASGI
application such as FastAPI to receive and respond to requests.

### `app.main:app`

This identifies the application object:

-   `app.main` is the Python module, corresponding to `app/main.py`.
-   The final `app` is the FastAPI application variable defined inside
    that module.

### `--host 0.0.0.0`

Binds the server to all available IPv4 interfaces inside the container.

If the server binds only to `127.0.0.1`, it will generally not accept
connections forwarded from outside the container.

### `--port 8000`

Tells Uvicorn to listen on container port 8000.

### `--workers 2`

Starts two worker processes.

More workers can improve throughput for some workloads, but each worker
consumes memory and may create its own database connection pool.

Choose the number based on CPU, memory, workload characteristics, and
database capacity. Do not assume two workers is always correct.

For orchestrated environments, one worker per container and horizontal
scaling can be easier to operate.

### Exec form versus shell form

The supplied command uses JSON-array **exec form**.

Preferred:

``` dockerfile
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Shell form:

``` dockerfile
CMD uvicorn app.main:app --host 0.0.0.0 --port 8000
```

Exec form starts the executable directly instead of introducing an
intermediate shell. This generally makes signal delivery and graceful
shutdown more predictable.

------------------------------------------------------------------------

# 5. Build, run, inspect, and test

The following commands work in PowerShell or a terminal with Docker
installed. PowerShell line continuation uses a backtick; in a Linux
shell, use `\` or write the command on one line.

## 5.1 Create `.dockerignore`

``` dockerignore
.git
.gitignore
__pycache__/
*.py[cod]
.pytest_cache/
.venv/
venv/
env/
.vscode/
.idea/
.env
.env.*
!.env.example
*.log
logs/
data/
models/
```

`.dockerignore` excludes matching files from the build context. It
reduces unnecessary transfer and helps prevent local-only files from
being included accidentally.

It is not a substitute for secret management. Never rely on
`.dockerignore` as the only protection for credentials.

## 5.2 Build the image

``` powershell
docker build -t python-production-api:1.0 .
```

  Part                      Meaning
  ------------------------- -----------------------------------------
  `docker build`            Build an image.
  `-t`                      Assign a repository name and tag.
  `python-production-api`   Image name.
  `1.0`                     Image tag.
  `.`                       Current directory is the build context.

For more detailed output:

``` powershell
docker build --progress=plain -t python-production-api:1.0 .
```

## 5.3 Inspect the image

``` powershell
docker image ls
docker image inspect python-production-api:1.0
docker history python-production-api:1.0
```

-   `image ls` lists local images.
-   `image inspect` shows image metadata and configuration.
-   `history` shows the image's layer history.

## 5.4 Run the container

``` powershell
docker run -d `
  --name my-python-api `
  -p 8000:8000 `
  python-production-api:1.0
```

-   `-d`: detached/background mode.
-   `--name`: readable container name.
-   `-p`: publish host port to container port.
-   Final argument: image to run.

## 5.5 Test endpoints

Open these URLs in a browser or call them with an HTTP client:

``` text
http://localhost:8000/
http://localhost:8000/health/live
http://localhost:8000/health/ready
http://localhost:8000/api/users
http://localhost:8000/docs
```

## 5.6 Inspect runtime behaviour

``` powershell
docker ps
docker ps -a
docker logs my-python-api
docker logs -f my-python-api
docker inspect my-python-api
docker stats my-python-api
docker exec -it my-python-api sh
docker exec -it my-python-api whoami
docker stop my-python-api
docker start my-python-api
docker rm my-python-api
```

`ps aux` may not be available in a slim image because the `ps` utility
is not guaranteed to be installed. Do not add diagnostic packages to a
production image just for convenience; use a separate debug image or
inspect `/proc` where appropriate.

------------------------------------------------------------------------

# 6. Advanced concepts missing from the example

The supplied Dockerfile is a good learning foundation, but it is not a
complete production deployment design. The following concepts are absent
or only partially addressed.

## 6.1 Reproducible builds and immutable base-image digests

**Missing:** The base image is selected by a mutable tag:

``` dockerfile
FROM python:3.12-slim
```

Tags can point to updated image content over time. Two builds made on
different dates may therefore use different base-image contents.

**Why it matters:** A production team should be able to identify exactly
which base image was used to build an artifact and reproduce or
investigate that build.

**Improvement:** Pin the base image by digest:

``` dockerfile
FROM python:3.12-slim@sha256:<verified-digest>
```

Obtain the real digest from the registry or trusted build process. Do
not paste an invented digest.

A pinned digest does not mean never updating. Establish a routine for
reviewing and updating the base image to receive security fixes.

## 6.2 Complete dependency locking and package hashes

**Missing:** Pinning only direct dependencies does not necessarily lock
every transitive dependency.

For example, FastAPI depends on other packages. If those indirect
dependencies are not constrained, a later build may resolve different
versions.

**Why it matters:** Dependency changes can introduce bugs,
incompatibilities, or vulnerabilities.

**Improvement:**

-   Generate a complete lock file.
-   Pin direct and transitive dependency versions.
-   Consider package hashes.
-   Use a controlled dependency update process.
-   Test updates before release.

Example installation command for a fully hash-locked requirements file:

``` bash
python -m pip install \
  --no-cache-dir \
  --require-hashes \
  -r requirements.lock
```

Every requirement and transitive dependency must be represented with
compatible hashes for this to work.

## 6.3 BuildKit cache mounts

**Missing:** The example uses normal layer caching, but it does not use
BuildKit cache mounts.

**Why it matters:** During repeated builds, pip may need to download or
build packages again when a relevant layer is invalidated.

A cache mount can preserve package download/build cache across builds
without baking that cache into the final image.

Example:

``` dockerfile
# syntax=docker/dockerfile:1

RUN --mount=type=cache,target=/root/.cache/pip \
    python -m pip wheel \
    --wheel-dir=/wheels \
    -r requirements.txt
```

This is a builder optimization. It is separate from the final runtime
image and should be tested with your build environment.

## 6.4 Build secrets and secret-safe builds

**Missing:** There is no handling for private package repository
credentials or other build-time secrets.

Never write credentials directly into Dockerfile instructions:

``` dockerfile
# BAD: do not do this
ENV PRIVATE_TOKEN=my-secret-token
```

Do not pass secrets through ordinary build arguments if they could be
exposed in build metadata or layers.

With BuildKit, use secret mounts:

``` dockerfile
RUN --mount=type=secret,id=pip_config,target=/etc/pip.conf \
    python -m pip install -r requirements.txt
```

Supply the secret at build time using the appropriate BuildKit secret
option.

Build secrets are for build-time access. Runtime secrets should be
injected by a secure runtime or orchestration secret-management
mechanism.

## 6.5 Native build dependencies and runtime shared libraries

**Missing:** The builder assumes all dependencies can be installed as
available wheels.

Some Python packages contain native extensions and may need:

-   A compiler such as GCC.
-   Python development headers.
-   OS development libraries.
-   A linker.
-   Package-specific build tools.

The runtime may also need shared libraries used by compiled extensions.

**Why it matters:** A package may build successfully in one environment
but fail to import in the final runtime image if required shared
libraries are missing.

**Improvement:** Install compilation tools only in the builder stage.
Install only the required runtime shared libraries in the final stage.

For example, the exact packages depend on your application. A PostgreSQL
driver, image-processing library, and scientific Python package can have
different requirements. Inspect package documentation and test the final
image.

## 6.6 Image vulnerability scanning and software supply-chain security

**Missing:** No image or dependency scanning is performed.

**Why it matters:** Your image contains the OS packages, Python
interpreter, and third-party Python dependencies. Vulnerabilities can
exist in any of these components.

Add scanning to CI/CD. Common tools include:

-   Trivy.
-   Docker Scout.
-   Grype.
-   OS/package vulnerability scanners provided by your cloud platform.

A typical Trivy example:

``` bash
trivy image python-production-api:1.0
```

Define a policy for critical/high findings, exceptions, remediation
deadlines, and rescanning.

Scanning does not prove an image is secure. It helps identify known
vulnerabilities.

## 6.7 Software Bill of Materials (SBOM) and image provenance

**Missing:** The Dockerfile does not generate or publish a software bill
of materials or build provenance.

An SBOM records components included in an artifact. Provenance describes
how an artifact was built and which inputs and processes were involved.

**Why it matters:** These records improve auditing, incident response,
compliance, and supply-chain traceability.

BuildKit and registry tooling can generate or attach provenance and SBOM
attestations. For example, supported Buildx builds can use:

``` bash
docker buildx build \
  --sbom=true \
  --provenance=true \
  -t python-production-api:1.0 .
```

Check your Buildx and registry capabilities and decide how attestations
will be stored and verified.

## 6.8 Runtime secrets and configuration management

**Missing:** The example defines only basic environment variables. It
does not demonstrate secure runtime secret injection.

Do not bake database passwords, API keys, cloud credentials, or private
certificates into an image.

Use an appropriate mechanism such as:

-   Docker Compose secrets for supported local/single-host workflows.
-   Kubernetes Secrets integrated with an external secret manager.
-   AWS Secrets Manager or Systems Manager Parameter Store.
-   A cloud platform's managed secret injection.

Environment variables are convenient for non-sensitive configuration,
but secrets can be exposed through process inspection, diagnostics,
crash reports, or misconfigured logging. Choose the secret delivery
mechanism based on your threat model.

## 6.9 Runtime configuration and twelve-factor configuration principles

**Missing:** `APP_ENV` and `PORT` are declared, but the `CMD` hardcodes
the port.

**Why it matters:** The same image should be deployable to development,
test, staging, and production with configuration supplied at runtime.

Read environment variables in application configuration or a dedicated
entrypoint. Validate required settings at startup. Do not silently fall
back to insecure production defaults.

## 6.10 Resource limits and resource requests

**Missing:** The Dockerfile does not set CPU or memory limits.

Resource controls are generally configured when running the container or
in the orchestration platform, rather than by `EXPOSE` or `ENV`.

Example:

``` bash
docker run -d \
  --name my-python-api \
  --memory=512m \
  --cpus=1.0 \
  -p 8000:8000 \
  python-production-api:1.0
```

These values are examples only. Load-test the application and choose
limits based on actual memory and CPU usage.

In Kubernetes, resource requests and limits are specified in the
workload manifest.

## 6.11 Read-only root filesystem and writable paths

**Missing:** The container's root filesystem is not configured as
read-only.

A read-only root filesystem reduces the places an attacker or
malfunctioning process can modify.

Example:

``` bash
docker run -d \
  --read-only \
  --tmpfs /tmp:rw,noexec,nosuid,size=64m \
  -p 8000:8000 \
  python-production-api:1.0
```

This works only if the application does not need to write elsewhere.
Give explicit writable volumes or temporary filesystems to the paths
that genuinely require writes.

## 6.12 Linux capabilities, seccomp, AppArmor and security profiles

**Missing:** The example uses a non-root user but does not configure
additional runtime security restrictions.

Useful controls include:

-   Dropping unnecessary Linux capabilities.
-   Applying a seccomp profile.
-   Using AppArmor or SELinux where supported.
-   Avoiding privileged containers.
-   Avoiding host PID/network namespaces unless required.
-   Avoiding unnecessary host mounts.

Example:

``` bash
docker run -d \
  --cap-drop=ALL \
  --security-opt=no-new-privileges:true \
  -p 8000:8000 \
  python-production-api:1.0
```

Test these settings against your application and platform. Do not assume
every workload has identical requirements.

## 6.13 Health checks: liveness, readiness and startup

**Partially present:** The Dockerfile has one health check.

A single endpoint may not represent all operational conditions.

Use separate concepts:

-   **Liveness:** Is the application process functioning, such that
    restarting it could help?
-   **Readiness:** Can this instance accept traffic right now?
-   **Startup:** Has initialization completed?

For a database-backed API, readiness may check database connectivity.
Liveness should generally avoid depending on a remote database, because
a database outage could otherwise trigger unnecessary restarts.

Kubernetes has distinct startup, readiness and liveness probes. Docker's
single `HEALTHCHECK` is not a full replacement for those probe types.

## 6.14 Observability: logs, metrics and traces

**Partially present:** `PYTHONUNBUFFERED=1` helps logs appear promptly,
but it does not provide complete observability.

Production services need to answer:

-   What happened?
-   How many requests are failing?
-   How long are requests taking?
-   Which dependency is slow?
-   Which trace connects related service calls?

Consider:

1.  Structured JSON logs to stdout/stderr.
2.  Request IDs or correlation IDs.
3.  Metrics such as request count, error rate, latency and resource
    usage.
4.  Distributed tracing using OpenTelemetry.
5.  Centralized log and metric collection.

Avoid logging passwords, tokens, personal data, or other sensitive
values.

## 6.15 Graceful shutdown and connection draining

**Partially present:** `STOPSIGNAL SIGTERM` is specified, and Uvicorn
handles shutdown signals.

A complete shutdown strategy also needs:

-   A suitable stop timeout.
-   Load-balancer connection draining.
-   A readiness transition before termination, where supported.
-   Cleanup of database pools and message consumers.
-   A policy for in-flight background tasks.
-   Orchestrator termination grace periods.

For example, Docker supports a stop timeout:

``` bash
docker stop --timeout 30 my-python-api
```

A signal alone does not guarantee that every request or background task
finishes successfully.

## 6.16 Worker sizing and horizontal scaling

**Partially present:** The example hardcodes two Uvicorn workers.

Worker count affects:

-   Memory consumption.
-   CPU utilization.
-   Throughput.
-   Database connection counts.
-   Startup time.

A common alternative in container orchestration is one server worker per
container, then scaling the number of containers.

The correct design depends on workload, platform, and load testing.
Avoid multiplying worker count and replica count without accounting for
database connection limits.

## 6.17 Container networking and service discovery

**Missing:** The example runs a single service and does not demonstrate
internal service communication.

A real application may connect to:

-   PostgreSQL.
-   Redis.
-   A message broker.
-   An internal API.
-   Object storage.

Containers on a user-defined Docker network can reach one another by
service/container DNS name.

Do not hardcode a database container's IP address; use service names and
runtime configuration.

## 6.18 Persistent storage and volumes

**Missing:** The example does not demonstrate persistent data.

A container's writable layer is not a durable database storage strategy.
Container replacement can discard data stored only in that layer.

Use:

-   Named volumes for appropriate single-host persistent storage.
-   Managed databases for production database workloads where
    appropriate.
-   Object storage for files and artifacts.
-   Explicit backup and restore processes.

Do not store important database data only inside an ephemeral
application container.

## 6.19 CI/CD and image promotion

**Missing:** The example describes a local build, not an automated
release process.

A production pipeline commonly performs:

1.  Code checkout.
2.  Tests and static analysis.
3.  Dependency and secret scanning.
4.  Docker build.
5.  Image vulnerability scanning.
6.  SBOM/provenance generation.
7.  Image push to a registry.
8.  Deployment to staging.
9.  Smoke/integration tests.
10. Approval or automated production rollout.
11. Rollback if health or error metrics degrade.

Build once and promote the same immutable image between environments
rather than rebuilding different images for each environment.

## 6.20 Image tagging, registry management and rollback

**Missing:** The example uses a local tag only.

For a team deployment, publish to a registry such as Docker Hub or
Amazon ECR.

Use meaningful immutable release identifiers, such as a Git commit SHA
or release version. Avoid relying on `latest` as the only production
identifier.

Keep prior known-good image versions available so a deployment can be
rolled back.

## 6.21 Multi-platform builds

**Missing:** The example does not discuss CPU architecture.

A build made for `linux/amd64` may not run natively on `linux/arm64`.

This matters when developing on an ARM laptop but deploying to x86 cloud
instances, or vice versa.

Docker Buildx supports multi-platform builds, for example:

``` bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t registry.example.com/python-production-api:1.0 \
  --push .
```

This requires a suitable builder and compatible dependencies. Native
extensions must be built for each target architecture.

## 6.22 Build context hygiene and `.dockerignore`

**Partially present:** The Dockerfile itself does not define the build
context exclusions.

A `.dockerignore` file prevents unnecessary files from being sent to the
build process.

Exclude local virtual environments, Git history, caches, test output,
datasets, local secrets and unrelated artifacts.

Review the file carefully: excluding a directory required by the build
will cause missing-file errors.

## 6.23 Docker Compose and local production-like environments

**Missing:** There is no multi-service configuration.

Docker Compose describes multiple services, networks, volumes, and
configuration in one YAML file.

It is valuable for local development and integration testing. It is not
automatically a replacement for a production orchestrator.

A Compose example is provided in the next section.

## 6.24 Kubernetes and orchestration

**Missing:** The Dockerfile alone does not define a deployment platform.

Kubernetes adds concepts such as:

-   Deployments and ReplicaSets.
-   Pods.
-   Services and Ingress.
-   ConfigMaps and Secrets.
-   Startup, readiness and liveness probes.
-   Resource requests and limits.
-   Horizontal Pod Autoscaling.
-   Rolling updates and rollbacks.
-   Persistent volumes.
-   Network policies.

Learn Docker fundamentals first, then learn orchestration. Kubernetes is
not required for every production application; managed container
platforms can be simpler.

------------------------------------------------------------------------

# 7. A stronger production-oriented Dockerfile

The following example incorporates several improvements while keeping
the build understandable. It is a teaching template, not a universally
secure or complete deployment configuration. Validate the base image
digest, dependency lock, OS packages, worker model, and runtime
permissions for your actual application.

## 7.1 Suggested project structure

``` text
python-production-api/
├── Dockerfile
├── .dockerignore
├── requirements.lock
└── app/
    ├── __init__.py
    ├── main.py
    └── run.py
```

## 7.2 Configurable entrypoint: app/run.py

``` python
import os

import uvicorn


if __name__ == "__main__":
    port = int(os.getenv("PORT", "8000"))
    workers = int(os.getenv("WEB_CONCURRENCY", "1"))

    uvicorn.run(
        "app.main:app",
        host="0.0.0.0",
        port=port,
        workers=workers,
        access_log=True,
    )
```

This makes the port and worker count configurable at runtime.

For an orchestrated deployment, start with one worker per container
unless load testing and platform design justify another arrangement.

## 7.3 Improved Dockerfile template

``` dockerfile
# syntax=docker/dockerfile:1

# Replace this placeholder with a verified digest.
# Pin the same tested base image for both stages.
ARG PYTHON_IMAGE=python:3.12-slim

# -------------------------
# Builder stage
# -------------------------
FROM ${PYTHON_IMAGE} AS builder

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /build

# Copy the fully resolved dependency lock first for caching.
COPY requirements.lock .

# Optional BuildKit cache mount.
# Add compiler and development packages here only if required
# by dependencies that need to be built from source.
RUN --mount=type=cache,target=/root/.cache/pip \
    python -m pip wheel \
    --wheel-dir=/wheels \
    --require-hashes \
    -r requirements.lock


# -------------------------
# Runtime stage
# -------------------------
FROM ${PYTHON_IMAGE} AS runtime

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    APP_ENV=production \
    PORT=8000 \
    WEB_CONCURRENCY=1

WORKDIR /app

# Create an unprivileged application identity.
RUN groupadd --system app && \
    useradd --system \
      --gid app \
      --home-dir /app \
      --no-create-home \
      app

# Install only the packages required at runtime.
COPY --from=builder /wheels /wheels
COPY requirements.lock .

RUN python -m pip install \
      --no-cache-dir \
      --no-index \
      --find-links=/wheels \
      --require-hashes \
      -r requirements.lock && \
    rm -rf /wheels

# Copy application source with correct ownership.
COPY --chown=app:app app/ ./app/

# Use the non-root identity for the application.
USER app

EXPOSE 8000

# This endpoint must exist in app/main.py.
HEALTHCHECK --interval=30s \
    --timeout=5s \
    --start-period=15s \
    --retries=3 \
    CMD ["python", "-c", "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health/live', timeout=3)"]

STOPSIGNAL SIGTERM

CMD ["python", "-m", "app.run"]
```

### What changed?

| Change                      | Benefit |
|-----------------------------|---------|
| `requirements.lock`         | Makes complete dependency locking possible. |
| `--require-hashes`          | Verifies packages against lock-file hashes. |
| BuildKit cache mount        | Can accelerate repeated dependency builds. |
| Configurable `PORT`         | Avoids hardcoding the port in `CMD`. |
| Configurable worker count   | Allows runtime tuning. |
| Removed recursive `chown`   | Avoids unnecessary ownership work. |
| One worker by default       | A useful starting point for horizontal container scaling. |
| Consistent stages           | Keeps the builder and runtime on the same selected base-image line. |


**Important:** `requirements.lock` must be a complete, compatible,
hash-locked dependency file. The example will not work with the earlier
two-line `requirements.txt` unless you generate the complete lock file
with hashes.

The `ARG PYTHON_IMAGE` example is a convenient override mechanism. For
strict reproducibility, provide a verified digest as the build argument
and ensure both stages use that exact immutable image reference.

Also, the example assumes the final application does not require
additional OS runtime libraries. Add those when your dependencies need
them.

------------------------------------------------------------------------

# 8. Docker Compose: run the API with PostgreSQL and Redis

This is the next major practical step. A production API rarely exists in
isolation.

A typical architecture might look like:

``` text
                   API client
                       |
                       v
                 FastAPI service
                    /       \
                   /         \
                  v           v
             PostgreSQL     Redis
              Database      Cache
```

## 8.1 docker-compose.yml

``` yaml
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
    image: python-production-api:1.0
    ports:
      - "8000:8000"
    environment:
      APP_ENV: production
      PORT: "8000"
      WEB_CONCURRENCY: "1"
      DATABASE_URL: postgresql://appuser:change-me@db:5432/appdb
      REDIS_URL: redis://redis:6379/0
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: postgres:16
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: change-me
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser -d appdb"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  redis:
    image: redis:7
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 5
    restart: unless-stopped

volumes:
  postgres_data:
```

This is a local learning example. Do not deploy it unchanged to a public
production environment.

In particular:

-   Replace example passwords with secrets.
-   Pin service images appropriately.
-   Restrict database network exposure.
-   Configure backups and restore testing.
-   Configure database authentication and TLS as required.
-   Add application database and Redis clients.
-   Add migrations and connection-pool management.
-   Configure resource limits and observability.

## 8.2 Why use service names?

Inside the Compose network, the API connects to:

``` text
db:5432
redis:6379
```

`db` and `redis` are service names. Compose provides internal DNS
service discovery.

Do not use `localhost` for a different container. Inside the API
container, `localhost` means the API container itself.

## 8.3 Start the services

``` bash
docker compose up --build -d
```

View status:

``` bash
docker compose ps
```

View logs:

``` bash
docker compose logs -f api
```

Stop services:

``` bash
docker compose down
```

Stop services and delete named volumes:

``` bash
docker compose down -v
```

**Warning:** `down -v` deletes the Compose-managed database volume and
therefore its stored data. Use it only when you intentionally want to
remove the local database data.

------------------------------------------------------------------------

# 9. Production deployment checklist

Use this as a review list before releasing an image.

## Image and build

-   [ ] Base image is maintained and its version/digest is controlled.
-   [ ] Python dependencies are fully locked.
-   [ ] Package hashes are verified where practical.
-   [ ] Multi-stage build excludes unnecessary build tools from runtime.
-   [ ] Build context is controlled using `.dockerignore`.
-   [ ] Build cache is used appropriately.
-   [ ] Image size is measured and unnecessary files are removed.
-   [ ] Image is built for the intended CPU architecture.

## Security

-   [ ] Application runs as a non-root user.
-   [ ] No credentials are baked into image layers.
-   [ ] Runtime secrets are injected securely.
-   [ ] OS and Python dependencies are scanned.
-   [ ] Image provenance and SBOM are considered.
-   [ ] Unnecessary Linux capabilities are dropped.
-   [ ] Privileged mode is avoided.
-   [ ] Writable filesystem paths are restricted where possible.
-   [ ] Base images and dependencies are updated regularly.

## Application operations

-   [ ] Health-check endpoints are implemented correctly.
-   [ ] Liveness and readiness responsibilities are clear.
-   [ ] Logs go to stdout/stderr.
-   [ ] Logs avoid secrets and sensitive data.
-   [ ] Metrics and tracing are available.
-   [ ] Graceful shutdown is tested.
-   [ ] Worker count is load-tested.
-   [ ] Database pools and external connections are managed.
-   [ ] Resource limits are configured.

## Deployment

-   [ ] Image is pushed to a registry.
-   [ ] Image tags identify a specific release.
-   [ ] CI/CD runs tests and security checks.
-   [ ] Staging deployment is validated.
-   [ ] Production rollout strategy is defined.
-   [ ] Rollback to a known-good image is possible.
-   [ ] Persistent data has backup and restore procedures.
-   [ ] Monitoring and alerting are configured.

------------------------------------------------------------------------

# 11. Knowledge assessment

Try answering these questions without referring to the explanations.

1.  What is the difference between an image and a container?
2.  Why does every `FROM` start a separate build stage?
3.  What is a wheel file?
4.  Why does `COPY --from=builder` matter?
5.  How does Docker layer caching improve build performance?
6.  What is the difference between `RUN`, `CMD`, and `ENTRYPOINT`?
7.  Why is `--no-index` useful in the runtime installation stage?
8.  Why should an application avoid running as root?
9.  What does `EXPOSE` do, and what does it not do?
10. What is the difference between Docker health status and container
    running status?
11. Why does a health check not automatically restart an unhealthy
    container?
12. What is the difference between liveness and readiness?
13. Why does exec-form `CMD` help signal handling?
14. Why are immutable image digests useful?
15. Why are transitive dependencies important for reproducible builds?
16. What is the purpose of a BuildKit cache mount?
17. How should build-time secrets be supplied?
18. Why should runtime secrets not be baked into an image?
19. Why are CPU and memory limits important?
20. What is the difference between a Docker volume and a container
    writable layer?
21. Why should containers communicate through service names instead of
    hardcoded IP addresses?
22. What does an SBOM contain?
23. What is image vulnerability scanning intended to find?
24. Why might a package build in the builder but fail in the runtime
    stage?
25. Why can one worker per container be useful in an orchestrated
    environment?

------------------------------------------------------------------------



