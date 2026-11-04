# CI/CD Fundamentals - Practical Notes With GitHub Actions & Docker

## 1. Understanding CI/CD

CI/CD automates how application code is validated, packaged, and
deployed, moving code from a developer's laptop to a running application
with minimal manual intervention.

-   **CI -- Continuous Integration:** Automatically checks, tests, and
    builds code when changes are pushed to GitHub.
-   **CD -- Continuous Delivery/Deployment:** Deploys a successfully
    built artifact, such as a Docker image, to a target environment.


---

## 2. Practical Scenario And Pipeline Architecture

| Component | Responsibility |
| --- | --- |
| GitHub repository | Stores source code and workflow definitions |
| GitHub Actions | Automates CI/CD workflows |
| Docker | Packages the application into an image |
| Docker Compose | Defines and manages application containers |
| Linux laptop | Deployment host and self-hosted runner for CD |

``` text
Developer laptop (Python + uv)
  -> Pre-commit, Ruff, tests
  -> Git push
  -> GitHub repository
  -> GitHub Actions CI: install, lint, test, build image
  -> Versioned Docker image
  -> Registry (recommended)
  -> GitHub Actions CD on self-hosted laptop
  -> Docker Compose replaces container
  -> New version running
```

> **Important:** GitHub Actions is the automation engine; the repository
> stores code and workflow configuration; the Docker image is the
> deployable artifact.

---

## 3. Fundamental CI/CD Concepts

> **3.1 Version Control -- [Git Notes](./Documentations/git.md)**

Git tracks source-code changes. GitHub Actions uses events such as
`push` and `pull_request` to decide when to run workflows.

> **3.2 Code Hygiene -- [Pre-Commit Notes](./Documentations/pre-commit.md)**

Pre-commit runs configured checks before Git creates a commit. A failed
check stops the commit until the issue is fixed.

> **Important:** Pre-commit does not run the full test suite unless
> configured to. CI should also run the full tests.

> **3.3 Automated Testing -- [Pytest Notes](./Documentations/pytest.md)**

Tests verify expected behavior. CI runs tests because developers may
forget, local environments may differ, and changes from others may
introduce regressions. Tests provide a quality gate before building and
deploying.

> **3.4 Containerization**

> **Docker -- [Docker Notes](./Documentations/docker.md)**

| Concept | Responsibility |
| --- | --- |
| Dockerfile | Image build instructions |
| Docker image | Packaged application |
| Docker container | Running application process |

A Dockerfile typically selects a base image, sets a working directory,
copies dependency definitions, installs locked dependencies, copies
source, documents a port, defines startup, and builds a versioned image.

> *Note:* Examples assume a FastAPI application with `main.py`
> containing an `app` object.

> **Docker Compose -- [Docker Compose Notes](./Documentations/docker.md)**

Compose defines an application in `compose.yaml` rather than requiring
long Docker commands. It simplifies consistent container management and
service replacement.

> **3.5 GitHub Actions -- [GitHub Actions Notes](./Documentations/git-hub-actions.md)**

GitHub Actions runs workflows in response to events:

1.  GitHub receives an event.
2.  A matching workflow is selected and scheduled.
3.  A runner receives the job.
4.  The runner executes steps.
5.  The workflow reports success or failure.

---

> **3.6 Workflow Triggers and Conditions**

GitHub Actions workflows execute in response to repository events.

| Event | Purpose |
| --- | --- |
| `push` | Executes when code is pushed |
| `pull_request` | Validates proposed changes before merging |
| `workflow_dispatch` | Allows manual workflow execution |
| `schedule` | Executes workflows on a schedule |
| `release` | Executes when a GitHub release is published |

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:
```

- `push` triggers CI after code is pushed.
- `pull_request` validates code before merging.
- Branch filters restrict workflow execution to selected branches.
- Job conditions control whether individual jobs execute.

**Recommended for this project:** run linting and tests on pull requests, build and publish Docker images after successful pushes to `main`, deploy only after a successful build, and protect `main` by requiring CI checks to pass.

---

> **3.7 Workflow Jobs, Dependencies, and Parallel Execution**

A workflow can contain multiple jobs. Jobs execute sequentially or in parallel depending on their dependencies. The `needs` keyword establishes dependencies.

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running lint"
  test:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running tests"
  build:
    needs: [lint, test]
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building Docker image"
  deploy:
    needs: build
    runs-on: self-hosted
    steps:
      - run: echo "Deploying application"
```

