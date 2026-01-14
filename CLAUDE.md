# Claude AI Context File

This file provides context for Claude AI when working on this project. It contains project structure, development setup, key decisions, and helpful information for future sessions.

## Project Overview

**Project Name**: Jaffle Shop (DuckDB variant)
**Type**: dbt (data build tool) demo project
**Database**: DuckDB (embedded analytical database)
**Purpose**: Self-contained playground for testing dbt with DuckDB

This is a fictional e-commerce store that demonstrates dbt transformation patterns. It transforms raw data (customers, orders, payments) into analytics-ready models.

## Key Project Details

- **dbt Version**: 1.9.4 (requirement: >=1.0.0, <2.0.0)
- **Python Version**: 3.11.14 (host) / 3.11 (dev container)
- **Database File**: `jaffle_shop.duckdb` (created by dbt, git-ignored)
- **Profile**: `jaffle_shop` (configured in `profiles.yml`)
- **Target**: `dev` (uses DuckDB with 24 threads)

## Project Structure

```
jaffle_shop_duckdb_dev/
├── .devcontainer/          # Dev container configuration (added 2026-01-09)
│   ├── devcontainer.json   # VS Code dev container config
│   ├── Dockerfile          # Container image definition
│   └── README.md           # Comprehensive dev container docs
├── models/                 # dbt models (SQL with Jinja)
│   ├── staging/           # Raw data transformations (views)
│   │   ├── stg_customers.sql
│   │   ├── stg_orders.sql
│   │   └── stg_payments.sql
│   ├── customers.sql      # Final customer model (table)
│   ├── orders.sql         # Final orders model (table)
│   └── schema.yml         # Model tests and documentation
├── seeds/                 # CSV files loaded as tables
│   ├── raw_customers.csv
│   ├── raw_orders.csv
│   └── raw_payments.csv
├── tests/                 # Custom data tests
├── macros/                # Reusable Jinja macros
├── analysis/              # Ad-hoc analytical queries
├── target/                # dbt build output (git-ignored)
├── dbt_project.yml        # dbt project configuration
├── profiles.yml           # Database connection config
├── requirements.txt       # Python dependencies (pip-compiled)
├── requirements-dev.txt   # Development dependencies
└── README.md             # Main project documentation
```

## Dev Container Setup (Added 2026-01-09)

### What Was Created

A complete dev container configuration in `.devcontainer/` directory:

**Files**:
1. `devcontainer.json` - VS Code configuration with 9 pre-installed extensions
2. `Dockerfile` - Python 3.11 + dbt-core 1.9.4 + dbt-duckdb 1.9.1 + DuckDB CLI
3. `README.md` - Comprehensive documentation (325+ lines)

**Key Decisions**:
- **Python 3.11**: Matches host environment (3.11.14)
- **Base Image**: `mcr.microsoft.com/devcontainers/python:3.11`
- **DuckDB CLI**: Standalone binary installed in `/usr/local/bin/duckdb` (latest version)
- **Working Directory**: `/workspaces/jaffle_shop_duckdb_dev`
- **Post-Create Command**: `dbt --version && dbt debug`
- **Remote User**: `vscode`

### Verification Results

Container tested and verified:
```bash
devcontainer build --workspace-folder .   # ✓ Success
devcontainer up --workspace-folder .      # ✓ Success
devcontainer exec ... dbt build           # ✓ All 28 tests pass
```

Build metrics:
- 3 seeds loaded (customers, orders, payments)
- 5 models created (3 views, 2 tables)
- 20 data tests passed
- Build time: ~1 second

## Important Files

### Configuration Files

- **`dbt_project.yml`**: Project metadata, model materialization defaults
- **`profiles.yml`**: Database connection (DuckDB path, threads)
- **`requirements.txt`**: Pin-compiled Python dependencies (Python 3.12 compatible)
- **`.devcontainer.json`** (root): OLD config, superseded by `.devcontainer/` directory
- **`Dockerfile`** (root): OLD config (Python 3.9), superseded by `.devcontainer/Dockerfile`

### Legacy Files

The root directory contains old dev container files that are now superseded:
- `.devcontainer.json` (root) - references old Dockerfile, uses Python 3.9
- `Dockerfile` (root) - old container config

These were NOT removed to preserve backward compatibility, but new setup should use `.devcontainer/` directory.

## Development Workflow

### Standard Commands

```bash
# Full build (recommended)
dbt build

# Individual steps
dbt seed      # Load CSV data
dbt run       # Run models
dbt test      # Run tests

# Documentation
dbt docs generate
dbt docs serve

# Database queries (using standalone DuckDB CLI)
duckdb jaffle_shop.duckdb
duckdb jaffle_shop.duckdb -c "SELECT * FROM customers LIMIT 5"

# Database queries (using Python-based duckcli)
duckcli jaffle_shop.duckdb
duckcli jaffle_shop.duckdb -e "SELECT * FROM customers LIMIT 5"
```

### VS Code Extensions Configured

Pre-installed in dev container:
1. `bastienboutonnet.vscode-dbt` - dbt Power User
2. `dorzey.vscode-sqlfluff` - SQL linting
3. `editorconfig.editorconfig` - EditorConfig support
4. `amodio.find-related` - Navigate between model/compiled/run files
5. `ms-azuretools.vscode-docker` - Docker support
6. `ms-python.python` - Python language support
7. `visualstudioexptteam.vscodeintellicode` - AI-assisted IntelliSense
8. `samuelcolvin.jinjahtml` - Jinja template support
9. `redhat.vscode-yaml` - YAML language support

### VS Code Features

