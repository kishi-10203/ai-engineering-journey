# CI/CD Fundamentals -- Practical Notes with GitHub Actions & Docker

## 1. Understanding CI/CD

CI/CD automates how application code is validated, packaged, and
deployed, moving code from a developer's laptop to a running application
with minimal manual intervention.

-   **CI -- Continuous Integration:** Automatically checks, tests, and
    builds code when changes are pushed to GitHub.
-   **CD -- Continuous Delivery/Deployment:** Deploys a successfully
    built artifact, such as a Docker image, to a target environment.

## 2. Practical Scenario and Pipeline Architecture

| Component | Responsibility |
|---|---|
| Developer | Write code, manage dependencies, run local checks, and push code |
| GitHub | Store source code and trigger automation |
| CI | Validate code, run tests, and build a versioned Docker image |
| Docker registry | Store and distribute the image |
| CD | Pull the validated image and deploy it |
| Docker Engine | Run and manage containers |

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

## 3. Fundamental CI/CD Concepts

### 3.1 Version control -- [Git Notes](./documentations/git.md)

Git tracks source-code changes. GitHub Actions uses events such as
`push` and `pull_request` to decide when to run workflows.

### 3.2 Code hygiene -- [Pre-commit Notes](./documentations/pre-commit.md)

Pre-commit runs configured checks before Git creates a commit. A failed
check stops the commit until the issue is fixed.

> **Important:** Pre-commit does not run the full test suite unless
> configured to. CI should also run the full tests.

### 3.3 Automated testing -- [Pytest Notes](./documentations/pytest.md)

Tests verify expected behavior. CI runs tests because developers may
forget, local environments may differ, and changes from others may
introduce regressions. Tests provide a quality gate before building and
deploying.

### 3.4 Containerization

#### Docker -- [Docker Notes](./documentations/docker.md)

| Concept | Responsibility |
|---|---|
| Dockerfile | Image build instructions |
| Docker image | Packaged application |
| Docker container | Running application process |

A Dockerfile typically selects a base image, sets a working directory,
copies dependency definitions, installs locked dependencies, copies
source, documents a port, defines startup, and builds a versioned image.

> *Note:* Examples assume a FastAPI application with `main.py`
> containing an `app` object.

#### Docker Compose -- [Docker Compose Notes](./documentations/docker.md)

Compose defines an application in `compose.yaml` rather than requiring
long Docker commands. It simplifies consistent container management and
service replacement.

### 3.5 GitHub Actions -- [GitHub Actions Notes](./documentations/git-hub-actions.md)

GitHub Actions runs workflows in response to events:

1.  GitHub receives an event.
2.  A matching workflow is selected and scheduled.
3.  A runner receives the job.
4.  The runner executes steps.
5.  The workflow reports success or failure.

## 4. Implementing the Pipeline

### 4.1 Initialize the Python project

``` bash
uv init python-ci-cd
cd python-ci-cd
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

### 4.2 CI -- Build and validate

The CI job checks out code, installs Python and uv, syncs dependencies,
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
  ci:
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
        run: docker build -t python-ci-cd:${{ github.sha }} .
```

`ubuntu-latest` uses a GitHub-hosted runner and a clean environment. CI
and CD can be separated so only a successful push to `main` deploys.

### 4.3 CD -- Deploy to the same laptop

The laptop needs Docker Engine, a registered GitHub self-hosted runner,
deployment configuration, and permission to run Docker commands.

``` yaml
deploy:
  needs: ci
  runs-on: [self-hosted, linux, x64]
  if: github.event_name == 'push' && github.ref == 'refs/heads/main'
  steps:
    - name: Checkout repository
      uses: actions/checkout@v4
    - name: Build deployment image
      run: docker build -t python-ci-cd:${{ github.sha }} .
    - name: Deploy application
      run: |
        docker compose down
        docker compose up -d --force-recreate
    - name: Verify deployment
      run: |
        docker compose ps
        docker compose logs --tail=50 app
```