```text
          GitHub Event
             /   \
          Lint   Test
             \   /
             Build
               |
             Deploy
```

- Jobs without dependencies can run in parallel.
- Jobs with `needs` wait for their dependencies.
- A failed required job normally prevents dependent jobs from running.
- Parallel execution can reduce pipeline time.

---

> **3.8 Quality Gates and Pipeline Failure Handling**

A quality gate is a condition that must be satisfied before the pipeline proceeds.

| Stage | Quality Gate |
| --- | --- |
| Code validation | Ruff checks pass |
| Testing | Required tests pass |
| Dependency installation | `uv sync` succeeds |
| Docker build | Image builds successfully |
| Registry | Image push succeeds |
| Deployment | Container starts successfully |
| Post-deployment | Application health check succeeds |

GitHub Actions stops subsequent steps in a job when a command fails by default. Dependent jobs are normally skipped when required jobs fail.

```yaml
- name: Run tests
  run: uv run pytest
- name: Build Docker image
  run: docker build -t python-CI-CD:${{ github.sha }} .
```

If tests fail, the Docker build step does not execute. **A failed quality gate should prevent an unsafe deployment.**

---

## 4. Implementing The Pipeline

> **4.1 Initialize The Python Project**

``` bash
uv init python-CI-CD
CD python-CI-CD
uv add fastapi uvicorn
uv add --dev pytest ruff pre-commit
```

Create the application and tests. Before committing:

``` bash
uv run pre-commit run --all-files
uv run pytest
git add .
git commit -m "Add Python application"
git push origin main
```

> **4.2 CI -- Build And Validate**

The CI job checks out code, installs Python and uv, syncs dependenCIes,
runs Ruff and pytest, and builds the Docker image.

``` yaml
name: Python CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

jobs:
  CI:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - name: Install uv
        uses: astral-sh/setup-uv@v6
      - name: Install dependencies
        run: uv sync --frozen
      - name: Ruff lint
        run: uv run ruff check .
      - name: Ruff format
        run: uv run ruff format --check .
      - name: Run tests
        run: uv run pytest
      - name: Build Docker image
        run: docker build -t python-CI-CD:${{ github.sha }} .
```

`ubuntu-latest` uses a GitHub-hosted runner and a clean environment. CI
and CD can be separated so only a successful push to `main` deploys.

> **4.3 CD -- Deploy To The Same Laptop**

The laptop needs Docker Engine, a registered GitHub self-hosted runner,
deployment configuration, and permission to run Docker commands.

``` yaml
deploy:
  needs: CI
  runs-on: [self-hosted, linux, x64]
  if: github.event_name == 'push' && github.ref == 'refs/heads/main'
  steps:
    - name: Checkout repository
      uses: actions/checkout@v4
    - name: Build deployment image
      run: docker build -t python-CI-CD:${{ github.sha }} .
    - name: Deploy application
      run: |
        docker compose down
        docker compose up -d --force-recreate
    - name: Verify deployment
      run: |
        docker compose ps
        docker compose logs --tail=50 app
```

The `CI` and `deploy` jobs belong under the same top-level `jobs` key.

> **Architecture detail:** An image built on a GitHub-hosted runner does
> not automatically exist on the laptop. This simple example rebuilds it
> on the self-hosted runner. A production-style pipeline should build
> once, push to a registry, and deploy that exact image.

---

## 5. Recommended Production-Style Architecture

A Docker registry stores images and makes them available to deployment
machines. Examples include GHCR, Docker Hub, and Amazon ECR.

> **5.1 Image Lifecycle**

``` text
GitHub Actions CI -> test/build -> Docker registry (GHCR)
                                      |
GitHub Actions CD on self-hosted laptop <- pull exact image
                                      |
                              Docker Engine / Compose
                                      |
                              New container running
```

---

## 5.2 Build Once, Deploy Many

> **Core principle:** Build an artifact once, validate it, and promote that exact artifact across environments.

```text
Source Code -> Build Docker Image -> Versioned Image
                                     |-> Development
                                     |-> Testing
                                     |-> Staging
                                     `-> Production