- **SQL Linting**: SQLFluff runs on type, errors in Problems panel
- **Quick Navigation**: CMD+R / CTRL+R to jump between model/compiled/run
- **YAML Validation**: Autocomplete and validation for dbt YAML files
- **Jinja Syntax**: Highlighting and autocomplete for dbt macros

## Common Issues and Solutions

### DuckDB Lock Error

**Error**: `IO Error: Could not set lock on file "jaffle_shop.duckdb"`

**Cause**: Another process has the database open (DBeaver, duckcli, etc.)

**Solution**:
1. Close all connections to the database
2. In worst case: `rm jaffle_shop.duckdb` and run `dbt build`

### Version Mismatch Warning

**Warning**: "Unable to do partial parsing because of a version mismatch"

**Cause**: Different dbt versions between runs (e.g., host vs container)

**Impact**: Minimal - dbt will do full parse instead of partial parse

**Solution**: Use consistent environment (always use dev container)

### Python Version Note

**requirements.txt** was compiled with Python 3.12 but works with Python 3.11:
- Compiled annotation: `# This file is autogenerated by pip-compile with Python 3.12`
- Dev container uses: Python 3.11.14
- Status: Compatible, no issues encountered

## Git Information

**Main Branch**: `duckdb`
**Last Commit** (before dev container): `db6bffa` - "Merge pull request #87"
**Dev Container Commit**: `158eff5` - "Add dev container configuration for dbt development"

**Ignored Files** (relevant):
- `target/` - dbt build output
- `*.duckdb` - Database files
- `*.duckdb.wal` - Database write-ahead log
- `dbt_packages/` - Installed dbt packages
- `logs/` - dbt logs
- `.user.yml` - User-specific dbt config

## Testing Checklist

When making changes, verify:

```bash
# 1. dbt debug passes
dbt debug

# 2. Full build succeeds
dbt build

# Expected output:
# - PASS=28 WARN=0 ERROR=0 SKIP=0 TOTAL=28
# - Completed successfully

# 3. In dev container
devcontainer build --workspace-folder .
devcontainer up --workspace-folder .
devcontainer exec --workspace-folder . dbt build
```

## Data Model

### Seeds (Raw Data)
- `raw_customers` (100 rows) - Customer master data
- `raw_orders` (99 rows) - Order transactions
- `raw_payments` (113 rows) - Payment records

### Staging Models (Views)
- `stg_customers` - Cleaned customer data
- `stg_orders` - Cleaned order data
- `stg_payments` - Cleaned payment data

### Final Models (Tables)
- `customers` - Customer analytics (lifetime value, order counts)
- `orders` - Order analytics (payment breakdowns by method)

### Tests
- 20 data tests covering:
  - Uniqueness (primary keys)
  - Not null (required fields)
  - Accepted values (status, payment methods)
  - Referential integrity (foreign keys)

## Performance Notes

This project is optimized for speed:
- DuckDB is an embedded database (no network overhead)
- Small dataset (< 500 rows total)
- Build completes in ~1 second
- 24 threads configured for parallel execution

## Documentation

**Main Documentation**:
- `README.md` - User-facing documentation
- `.devcontainer/README.md` - Dev container comprehensive guide
- `DEVCONTAINER_SETUP.md` - Dev container setup summary

**dbt Docs**:
```bash
dbt docs generate  # Creates documentation
dbt docs serve     # Serves on http://localhost:8080
```

## Future Considerations

### Old Configuration Files

Consider in future:
- Remove or archive root-level `.devcontainer.json` and `Dockerfile`
- Update any CI/CD that might reference old files
- Verify GitHub Codespaces uses new `.devcontainer/` directory

### Python Version Upgrade

If upgrading Python:
1. Update `.devcontainer/Dockerfile` base image
2. Consider recompiling `requirements.txt` with target Python version
3. Test thoroughly with `dbt build`

### dbt Version Upgrade

If upgrading dbt:
1. Update `requirements.txt`
2. Check `dbt_project.yml` for `require-dbt-version`
3. Review dbt migration guide for breaking changes
4. Rebuild dev container and test

## Quick Reference Commands

```bash
# Dev Container
devcontainer build --workspace-folder .
devcontainer up --workspace-folder .
devcontainer exec --workspace-folder . <command>

# dbt
dbt debug                  # Verify configuration
dbt build                  # Full build
dbt run --select <model>   # Run specific model
dbt test --select <model>  # Test specific model
dbt docs generate          # Generate docs
dbt docs serve             # Serve docs

# DuckDB (standalone CLI)
duckdb jaffle_shop.duckdb                    # Interactive CLI
duckdb jaffle_shop.duckdb -c "<SQL>"        # Execute SQL
echo "<SQL>" | duckdb jaffle_shop.duckdb    # Pipe SQL

# DuckDB (Python-based duckcli)
duckcli jaffle_shop.duckdb                    # Interactive CLI (enhanced)
duckcli jaffle_shop.duckdb -e "<SQL>"        # Execute SQL
echo "<SQL>" | duckcli jaffle_shop.duckdb    # Pipe SQL

# Git
git status                 # Check status
git add <files>           # Stage files
git commit -m "message"   # Commit
git push                  # Push to remote
```

## Contact and Resources

- **dbt Documentation**: https://docs.getdbt.com/
- **DuckDB Documentation**: https://duckdb.org/docs/
- **Dev Containers**: https://code.visualstudio.com/docs/devcontainers/containers
- **GitHub Repo**: https://github.com/dbt-labs/jaffle_shop_duckdb

---

**Last Updated**: 2026-01-14
**Updated By**: Claude Sonnet 4.5
**Session Context**: Added DuckDB CLI standalone binary to dev container
