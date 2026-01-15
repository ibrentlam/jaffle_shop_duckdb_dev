# Use Microsoft's Python 3.11 devcontainer base image
FROM mcr.microsoft.com/devcontainers/python:3.11

# Set environment variables for Python
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Install system dependencies
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    git \
    curl \
    unzip \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/*

# Install DuckDB CLI
RUN curl -L -o /tmp/duckdb_cli.zip https://github.com/duckdb/duckdb/releases/latest/download/duckdb_cli-linux-amd64.zip \
    && unzip /tmp/duckdb_cli.zip -d /tmp \
    && mv /tmp/duckdb /usr/local/bin/duckdb \
    && chmod +x /usr/local/bin/duckdb \
    && rm /tmp/duckdb_cli.zip \
    && duckdb --version

# Copy requirements file
COPY requirements.txt /tmp/requirements.txt

# Upgrade pip and install Python dependencies
RUN pip install --upgrade pip setuptools wheel \
    && pip install -r /tmp/requirements.txt \
    && rm /tmp/requirements.txt

# Verify installations
RUN dbt --version

# Set working directory
WORKDIR /workspaces/jaffle_shop_duckdb_dev