```

Rebuilding separately for each environment can produce different artifacts if dependencies, base images, or build inputs change. CI should build and publish one image; CD should deploy that exact image.

> **Image Tags Vs. Image Digests**

| Identifier | Meaning |
| --- | --- |
| `latest` | Mutable image reference |
| `v1.0.0` | Release version |
| Git commit SHA | Identifies the source revision |
| Image digest | Identifies exact image content |

```text
ghcr.io/user/python-CI-CD:a1b2c3d
ghcr.io/user/python-CI-CD@sha256:...
```

A tag can point to different images over time; a digest identifies specific image content. The image tested and published by CI should be the same image deployed by CD.

---

## 5.3 Caching In CI/CD

ci pipelines repeatedly download dependencies and build Docker images. Caching avoids unnecessary repeated work.

| Cache | Purpose | Example |
| --- | --- | --- |
| Dependency cache | Reuse downloaded Python packages | UV cache |
| Docker build cache | Reuse unchanged image layers | Docker BuildKit |
| GitHub Actions cache | Reuse files between workflow runs | `actions/cache` |

Benefits include faster workflows, reduced dependency download time, fewer repeated build operations, and improved CI efficiency.

```yaml
- name: Install UV
  uses: astral-sh/setup-uv@v6
  with:
    enable-cache: true
```

> **Important:** Caching is a performance optimization, not a correctness mechanism. The pipeline should remain reproducible when the cache is unavailable.

---

> **5.4 Image Tags**

Do not rely exclusively on `latest` for production deployments.

| Tag | Meaning |
| --- | --- |
| `latest` | Mutable image reference |
| `v1.0.0` | Release version |
| `a1b2c3d` | Specific Git commit |
| `main` | Often a mutable branch-based reference |

Commit-based tags identify the deployed source version and help with
rollback.

``` bash
docker build -t python-CI-CD:a1b2c3d .
```

> **5.5 CI/CD Workflow Using GHCR**

This example tests and builds the image once, pushes it to GHCR, then
deploys the same tag.

``` yaml
name: Python CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ghcr.io/your-github-username/python-CI-CD

jobs:
  CI:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    outputs:
      image_tag: ${{ steps.meta.outputs.tag }}
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - name: Install uv
        uses: astral-sh/setup-uv@v6
      - name: Install dependencies
        run: uv sync --frozen
      - name: Lint
        run: uv run ruff check .
      - name: Format check
        run: uv run ruff format --check .
      - name: Tests
        run: uv run pytest
      - name: Generate image tag
        id: meta
        run: echo "tag=${GITHUB_SHA}" >> "$GITHUB_OUTPUT"
      - name: Login to GHCR
        if: github.event_name == 'push'
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - name: Build and push Docker image
        if: github.event_name == 'push'
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ${{ env.IMAGE_NAME }}:${{ steps.meta.outputs.tag }}

  deploy:
    needs: CI
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    runs-on: [self-hosted, linux, x64]
    permissions:
      contents: read
      packages: read
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Login to GHCR
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - name: Pull and deploy
        env:
          IMAGE_NAME: ${{ env.IMAGE_NAME }}
          IMAGE_TAG: ${{ needs.CI.outputs.image_tag }}
        run: |
          export IMAGE_NAME IMAGE_TAG
          docker compose pull
          docker compose up -d --force-recreate --remove-orphans
      - name: Verify deployment
        run: |
          docker compose ps
          docker compose logs --tail=50 app
```

Replace `your-github-username` with the lowercase GitHub username or
organization. The deployment Compose file can reference the image:

``` yaml
services:
  app:
    image: ${IMAGE_NAME}:${IMAGE_TAG}
    container_name: python-app
    restart: unless-stopped
    ports:
      - "8000:8000"
