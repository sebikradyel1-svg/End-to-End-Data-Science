# ==============================================================================
# DOCKERFILE - Customer Churn Prediction System
# Project: End-to-End ML Pipeline for Customer Churn Analysis
# Author: Sebastian Kradyel
# ==============================================================================
# INSTRUCTIONS:
# 1. Rename this file to "Dockerfile" (no extension)
# 2. Place in your project root folder
# 3. Build: docker build -t churn-ml:latest .
# 4. Run: docker run -p 8888:8888 churn-ml:latest
# 5. Open browser: http://localhost:8888
# ==============================================================================

# -----------------------------------------------------------------------------
# STAGE 1: BUILDER
# -----------------------------------------------------------------------------
FROM python:3.10-slim AS builder

WORKDIR /app

# System dependencies for data science packages
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
LABEL description="Customer Churn Prediction - ML Classification with Jupyter"
LABEL version="1.0"

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONPATH=/app \
    MPLBACKEND=Agg \
    JUPYTER_TOKEN="" \
    JUPYTER_ENABLE_LAB=yes

WORKDIR /app

# Runtime dependencies for visualization
RUN apt-get update && apt-get install -y --no-install-recommends \
    libgomp1 \
    fonts-liberation \
    libfreetype6 \
    && rm -rf /var/lib/apt/lists/*

# Copy Python packages from builder
COPY --from=builder /usr/local/lib/python3.10/site-packages /usr/local/lib/python3.10/site-packages
COPY --from=builder /usr/local/bin /usr/local/bin

# Copy notebooks and code
COPY . .

# Create directories
RUN mkdir -p /app/data /app/outputs /app/models

# Security: Non-root user
RUN useradd --create-home --shell /bin/bash appuser && \
    chown -R appuser:appuser /app
USER appuser

# Jupyter port
EXPOSE 8888

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=15s --retries=3 \
    CMD python -c "import pandas; import sklearn; print('ok')" || exit 1

# Start Jupyter Lab
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", "--NotebookApp.token=''"]