The `ci` and `deploy` jobs belong under the same top-level `jobs` key.

> **Architecture detail:** An image built on a GitHub-hosted runner does
> not automatically exist on the laptop. This simple example rebuilds it
> on the self-hosted runner. A production-style pipeline should build
> once, push to a registry, and deploy that exact image.

## 5. Recommended Production-Style Architecture

A Docker registry stores images and makes them available to deployment
machines. Examples include GHCR, Docker Hub, and Amazon ECR.

### 5.1 Image lifecycle

``` text
GitHub Actions CI -> test/build -> Docker registry (GHCR)
                                      |
GitHub Actions CD on self-hosted laptop <- pull exact image
                                      |
                              Docker Engine / Compose
                                      |
                              New container running
```

### 5.2 Image tags

Do not rely exclusively on `latest` for production deployments.

| Tag | Meaning |
|---|---|
| `latest` | Mutable image reference |
| `v1.0.0` | Release version |
| `a1b2c3d` | Specific Git commit |
| `main` | Often a mutable branch-based reference |

Commit-based tags identify the deployed source version and help with
rollback.

``` bash
docker build -t python-ci-cd:a1b2c3d .
```

### 5.3 CI/CD workflow using GHCR

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
  IMAGE_NAME: ghcr.io/your-github-username/python-ci-cd

jobs:
  ci:
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
    needs: ci
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
          IMAGE_TAG: ${{ needs.ci.outputs.image_tag }}
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

### 5.4 What happens after a push?

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

## 6. Essential Deployment Concepts

### 6.1 Deployment vs. release

| Term | Meaning |
|---|---|
| Deployment | Installing a version into an environment |
| Release | Making that version available to users |

An application can be deployed without immediately being exposed to
users.

### 6.2 Rolling deployment

Gradually replaces old instances with new instances. Useful with
multiple replicas to minimize downtime.

### 6.3 Blue-green deployment

Maintains two environments: **Blue** is active; **Green** contains the
new version. After testing Green, traffic is switched to it.

### 6.4 Rollback

Rollback returns to a previously working version. With image tags, an
older image can be deployed:

``` bash
IMAGE_NAME=ghcr.io/your-github-username/python-ci-cd \
IMAGE_TAG=<previous-commit-sha> \
docker compose up -d --force-recreate
```

### 6.5 Health checks

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

## 7. Laptop-Based Self Hosted Deployment Considerations

### 7.1 Self-hosted runner lifecycle

1.  Open the repository's **Settings → Actions → Runners**.
2.  Select **New self-hosted runner**.
3.  Choose Linux and the appropriate architecture.
4.  Follow GitHub's installation and registration instructions.
5.  Start the runner and confirm it is online.

Target it in a workflow with:

``` yaml
runs-on: [self-hosted, linux, x64]
```

### 7.2 Docker permissions

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

### 7.3 Deployment downtime

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

### 7.4 Keep secrets safe

Never hardcode passwords, access tokens, or private keys in source code,
Dockerfiles, workflow YAML, or Git repositories. Use GitHub Actions
Secrets or another appropriate secret-management solution.

``` yaml
${{ secrets.GITHUB_TOKEN }}
```

## 8. Important Files to Remember

| File | Responsibility |
|---|---|
| `pyproject.toml` | Python project configuration and dependencies |
| `uv.lock` | Dependency version locking |
| `.pre-commit-config.yaml` | Local Git commit checks |
| `Dockerfile` | Image build instructions |
| `compose.yaml` | Container deployment configuration |
| `.dockerignore` | Files excluded from Docker build context |
| `.github/workflows/ci-cd.yaml` | CI/CD automation definition |

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


> **The central principle of CI/CD:** Developers focus on writing code.
> The pipeline automatically validates, packages, and deploys it
> consistently and repeatably.

Once the pipeline works on your laptop, try deploying the same image to
a separate Linux machine. This helps distinguish a CI/CD runner from a
deployment server---an important real-world DevOps concept.