```

Make `IMAGE_NAME` and `IMAGE_TAG` available to Compose in the deployment
step. The example assumes the package is accessible to the runner using
the workflow token; private packages require appropriate permissions.

> **5.6 What Happens After A Push?**

1.  GitHub receives the push.
2.  CI starts on a hosted runner.
3.  uv installs dependencies; Ruff checks code; pytest runs tests.
4.  Docker builds the image and pushes it to GHCR with the commit SHA.
5.  The deployment job waits for CI.
6.  The self-hosted runner pulls the exact image.
7.  Compose replaces the old container.
8.  The new application starts on the laptop.

> **Central concept:** CI validates and packages the application; CD
> deploys the validated, versioned artifact.

---

## 6. Essential Deployment Concepts

> **6.1 Deployment Vs. Release**

| Term | Meaning |
| --- | --- |
| Deployment | Installing a version into an environment |
| Release | Making that version available to users |

An application can be deployed without immediately being exposed to
users.

> **6.2 Rolling Deployment**

Gradually replaces old instances with new instances. Useful with
multiple replicas to minimize downtime.

> **6.3 Blue-Green Deployment**

Maintains two environments: **Blue** is active; **Green** contains the
new version. After testing Green, traffic is switched to it.

> **6.4 Rollback**

Rollback returns to a previously working version. With image tags, an
older image can be deployed:

``` bash
IMAGE_NAME=ghcr.io/your-github-username/python-CI-CD \
IMAGE_TAG=<previous-commit-sha> \
docker compose up -d --force-recreate
```

> **6.5 Health Checks**

A running container is not necessarily a healthy application. For
example, it may be unable to connect to its database.

A FastAPI health endpoint might be:

``` python
@app.get("/health")
def health():
    return {"status": "healthy"}
