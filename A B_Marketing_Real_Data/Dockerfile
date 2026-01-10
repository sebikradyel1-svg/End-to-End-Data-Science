# ==============================================================================
# DOCKERFILE - Marketing A/B Testing Analysis
# Project: Statistical Analysis of Marketing Campaign Experiments
# Author: Sebastian Kradyel
# ==============================================================================
# INSTRUCTIONS:
# 1. Rename this file to "Dockerfile" (no extension)
# 2. Place in your project root folder
# 3. Build: docker build -t ab-testing:latest .
# 4. Run: docker run -p 8888:8888 ab-testing:latest
# 5. Open browser: http://localhost:8888
# ==============================================================================

# -----------------------------------------------------------------------------
# STAGE 1: BUILDER
# -----------------------------------------------------------------------------
FROM python:3.10-slim AS builder

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    g++ \
    git \
    libfreetype6-dev \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .

RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# -----------------------------------------------------------------------------
# STAGE 2: RUNTIME
# -----------------------------------------------------------------------------
FROM python:3.10-slim AS runtime

LABEL maintainer="Sebastian Kradyel"
LABEL description="Marketing A/B Testing - Statistical Analysis with Python"
LABEL version="1.0"

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONPATH=/app \
    MPLBACKEND=Agg \
    JUPYTER_TOKEN="" \
    JUPYTER_ENABLE_LAB=yes

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    libgomp1 \
    fonts-liberation \
    libfreetype6 \
    && rm -rf /var/lib/apt/lists/*

# Copy packages from builder
COPY --from=builder /usr/local/lib/python3.10/site-packages /usr/local/lib/python3.10/site-packages
COPY --from=builder /usr/local/bin /usr/local/bin

# Copy application
COPY . .

# Create directories
RUN mkdir -p /app/data /app/outputs /app/reports

# Security: Non-root user
RUN useradd --create-home --shell /bin/bash appuser && \
    chown -R appuser:appuser /app
USER appuser

EXPOSE 8888

HEALTHCHECK --interval=30s --timeout=10s --start-period=15s --retries=3 \
    CMD python -c "import scipy.stats; import pandas; print('ok')" || exit 1

# Start Jupyter Lab
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", "--NotebookApp.token=''"]
