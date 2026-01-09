# Dev Container Configuration

This directory contains the development container configuration for the Jaffle Shop dbt project. The dev container provides a consistent, reproducible development environment with all necessary dependencies pre-installed.

## What's Included

The dev container includes:

- **Python 3.11**: The base Python runtime
- **dbt-core 1.9.4**: The dbt transformation framework
- **dbt-duckdb 1.9.1**: DuckDB adapter for dbt
- **SQLFluff**: SQL linter for code quality
- **duckcli**: Command-line interface for DuckDB
- **Git**: Version control
- **VS Code Extensions**:
  - dbt Power User (`bastienboutonnet.vscode-dbt`)
  - SQLFluff linter (`dorzey.vscode-sqlfluff`)
  - EditorConfig (`editorconfig.editorconfig`)
  - Find Related (`amodio.find-related`)
  - Docker (`ms-azuretools.vscode-docker`)
  - Python (`ms-python.python`)
  - IntelliCode (`visualstudioexptteam.vscodeintellicode`)
  - Jinja (`samuelcolvin.jinjahtml`)
  - YAML (`redhat.vscode-yaml`)

## Prerequisites

### Using VS Code Dev Containers

1. **Docker Desktop**: Install [Docker Desktop](https://www.docker.com/products/docker-desktop/)
2. **VS Code**: Install [Visual Studio Code](https://code.visualstudio.com/)
3. **Dev Containers Extension**: Install the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

### Using GitHub Codespaces

1. **GitHub Account**: Ensure you have access to [GitHub Codespaces](https://github.com/features/codespaces)
2. No local installation required - everything runs in the cloud

### Using DevContainer CLI

1. **DevContainer CLI**: The `devcontainer` command-line tool
2. **Docker**: A running Docker daemon

## Getting Started

### Option 1: VS Code Dev Containers

1. Clone this repository
2. Open the project folder in VS Code
3. When prompted, click "Reopen in Container" (or use Command Palette: `Dev Containers: Reopen in Container`)
4. Wait for the container to build and start (first time may take several minutes)
5. Once ready, open a terminal and run:
   ```bash
   dbt build
   ```

### Option 2: GitHub Codespaces

1. Navigate to the repository on GitHub
2. Click the green "Code" button
3. Select the "Codespaces" tab
4. Click "Create codespace on duckdb"
5. Wait for the codespace to initialize
6. The postCreateCommand will automatically run `dbt --version` and `dbt debug`
7. Open a terminal and run:
   ```bash
   dbt build
   ```

### Option 3: DevContainer CLI

Build the container:
```bash
devcontainer build --workspace-folder .
```

Start the container:
```bash
devcontainer up --workspace-folder .
```

Execute commands in the container:
```bash
devcontainer exec --workspace-folder . dbt build
```

Stop the container:
```bash
docker stop <container-id>
```

## Configuration Files

### `devcontainer.json`

The main configuration file that defines:
- Container build settings
- VS Code settings and extensions
- Post-creation commands
- User and workspace configuration

Key settings:
- **build.dockerfile**: Points to the Dockerfile
- **build.context**: Sets the build context to the parent directory
- **customizations.vscode**: Configures VS Code settings and extensions
- **postCreateCommand**: Runs `dbt --version && dbt debug` after container creation
- **remoteUser**: Sets the container user to `vscode`

### `Dockerfile`

Defines the container image with:
- Base image: `mcr.microsoft.com/devcontainers/python:3.11`
- System dependencies (git)
- Python dependencies from `requirements.txt`
- Working directory: `/workspaces/jaffle_shop_duckdb_dev`

## Development Workflow

1. **Start the container**: Use one of the methods above
2. **Verify setup**: Run `dbt debug` to check the configuration
3. **Run dbt commands**:
   ```bash
   dbt seed      # Load CSV data
   dbt run       # Run models
   dbt test      # Run tests
   dbt build     # Run everything (seed + run + test)
   dbt docs generate && dbt docs serve  # Generate and view documentation
   ```
4. **Query the database**:
   ```bash
   duckcli jaffle_shop.duckdb
   ```

## Features

### SQL Linting

SQLFluff is configured to lint your SQL files as you type. Errors appear:
- Underlined in red in the editor
- Listed in the VS Code Problems panel

### dbt File Navigation

Use `CMD+R` (Mac) or `CTRL+R` (Windows/Linux) to jump between:
- Model files (`models/`)
- Compiled SQL (`target/compiled/`)
- Run SQL (`target/run/`)

### YAML Schema Validation

YAML files automatically validate against dbt's JSON schemas, providing:
- Autocomplete for property names
- Validation of property values
- Inline documentation

## Troubleshooting

### Container Build Fails

If the container fails to build:
1. Check your Docker installation: `docker --version`
2. Ensure Docker daemon is running
3. Try rebuilding: `Dev Containers: Rebuild Container` (VS Code) or `devcontainer build --no-cache`

### DuckDB Lock Error

If you see: `IO Error: Could not set lock on file "jaffle_shop.duckdb"`

This means another process is using the database:
1. Close any DuckDB connections (DBeaver, duckcli, etc.)
2. In worst case, delete `jaffle_shop.duckdb` (you'll lose data but can rebuild with `dbt build`)

### dbt Version Mismatch

If you see version mismatch warnings:
- The container uses dbt-core 1.9.4 as specified in `requirements.txt`
- This is compatible with the project requirements (>= 1.0.0, < 2.0.0)
- To update, modify `requirements.txt` and rebuild the container

## Customization

### Adding Python Packages

1. Edit `requirements.txt` in the project root
2. Rebuild the container

### Adding VS Code Extensions

1. Edit `.devcontainer/devcontainer.json`
2. Add extension IDs to the `extensions` array
3. Rebuild the container

### Modifying VS Code Settings

1. Edit `.devcontainer/devcontainer.json`
2. Update the `customizations.vscode.settings` section
3. Reload the window or rebuild the container

## Additional Resources

- [VS Code Dev Containers Documentation](https://code.visualstudio.com/docs/devcontainers/containers)
- [GitHub Codespaces Documentation](https://docs.github.com/en/codespaces)
- [dbt Documentation](https://docs.getdbt.com/)
- [DuckDB Documentation](https://duckdb.org/docs/)
- [DevContainer CLI](https://github.com/devcontainers/cli)