```

Compose can monitor it:

``` yaml
services:
  app:
    image: ${IMAGE_NAME}:${IMAGE_TAG}
    ports:
      - "8000:8000"
    healthcheck:
      test:
        - CMD
        - python
        - -c
        - "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health')"
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s
```


---

## 7. Laptop-Based Deployment Considerations

> **7.1 Self-Hosted Runner Lifecycle**

1.  Open the repository's **Settings → Actions → Runners**.
2.  Select **New self-hosted runner**.
3.  Choose Linux and the appropriate architecture.
4.  Follow GitHub's installation and registration instructions.
5.  Start the runner and confirm it is online.

Target it in a workflow with:

``` yaml
runs-on: [self-hosted, linux, x64]
```

> **7.2 Docker Permissions**

The runner needs permission to communicate with Docker Engine. On Linux
this commonly involves Docker socket access.

> **Security warning:** Membership in the Docker group effectively
> grants root-level privileges on the host.

-   Do not run untrusted pull-request code on a privileged self-hosted
    runner.
-   Restrict who can modify deployment workflows.
-   Protect the main branch.
-   Keep runner software and the OS updated.
-   Consider a dedicated deployment runner or isolated environment.

> **7.3 Deployment Downtime**

This sequence stops the old application before starting the new one and
can cause downtime:

``` bash
docker compose down
docker compose up -d
```

Prefer:

``` bash
docker compose up -d --force-recreate
```

Replacing a single container can still cause a short interruption. For
zero-downtime deployments, consider blue-green deployment or a reverse
proxy with multiple application instances.

> **7.4 Keep Secrets Safe**

Never hardcode passwords, access tokens, or private keys in source code,
Dockerfiles, workflow YAML, or Git repositories. Use GitHub Actions
Secrets or another appropriate secret-management solution.

``` yaml
${{ secrets.GITHUB_TOKEN }}
```

---

## 8. Important Files To Remember

| File | Responsibility |
| --- | --- |
| `pyproject.toml` | Python project configuration and dependencies |
| `uv.lock` | Dependency version locking |
| `.pre-commit-config.yaml` | Local Git commit checks |
| `Dockerfile` | Image build instructions |
| `compose.yaml` | Container deployment configuration |
| `.dockerignore` | Files excluded from Docker build context |
| `.github/workflows/CI-CD.yaml` | CI/CD automation definition |

Example `.dockerignore`:

``` text
.git/
.github/
.venv/
__pycache__/
.pytest_cache/
.ruff_cache/
*.pyc
.env
```

This prevents unnecessary files and local secrets from entering the
Docker build context.

---

## 9. Final Mental Model

  -----------------------------------------------------------------------
  Component                           Responsibility
  ----------------------------------- -----------------------------------
  Developer                           Write code, manage dependencies,
                                      run local checks, and push code

  GitHub                              Store source code and trigger
                                      automation

  CI                                  Validate code, run tests, and build
                                      a versioned Docker image

  Docker registry                     Store and distribute the image

  CD                                  Pull the validated image and deploy
                                      it

  Docker Engine                       Run and manage containers
  -----------------------------------------------------------------------

> **The central principle of CI/CD:** Developers focus on writing code.
> The pipeline automatically validates, packages, and deploys it
> consistently and repeatably.

Once the pipeline works on your laptop, try deploying the same image to
a separate Linux machine. This helps distinguish a CI/CD runner from a
deployment server---an important real-world DevOps concept.

---

## 9. CI/CD Tools Landscape: Tools, Alternatives, And Use Cases

This reference section describes tools across the software delivery lifecycle. Keep implementation examples focused on GitHub Actions, UV, Ruff, Pytest, Docker, GHCR, and Docker Compose; use this section for alternatives.

> **9.1 Source Code Management**

| Tool | Advantages | Use Case |
| --- | --- | --- |
| GitHub | Git hosting, pull requests, Actions integration | GitHub-based projects |
| GitLab | Integrated source control, CI/CD, and registry | End-to-end DevOps platform |
| Bitbucket | Jira and Atlassian integration | Atlassian-based organizations |
| Azure Repos | Microsoft ecosystem integration | Azure DevOps environments |

**Recommended:** Git + GitHub for this project.

> **9.2 Python Dependency Management**

| Tool | Advantages | Use Case |
| --- | --- | --- |
| UV | Fast, lockfile support, environment management | Modern Python projects |
| Poetry | Integrated dependency management and packaging | Python applications and libraries |
| PDM | Modern Python packaging support | PEP-compliant Python projects |
| pip | Standard Python package installer | Simple Python projects |
| Conda | Manages Python and native dependencies | Data science and scientific computing |

**Recommended:** UV.

> **9.3 Code Quality And Testing Tools**

| Category | Primary Tool | Alternatives | Purpose |
| --- | --- | --- | --- |
| Linting | Ruff | Flake8, Pylint | Detect code-quality issues |
| Formatting | Ruff Formatter | Black | Consistent formatting |
| Import sorting | Ruff | isort | Organize imports |
| Type checking | mypy | Pyright | Static type checking |
| Git hooks | pre-commit | Lefthook | Run checks before commits |
| Unit testing | Pytest | unittest | Test application behavior |
| Coverage | Coverage.py | pytest-cov | Measure test coverage |
| Property testing | Hypothesis | — | Test generated inputs |

Ruff combines several linting and formatting capabilities. mypy and Ruff solve different problems. Pre-commit runs configured checks; Pytest verifies behavior; Coverage.py measures which code tests exercise.

**Recommended:** Ruff + Pre-commit + Pytest; add mypy and coverage later.

> **9.4 CI/CD Automation Tools**

| Tool | Advantages | Use Case |
| --- | --- | --- |
| GitHub Actions | Native GitHub integration, hosted and self-hosted runners | GitHub repositories |
| Jenkins | Extensive plugins and customization | Enterprise/self-managed pipelines |
| GitLab CI/CD | Integrated source control and CI/CD | GitLab repositories |
| circleci | Managed CI platform and reusable configuration | Dedicated CI environments |
| Azure Pipelines | Microsoft and Azure integration | Microsoft-centric organizations |
| Buildkite | Flexible agent-based execution | Custom build infrastructure |

GitHub Actions is the best fit for this project. Jenkins is useful for highly customized self-managed automation; GitLab ci/CD fits GitLab-hosted code; circleci fits teams preferring managed CI.

**Recommended:** Learn GitHub Actions deeply before exploring alternatives.

> **9.5 Containerization Tools**

| Tool | Advantages | Use Case |
| --- | --- | --- |
| Docker | Mature ecosystem and broad tooling | Container build and execution |
| Podman | Daemonless and rootless operation | Security-conscious Linux environments |
| containerd | Lightweight container runtime | Container infrastructure |
| Buildah | Builds OCI-compatible images | Container image building |

| Compose/Orchestration Tool | Use Case |
| --- | --- |
| Docker Compose | Manage containers on one host |
| Podman Compose | Compose-style application management |
| Kubernetes | Orchestrate containers across clusters |
| Nomad | Workload scheduling and orchestration |

Docker and Podman are commonly compared as container-engine alternatives.

**Recommended:** Docker + Docker Compose for the current project.

> **9.6 Container Registries**

| Tool | Advantages | Use Case |
| --- | --- | --- |
| GHCR | GitHub integration | GitHub Actions projects |
| Docker Hub | Widely used image repository | General-purpose image distribution |
| Amazon ECR | AWS integration | AWS deployments |
| Google Artifact Registry | GCP integration | Google Cloud deployments |
| Azure Container Registry | Azure integration | Azure deployments |
| Harbor | Self-hosted registry and access control | Private infrastructure |

**Recommended:** GHCR because it integrates naturally with GitHub Actions.

> **9.7 Deployment And Configuration Management**

| Tool | Advantages | Use Case |
| --- | --- | --- |
| Docker Compose | Simple container deployment | Single-host applications |
| Ansible | Agentless automation and configuration management | Server configuration |
| Kubernetes | Scheduling, scaling, and self-healing | Multi-node container environments |
| Helm | Reusable Kubernetes application packages | Kubernetes deployments |
| Argo CD | GitOps-based continuous delivery | Kubernetes deployment automation |
| Flux CD | GitOps automation | Kubernetes deployment automation |

Docker Compose defines containers to run; Ansible automates server configuration; Kubernetes orchestrates containers across machines; Helm packages Kubernetes applications; Argo CD and Flux automate GitOps delivery.

**Recommended:** Docker Compose now; Ansible and Kubernetes later.

> **9.8 Infrastructure As Code**

| Tool | Advantages | Use Case |
| --- | --- | --- |
| Terraform | Declarative provisioning, broad provider support | Multi-cloud infrastructure |
| OpenTofu | Open-source Terraform-compatible alternative | Infrastructure automation |
| Pulumi | Uses languages such as Python and TypeScript | Developer-oriented IaC |
| AWS CloudFormation | Native AWS provisioning | AWS infrastructure |
| Ansible | Configuration management and automation | Server setup and configuration |

| Terraform | Ansible |
| --- | --- |
| Provisions infrastructure | Configures infrastructure |
| Creates virtual machines | Installs packages |
| Creates networks | Configures services |
| Manages infrastructure lifecycle | Automates operational tasks |

```text
Terraform -> Create Linux VM -> Ansible -> Install/configure Docker -> Deploy application
```

**Recommended:** Learn Terraform fundamentals later; it is not required for the current laptop-based project.

> **9.9 Security And Vulnerability Scanning**

| Tool | Purpose |
| --- | --- |
| Bandit | Python security linting |
| pip-audit | Python dependency vulnerability scanning |
| Trivy | Container image and infrastructure scanning |
| Docker Scout | Container image security analysis |
| Gitleaks | Detect accidentally committed secrets |
| Checkov | Infrastructure configuration security scanning |

```bash
uv add --dev bandit pip-audit
uv run bandit -r .
uv run pip-audit
```

Recommended CI additions: dependency vulnerability scanning, secret detection, and Docker image vulnerability scanning.

> **9.10 Monitoring And Observability Tools**

| Tool | Purpose | Use Case |
| --- | --- | --- |
| Prometheus | Metrics collection | System and application metrics |
| Grafana | Visualization | Dashboards and alerts |
| OpenTelemetry | Telemetry instrumentation | Metrics, logs, and traces |
| Loki | Log aggregation | Centralized logs |
| ELK Stack | Log processing and analysis | Search and analyze logs |
| Sentry | Error monitoring | Track Python exceptions |

| Pillar | Question Answered |
| --- | --- |
| Logs | What happened? |
| Metrics | How much or how often? |
| Traces | Where did the request spend time? |

Start with Docker logs and resource monitoring; introduce Prometheus and Grafana later.

> **9.11 Notification And Alerting**

| Tool | Use Case |
| --- | --- |
| Slack | CI/CD notifications |
| Microsoft Teams | Team deployment notifications |
| PagerDuty | Production incident alerts |
| Email | Build and deployment status notifications |

Examples include CI failure, successful image publication, completed deployment, failed health check, or application unavailability.

---

## 10. CI/CD Security And Reliability

> **10.1 Secrets Management**

Secrets include database passwords, API keys, registry credentials, access tokens, and private keys. Never hardcode secrets in source code, Dockerfiles, or workflow YAML. Use GitHub Actions Secrets or a dedicated secret-management solution.

```yaml
password: ${{ secrets.GITHUB_TOKEN }}
```

> **10.2 Self-Hosted Runner Security**

A self-hosted runner executes workflows on your own machine. Do not execute untrusted pull-request code on a privileged deployment runner. Restrict repository write permissions, protect deployment branches, update runner software, restrict Docker socket access, and separate CI/deployment permissions where possible.

> **Important:** Access to the Docker socket can effectively provide root-level control over the host.

> **10.3 Supply-Chain Security**

Software supply-chain security helps ensure application code, dependencies, and build artifacts are trustworthy.

| Concept | Purpose |
| --- | --- |
| SBOM | Software Bill of Materials; lists components and dependencies |
| Image scanning | Detect vulnerabilities in container images |
| Image signing | Verify image authenticity and integrity |
| Dependency auditing | Detect vulnerable packages |
| Secret scanning | Detect accidentally exposed credentials |

Tools such as Trivy, Gitleaks, and Cosign can help implement these practices.

> **10.4 Deployment Reliability**

A reliable deployment should include successful CI checks, versioned Docker images, health verification, deployment logging, rollback capability, and persistent data management.

> A successful workflow does not automatically guarantee a healthy application.

---

## 11. Configuration And Persistent Data

> **11.1 Environment Variables**

Applications need different configuration in different environments.

| Configuration | Development | Production |
| --- | --- | --- |
| Debug mode | Enabled | Disabled |
| Database | Local database | Production database |
| Log level | DEBUG | INFO |
| API URL | Localhost | Production domain |
| Secrets | Development secrets | Production secrets |

```python
import os

