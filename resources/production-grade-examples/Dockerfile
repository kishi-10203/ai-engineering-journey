
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
    CMD ["python", "-c", "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health', timeout=3)"]

# Graceful termination signal
STOPSIGNAL SIGTERM

# Start application
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]
