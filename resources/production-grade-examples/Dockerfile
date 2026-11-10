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