APP_ENV = os.getenv("APP_ENV", "development")
DEBUG = os.getenv("DEBUG", "false").lower() == "true"
```

Ideally, the same Docker image is deployable across environments using different runtime configuration.

> **11.2 Persistent Storage**

Containers are replaceable; their writable filesystem should not be treated as permanent storage. Use Docker volumes or external storage for persistent application data.

```yaml
services:
  app:
    image: ${IMAGE_NAME}:${IMAGE_TAG}
    volumes:
      - app-data:/app/uploads

volumes:
  app-data:
```

> **11.3 Database Migrations**

Database migrations manage schema changes, such as adding a column required by a new application version. Alembic is commonly used with SQLAlchemy.

> **Important:** Rolling back a Docker image does not automatically roll back database migrations. Consider database compatibility separately.

---

## 12. Monitoring And Observability

> **12.1 Logging**

Logs help investigate what happened inside the application.

```bash
docker compose logs -f app
docker compose logs --tail=100 app
```

> **12.2 Metrics**

Metrics help monitor system resource consumption and application performance.

```bash
docker stats
```

Common metrics include CPU utilization, memory consumption, network traffic, API response time, request count, and error rate.

> **12.3 Health Checks**

A running container is not necessarily a healthy application. Use health endpoints to verify readiness.

```python
@app.get("/health")
def health():
    return {"status": "healthy"}
```

> **12.4 Observability Tools**

As the application grows, consider Prometheus for metrics, Grafana for dashboards, Loki for logs, OpenTelemetry for instrumentation, and Sentry for application errors.


---

## 13. CI/CD Revision And Learning Roadmap

> **13.1 Essential Concepts Revision**

| Concept | What You Should Understand |
| --- | --- |
| CI | Automated validation and integration |
| CD | Automated delivery and deployment |
| Trigger | What starts a workflow |
| Runner | Where workflow jobs execute |
| Job | Collection of workflow steps |
| Quality gate | Condition required to proceed |
| Artifact | Output produced by a build |
| Image registry | Stores and distributes Docker images |
| Image tag | Identifies an image reference |
| Image digest | Identifies exact image content |
| Deployment | Installing an application version |
| Rollback | Returning to a previous version |
| Health check | Verifying application health |
| IaC | Managing infrastructure through code |
| GitOps | Managing desired deployment state through Git |
| Observability | Understanding application behavior through telemetry |

> **13.2 Recommended Tool Learning Roadmap**

| Phase | Tools | Goal |
| --- | --- | --- |
| 1. Development | Git, UV, Ruff, Pre-commit, Pytest | Python development workflow |
| 2. Containerization | Docker, Docker Compose | Build and run containers |
| 3. CI/CD | GitHub Actions | Automate validation and builds |
| 4. Artifact management | GHCR | Publish and retrieve images |
| 5. Deployment | Self-hosted runner | Automated laptop deployment |
| 6. Security | Trivy, Gitleaks, pip-audit | Secure software delivery |
| 7. Monitoring | Prometheus, Grafana | Observe application health |
| 8. Infrastructure | Terraform, Ansible | Automate infrastructure |
| 9. Orchestration | Kubernetes, Helm, Argo CD | Manage production deployments |

