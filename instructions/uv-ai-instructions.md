---
description: 'UV-specific best practices and guidelines for Python package management in AI assistants'
applyTo: '**/*.py, **/pyproject.toml, **/uv.toml, **/uv.lock, **/requirements.txt, **/.python-version'
---

# UV Python Package Manager - Universal AI Assistant Rules & Guidelines

> **Comprehensive steering documentation for AI assistants to effectively use UV (by Astral-sh) - an extremely fast Python package manager and project manager.**

## Table of Contents

1. [Core Principles](#core-principles)
2. [Installation & Setup](#installation--setup)
3. [Project Management Commands](#project-management-commands)
4. [Dependency Management](#dependency-management)
5. [Python Version Management](#python-version-management)
6. [Environment & Virtual Environment Management](#environment--virtual-environment-management)
7. [Tool Management](#tool-management)
8. [Script Execution](#script-execution)
9. [Build & Publishing](#build--publishing)
10. [Pip Interface Compatibility](#pip-interface-compatibility)
11. [Configuration & Optimization](#configuration--optimization)
12. [Error Handling & Troubleshooting](#error-handling--troubleshooting)
13. [Best Practices & Workflows](#best-practices--workflows)
14. [Advanced Usage Patterns](#advanced-usage-patterns)
15. [Integration Guidelines](#integration-guidelines)

## Introduction

This document serves as a universal and comprehensive guide for AI assistants (like Grok) to use, recommend, or simulate the UV CLI—a fast Python package manager developed by Astral. UV streamlines Python project management, including creating environments, resolving dependencies, locking versions, and running scripts. This guide is based on the official UV CLI reference (https://docs.astral.sh/uv/reference/cli/) and incorporates best practices for clarity, safety, and efficiency.

Always prioritize user safety: warn about security risks (e.g., `--allow-insecure-host`), recommend using virtual environments, and suggest checking for updates. When recommending commands, provide explanations, potential outputs, and troubleshooting tips. If simulating UV in code (e.g., via Python scripts), use subprocess calls with proper error handling.

UV is designed to be faster and more reliable than traditional tools like pip and venv. It supports PEP 508 requirements, workspaces, and lockfiles (`uv.lock`) for reproducible environments.

## Global Options

These options apply to most subcommands and can be set via environment variables for persistent configuration.

- `--allow-insecure-host`, `--trusted-host <allow-insecure-host>`: Allow insecure connections to hosts (multiple allowed). **Warning**: Bypasses SSL; use only in trusted networks. Env: `UV_INSECURE_HOST`.
- `--cache-dir <cache-dir>`: Path to cache directory. Default: Platform-specific (e.g., `~/.cache/uv` on Linux). Env: `UV_CACHE_DIR`.
- `--color <color-choice>`: Control output color (`auto`, `always`, `never`). Default: `auto`.
- `--compile-bytecode`, `--compile`: Compile to bytecode post-install for faster startups. Default: False. Env: `UV_COMPILE_BYTECODE`.
- `--config-file <config-file>`: Path to `uv.toml` config. Env: `UV_CONFIG_FILE`.
- `--config-setting`, `--config-settings`, `-C <config-setting>`: PEP 517 build settings as `KEY=VALUE`.
- `--config-settings-package <config-settings-package>`: Package-specific build settings as `PACKAGE:KEY=VALUE`.
- `--default-index <default-index>`: Default index URL. Default: https://pypi.org/simple. Env: `UV_DEFAULT_INDEX`.
- `--directory <directory>`: Change working directory before execution.
- `--exclude-newer <exclude-newer>`: Exclude packages newer than a date (RFC 3339). Env: `UV_EXCLUDE_NEWER`.
- `--extra-index-url <extra-index-url>`: Deprecated; extra index URLs. Env: `UV_EXTRA_INDEX_URL`.
- `--find-links <find-links>`: Additional locations for distributions. Env: `UV_FIND_LINKS`.
- `--fork-strategy <fork-strategy>`: Version selection strategy (`fewest`, `requires-python`). Default: `requires-python`. Env: `UV_FORK_STRATEGY`.
- `--help`, `-h`: Show help.
- `--index <index>`: Resolution index URLs (higher priority). Env: `UV_INDEX`.
- `--index-strategy <index-strategy>`: Multi-index resolution strategy (`first-index`, `unsafe-first-match`, `unsafe-best-match`). Default: `first-index`. Env: `UV_INDEX_STRATEGY`.
- `--index-url`, `-i <index-url>`: Deprecated; main index URL. Env: `UV_INDEX_URL`.
- `--keyring-provider <keyring-provider>`: Authentication method (`disabled`, `subprocess`). Default: `disabled`. Env: `UV_KEYRING_PROVIDER`.
- `--link-mode <link-mode>`: Installation link method (`clone`, `copy`, `hardlink`, `symlink`). Default: Platform-dependent. Env: `UV_LINK_MODE`.
- `--managed-python`: Require UV-managed Python. Env: `UV_MANAGED_PYTHON`.
- `--native-tls`: Use native TLS certificates. Env: `UV_NATIVE_TLS`.
- `--no-binary`: No pre-built wheels; build from source.
- `--no-binary-package <no-binary-package>`: No wheels for specific package.
- `--no-build`: No source builds; use cached wheels.

## Core Principles

### 1. **UV First Philosophy**
- **ALWAYS prefer UV over pip, pipenv, poetry, conda** when working with Python projects
- UV is designed to be a drop-in replacement that's significantly faster
- UV handles Python installation, dependency resolution, virtual environments, and project management

### 2. **Speed & Performance**
- UV is 10-100x faster than traditional tools
- Utilizes Rust-based implementation for performance
- Built-in caching and parallel downloads
- Copy-on-write optimization for package installations

### 3. **Modern Python Standards**
- Follows PEP 517, PEP 518, PEP 621, PEP 723 standards
- Uses `pyproject.toml` for project configuration
- Supports modern Python packaging standards
- Compatible with existing Python ecosystem

## Installation & Setup

### **Rule 1: Installation Detection & Setup**

**Before using UV, always check if it's installed:**

```bash
# Check if UV is installed
uv --version

# If not installed, install UV
curl -LsSf https://astral.sh/uv/install.sh | sh

# Or using pip (if UV not available)
pip install uv

# Or using package managers
# macOS with Homebrew:
brew install uv

# Windows with Chocolatey:
choco install uv

# Linux package managers (check distro-specific packages)
```

**Rule 1.1: Post-Installation Setup**
```bash
# Update shell configuration for tools
uv tool update-shell

# Set up Python executable directory in PATH
uv python update-shell

# View configuration
uv --help
```

## Project Management Commands

### **Rule 2: Project Lifecycle Management**

**2.1: Creating New Projects**

```bash
# Create a new Python project (preferred approach)
uv init my-project
cd my-project

# Create with specific Python version
uv init my-project --python 3.11

# Create as a package (for distribution)
uv init my-project --package

# Create as an application
uv init my-project --app

# Create with specific build backend
uv init my-project --build-backend setuptools

# Create standalone script projects
uv init my-script --script
```

**2.2: Working with Existing Projects**

```bash
# Navigate to existing project and sync
cd existing-project

# Sync dependencies (equivalent to pip install -r requirements.txt)
uv sync

# Sync with specific groups
uv sync --group dev --group test

# Sync without development dependencies
uv sync --no-dev

# Exact sync (remove extraneous packages)
uv sync --exact
```

**2.3: Project Information & Inspection**

```bash
# Show dependency tree
uv tree

# Show project structure
uv tree --depth 2

# Export lockfile to requirements.txt format
uv export --format requirements-txt > requirements.txt

# Export only production dependencies
uv export --no-dev --format requirements-txt > requirements.txt
```

## Dependency Management

### **Rule 3: Adding & Managing Dependencies**

**3.1: Adding Dependencies**

```bash
# Add a dependency (equivalent to pip install + requirements.txt update)
uv add requests

# Add with specific version
uv add requests==2.31.0
uv add "requests>=2.30.0,<3.0.0"

# Add development dependencies
uv add --dev pytest black ruff

# Add to specific groups
uv add --group test pytest coverage
uv add --group docs sphinx mkdocs

# Add optional dependencies
uv add --optional web fastapi uvicorn
uv add --extra dev requests[security]

# Add from specific index
uv add --index https://pypi.org/simple/ requests

# Add editable dependencies (local packages)
uv add --editable ./my-local-package
uv add -e ../sibling-package

# Add from Git repositories
uv add git+https://github.com/user/repo.git
uv add git+https://github.com/user/repo.git@main
uv add git+https://github.com/user/repo.git@v1.2.3

# Add from URLs
uv add https://files.pythonhosted.org/packages/.../package.whl
```

**3.2: Removing Dependencies**

```bash
# Remove dependencies
uv remove requests

# Remove multiple dependencies
uv remove requests urllib3 chardet

# Remove from specific groups
uv remove --dev pytest
uv remove --group test coverage
```

**3.3: Upgrading Dependencies**

```bash
# Upgrade all dependencies
uv sync --upgrade

# Upgrade specific packages
uv sync --upgrade-package requests
uv sync --upgrade-package requests --upgrade-package urllib3

# Upgrade with constraints
uv add --upgrade requests
```

## Python Version Management

### **Rule 4: Python Installation & Version Management**

**4.1: Installing Python Versions**

```bash
# List available Python versions
uv python list

# List installed Python versions
uv python list --only-installed

# Install specific Python versions
uv python install 3.11
uv python install 3.12.1
uv python install pypy3.10

# Install latest version
uv python install 3.12

# Install multiple versions
uv python install 3.11 3.12 pypy3.10
```

**4.2: Python Version Selection**

```bash
# Pin Python version for project
uv python pin 3.11

# Pin to specific patch version
uv python pin 3.11.5

# Pin globally
uv python pin --global 3.12

# Find Python installations
uv python find 3.11
uv python find python3

# Show Python installation directory
uv python dir
```

**4.3: Python Version in Projects**

```bash
# Create project with specific Python version
uv init --python 3.11 my-project

# Change Python version for existing project
cd my-project
uv python pin 3.12
uv sync  # Re-sync with new Python version

# Run with specific Python version
uv run --python 3.11 python script.py
```

## Environment & Virtual Environment Management

### **Rule 5: Virtual Environment Handling**

**5.1: Virtual Environment Creation**

```bash
# Create virtual environment (manual approach)
uv venv

# Create with specific name
uv venv my-env

# Create with specific Python version
uv venv --python 3.11

# Create in specific directory
uv venv /path/to/my-env

# Create with system site packages
uv venv --system-site-packages

# Create relocatable environment
uv venv --relocatable
```

**5.2: Environment Activation & Usage**

```bash
# UV automatically manages environments for projects
# No manual activation needed when using uv run

# For manual activation (if needed):
# On Unix/macOS:
source .venv/bin/activate

# On Windows:
.venv\Scripts\activate

# Deactivate
deactivate
```

**5.3: Environment Information**

```bash
# Show environment info (when using uv pip interface)
uv pip list
uv pip show package-name
uv pip freeze
uv pip check

# Show dependency tree in environment
uv pip tree
```

## Tool Management

### **Rule 6: Global Tool Installation & Management**

**6.1: Installing Tools**

```bash
# Install tools globally (like pipx)
uv tool install ruff
uv tool install black
uv tool install mypy

# Install with specific version
uv tool install ruff==0.1.6

# Install from Git
uv tool install git+https://github.com/astral-sh/ruff.git

# Install with extras
uv tool install ruff[dev]

# Install in editable mode
uv tool install --editable ./my-tool
```

**6.2: Running Tools**

```bash
# Run tool temporarily (uvx alias available)
uv tool run ruff check .
uvx ruff check .

# Run tool with specific version
uv tool run ruff==0.1.6 check .

# Run tool with dependencies
uv tool run --with requests --with urllib3 python -c "import requests"

# Run tool from requirements file
uv tool run --with-requirements requirements.txt python script.py
```

**6.3: Managing Installed Tools**

```bash
# List installed tools
uv tool list

# Show tool details with paths
uv tool list --show-paths

# Show tool dependencies
uv tool list --show-with

# Upgrade tools
uv tool upgrade ruff
uv tool upgrade --all

# Uninstall tools
uv tool uninstall ruff
uv tool uninstall --all

# Show tool directory
uv tool dir
uv tool dir --bin  # Show executable directory
```

## Script Execution

### **Rule 7: Script Execution & Management**

**7.1: Running Scripts**

```bash
# Run Python scripts in project environment
uv run python script.py
uv run script.py  # If script has shebang

# Run with additional arguments
uv run python script.py --arg1 value1 --arg2 value2

# Run with temporary dependencies
uv run --with requests python -c "import requests; print(requests.get('https://httpbin.org/get').json())"

# Run with dependencies from file
uv run --with-requirements requirements.txt python script.py

# Run specific Python version
uv run --python 3.11 python script.py
```

**7.2: PEP 723 Inline Scripts**

```python
#!/usr/bin/env -S uv run
# /// script
# dependencies = [
#     "requests",
#     "rich",
# ]
# ///

import requests
from rich import print

response = requests.get("https://httpbin.org/get")
print(response.json())
```

```bash
# Run PEP 723 script
uv run script.py

# Add dependencies to script
uv add --script script.py beautifulsoup4

# Remove dependencies from script
uv remove --script script.py beautifulsoup4
```

**7.3: Module Execution**

```bash
# Run Python modules
uv run -m pytest
uv run -m pip list  # Though prefer uv pip list
uv run -m black .
uv run -m mypy src/
```

## Build & Publishing

### **Rule 8: Package Building & Distribution**

**8.1: Building Packages**

```bash
# Build package (creates wheel and sdist)
uv build

# Build only wheel
uv build --wheel

# Build only source distribution
uv build --sdist

# Build with specific Python version
uv build --python 3.11

# Build with output directory
uv build --out-dir dist/

# Build with configuration
uv build --config-setting key=value
```

**8.2: Publishing Packages**

```bash
# Publish to PyPI
uv publish

# Publish to specific index
uv publish --index testpypi

# Publish specific files
uv publish dist/*

# Publish with credentials
uv publish --username __token__ --password $PYPI_TOKEN

# Dry run (check without publishing)
uv publish --dry-run
```

**8.3: Version Management**

```bash
# Show current version
uv version

# Update version
uv version patch    # 1.0.0 -> 1.0.1
uv version minor    # 1.0.0 -> 1.1.0
uv version major    # 1.0.0 -> 2.0.0

# Set specific version
uv version 1.2.3

# Show version without project name
uv version --short

# Dry run version update
uv version --dry-run patch
```

## Pip Interface Compatibility

### **Rule 9: Pip-Compatible Commands**

**9.1: Package Installation (uv pip interface)**

```bash
# Install packages (pip-like interface)
uv pip install requests
uv pip install -r requirements.txt
uv pip install -e .  # Editable install

# Install with constraints
uv pip install -c constraints.txt requests

# Install from URLs
uv pip install https://github.com/user/repo/archive/main.zip

# Install with no dependencies
uv pip install --no-deps requests

# Install to specific target
uv pip install --target /path/to/target requests

# Install with pre-release versions
uv pip install --pre requests
```

**9.2: Package Information**

```bash
# Show package information
uv pip show requests

# List installed packages
uv pip list

# List in freeze format
uv pip freeze

# List outdated packages
uv pip list --outdated

# Check environment consistency
uv pip check

# Show dependency tree
uv pip tree
```

**9.3: Compilation & Synchronization**

```bash
# Compile requirements (like pip-tools)
uv pip compile requirements.in

# Compile with output file
uv pip compile requirements.in -o requirements.txt

# Compile for specific Python version
uv pip compile --python-version 3.11 requirements.in

# Compile with constraints
uv pip compile --constraint constraints.txt requirements.in

# Sync environment with lockfile
uv pip sync requirements.txt

# Sync with dry run
uv pip sync --dry-run requirements.txt
```

## Configuration & Optimization

### **Rule 10: Configuration Management**

**10.1: Configuration Files**

UV uses configuration files in this order of precedence:
1. `uv.toml` (project-specific)
2. `pyproject.toml` (project-specific, under `[tool.uv]`)
3. Global configuration files

```toml
# uv.toml or pyproject.toml [tool.uv] section
[tool.uv]
dev-dependencies = [
    "pytest>=7.0",
    "black>=23.0",
    "ruff>=0.1.0",
]

# Default dependency groups
default-groups = ["dev", "test"]

# Package index configuration
index-url = "https://pypi.org/simple/"
extra-index-url = ["https://download.pytorch.org/whl/cpu"]

# Resolution strategy
resolution = "highest"  # or "lowest", "lowest-direct"

# Install configuration
compile-bytecode = true
no-cache = false

# Build configuration
build-constraints = ["setuptools>=40.0"]
```

**10.2: Environment Variables**

```bash
# Common UV environment variables
export UV_CACHE_DIR=/path/to/cache
export UV_CONFIG_FILE=/path/to/uv.toml
export UV_DEFAULT_INDEX=https://pypi.org/simple/
export UV_PYTHON=3.11
export UV_RESOLUTION=highest
export UV_NO_CACHE=true
export UV_VERBOSE=1
```

**10.3: Cache Management**

```bash
# Show cache directory
uv cache dir

# Clean cache
uv cache clean

# Clean specific packages
uv cache clean requests urllib3

# Prune cache (remove unused)
uv cache prune

# Prune for CI optimization
uv cache prune --ci
```

## Error Handling & Troubleshooting

### **Rule 11: Common Error Resolution**

**11.1: Dependency Resolution Issues**

```bash
# Verbose output for debugging
uv add --verbose package-name

# Force refresh packages
uv sync --refresh

# Refresh specific packages
uv sync --refresh-package requests

# Use lowest resolution strategy
uv sync --resolution lowest

# Check for conflicts
uv pip check
```

**11.2: Python Version Issues**

```bash
# Install required Python version
uv python install 3.11

# Force use of managed Python
uv run --python 3.11 python script.py

# Check Python discovery
uv python find 3.11
```

**11.3: Network & Index Issues**

```bash
# Use offline mode
uv sync --offline

# Allow insecure hosts
uv add --allow-insecure-host example.com package

# Use alternative index
uv add --index https://pypi.org/simple/ package

# Disable network access
uv sync --no-network
```

**11.4: Build Issues**

```bash
# Verbose build output
uv build --verbose

# No build isolation
uv pip install --no-build-isolation package

# Force binary packages only
uv pip install --only-binary=all package

# Force source builds
uv pip install --no-binary=all package
```

## Best Practices & Workflows

**Best Practices for AI Usage**:
- **Context Awareness**: Assess the user's project setup (e.g., existing `pyproject.toml`) before suggesting commands.
- **Version Control**: Always recommend pinning versions in `pyproject.toml` for reproducibility.
- **Caching and Performance**: Use `--cache-dir` for custom caching; enable `--compile-bytecode` for production setups.
- **Security**: Avoid `--allow-insecure-host` unless necessary; use official indexes like PyPI.
- **Error Handling**: Advise users to check logs and use `--verbose` for debugging.
- **Integration**: For AIs with code execution capabilities, execute UV via shell commands in isolated environments.
- **Updates**: Remind users to update UV regularly (e.g., via `uv self update`).
- **Limitations**: UV does not install additional packages via pip; stick to built-in libraries for simulations.

### Step-by-Step Workflows

### 1. Creating a New Project
   - **Step 1**: Initialize the project.
     - Command: `uv init [PATH] [OPTIONS]`
     - Example: `uv init my-project --lib` for a library.
   - **Step 2**: Navigate to the project directory.
     - `cd my-project`
   - **Step 3**: Add initial dependencies if needed.
     - See "Adding Dependencies" workflow.
   - **Step 4**: Lock and sync.
     - `uv lock && uv sync`
   - **Best Practice**: Use `--no-workspace` if not adding to an existing workspace.

### 2. Adding Dependencies
   - **Step 1**: Ensure project is initialized with `pyproject.toml`.
   - **Step 2**: Add the package.
     - Command: `uv add <PACKAGES> [OPTIONS]`
     - Example: `uv add requests --dev` for development dependency.
   - **Step 3**: Verify updates to `pyproject.toml` and `uv.lock`.
   - **Step 4**: Sync the environment.
     - `uv sync`
   - **Best Practice**: Use `--editable` for local packages; `--frozen` to skip re-locking.

### 3. Locking Dependencies
   - **Step 1**: Update `pyproject.toml` with dependencies.
   - **Step 2**: Run lock.
     - Command: `uv lock [OPTIONS]`
     - Example: `uv lock --upgrade` to force updates.
   - **Step 3**: Check for changes in `uv.lock`.
   - **Best Practice**: Use `--frozen` for quick checks; `--upgrade-package` for specific updates.

### 4. Syncing the Environment
   - **Step 1**: Ensure `uv.lock` exists.
   - **Step 2**: Sync.
     - Command: `uv sync [OPTIONS]`
     - Example: `uv sync --all-extras`
   - **Step 3**: Verify installed packages with `uv pip list`.
   - **Best Practice**: Use `--inexact` to avoid removing extras; `--frozen` to skip re-locking.

### 5. Running Scripts or Commands
   - **Step 1**: Ensure environment is synced.
   - **Step 2**: Run the command.
     - Command: `uv run [OPTIONS] <COMMAND>`
     - Example: `uv run python script.py`
   - **Best Practice**: Use `--with <package>` for temporary dependencies.

### 6. Creating a Virtual Environment
   - **Step 1**: Specify the path.
     - Command: `uv venv [OPTIONS] [PATH]`
     - Example: `uv venv .venv`
   - **Step 2**: Activate: `source .venv/bin/activate` (Unix) or `.venv\Scripts\activate` (Windows).
   - **Best Practice**: Use `--python <version>` to specify Python version.

### 7. Removing Dependencies
   - **Step 1**: Remove the package.
     - Command: `uv remove <PACKAGES> [OPTIONS]`
     - Example: `uv remove requests`
   - **Step 2**: Sync: `uv sync`.
   - **Best Practice**: Use `--group <group>` for specific groups.

### 8. Exporting Lockfile
   - **Step 1**: Run export.
     - Command: `uv export [OPTIONS]`
     - Example: `uv export --format requirements.txt > requirements.txt`
   - **Step 2**: Use the exported file for other tools.
   - **Best Practice**: Use `--all-extras` for complete exports.

### 9. Managing Python Versions
   - **Step 1**: List available: `uv python list`.
   - **Step 2**: Install: `uv python install <version>`.
   - **Best Practice**: Use UV-managed Pythons for consistency.

### 10. Other Workflows
   - **Building Packages**: `uv build [OPTIONS]`.
   - **Publishing**: `uv publish [OPTIONS] <DISTRIBUTIONS>`.
   - **Cache Management**: `uv cache clean [OPTIONS]`.
   - **Dependency Tree**: `uv tree [OPTIONS]`.
   - **Tool Management**: `uv tool install <tool>`.

### **Rule 12: Recommended Workflows**

**12.1: New Project Workflow**

```bash
# 1. Create new project
uv init my-project --python 3.11
cd my-project

# 2. Add dependencies
uv add fastapi uvicorn
uv add --dev pytest black ruff mypy

# 3. Set up development environment
uv sync

# 4. Run application
uv run python main.py

# 5. Test
uv run pytest

# 6. Format and lint
uv run black .
uv run ruff check .
uv run mypy .

# 7. Build when ready
uv build
```

**12.2: Existing Project Migration**

```bash
# 1. Navigate to project
cd existing-project

# 2. Initialize UV project
uv init --no-readme  # Don't overwrite existing files

# 3. Import dependencies from requirements.txt
uv add -r requirements.txt
uv add --dev -r requirements-dev.txt

# 4. Sync environment
uv sync

# 5. Update scripts to use uv run
# Replace: python script.py
# With: uv run python script.py
```

**12.3: CI/CD Integration**

```bash
# Install UV in CI
curl -LsSf https://astral.sh/uv/install.sh | sh

# Cache optimization for CI
uv cache prune --ci

# Install dependencies
uv sync --frozen  # Use exact lockfile versions

# Run tests
uv run pytest

# Build and publish
uv build
uv publish --username __token__ --password $PYPI_TOKEN
```

**12.4: Development Workflow**

```bash
# Daily development commands
uv sync                    # Update environment
uv add package            # Add new dependency
uv run python script.py   # Run scripts
uv run pytest            # Run tests
uv tool run ruff check .  # Lint code
uv tree                   # Check dependencies
```

## Advanced Usage Patterns

### **Rule 13: Advanced Features**

**13.1: Workspace Management**

```toml
# pyproject.toml for workspace
[tool.uv.workspace]
members = ["packages/*", "apps/*"]
exclude = ["packages/legacy"]

# Workspace dependencies
[tool.uv.workspace.dependencies]
shared-utils = {path = "packages/shared-utils", develop = true}
```

```bash
# Work with workspace packages
uv add --workspace shared-utils
uv sync --workspace
```

**13.2: Dependency Groups**

```toml
# pyproject.toml
[tool.uv]
dev-dependencies = [
    "pytest",
    "black",
    "ruff",
]

[tool.uv.dependency-groups]
test = ["pytest", "coverage"]
docs = ["sphinx", "mkdocs"]
typing = ["mypy", "types-requests"]
```

```bash
# Install specific groups
uv sync --group test
uv sync --group docs --group typing
uv sync --no-group dev  # Exclude dev group
```

**13.3: Lock File Management**

```bash
# Update lock file
uv lock

# Update specific packages in lock
uv lock --upgrade-package requests

# Check if lock is up to date
uv lock --check

# Generate lock without installing
uv lock --dry-run
```

**13.4: Cross-Platform Builds**

```bash
# Build for specific platforms
uv pip compile --platform linux requirements.in
uv pip compile --platform macos requirements.in
uv pip compile --platform windows requirements.in

# Universal builds
uv pip compile --universal requirements.in
```

## Integration Guidelines

### **Rule 14: Tool Integration**

**14.1: IDE Integration**

```bash
# Set up Python interpreter path for IDEs
uv run which python  # Get interpreter path

# For VS Code, update settings.json:
{
    "python.defaultInterpreterPath": ".venv/bin/python",
    "python.terminal.activateEnvironment": false
}
```

**14.2: Docker Integration**

```dockerfile
# Dockerfile with UV
FROM python:3.11-slim

# Install UV
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/uv

# Copy project files
COPY pyproject.toml uv.lock ./

# Install dependencies
RUN uv sync --frozen --no-cache

# Copy application code
COPY . .

# Run application
CMD ["uv", "run", "python", "main.py"]
```

**14.3: GitHub Actions Integration**

```yaml
# .github/workflows/test.yml
name: Test
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install UV
        uses: astral-sh/setup-uv@v2
        with:
          version: "latest"
      
      - name: Install dependencies
        run: uv sync
      
      - name: Run tests
        run: uv run pytest
      
      - name: Cache UV
        uses: actions/cache@v4
        with:
          path: ~/.cache/uv
          key: uv-${{ runner.os }}-${{ hashFiles('uv.lock') }}
```

**14.4: Pre-commit Integration**

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.6
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: local
    hooks:
      - id: uv-lock
        name: uv-lock
        entry: uv lock
        language: system
        files: pyproject.toml
        pass_filenames: false
```

## Subcommand Reference

### uv add
**Description**: Add dependencies to `pyproject.toml`, update lockfile and environment.
**Usage**: `uv add [OPTIONS] <PACKAGES|--requirements <REQUIREMENTS>>`
**Key Arguments**: PACKAGES (PEP 508 specs, e.g., `ruff==0.5.0`).
**Main Options**:
- `--dev`: Add to dev group. Default: False.
- `--editable`: Add as editable.
- `--extra <extra>`: Enable extras (multiple).
- `--group <group>`: Add to specific group.
- `--frozen`: Skip re-locking. Env: `UV_FROZEN`.
- And global options.

### uv init
**Description**: Create a new project with `pyproject.toml`.
**Usage**: `uv init [OPTIONS] [PATH]`
**Key Arguments**: PATH (project directory, default: current).
**Main Options**:
- `--app`, `--application`: For apps (default).
- `--lib`, `--library`: For libraries.
- `--name <name>`: Project name.
- `--no-workspace`: Don't add to parent workspace.
- `--package`: Set up as distributable package.
- And global options.

### uv lock
**Description**: Update or create `uv.lock`.
**Usage**: `uv lock [OPTIONS]`
**Key Arguments**: None.
**Main Options**:
- `--upgrade`: Force update.
- `--frozen`: Assert lockfile exists without update. Env: `UV_FROZEN`.
- `--locked`: Check if up-to-date. Env: `UV_LOCKED`.
- `--dry-run`: Simulate without writing.
- And global options.

### uv sync
**Description**: Update environment to match lockfile.
**Usage**: `uv sync [OPTIONS]`
**Key Arguments**: None.
**Main Options**:
- `--all-extras`: Include all extras. Env: `UV_ALL_EXTRAS`.
- `--all-groups`: Include all groups. Env: `UV_ALL_GROUPS`.
- `--frozen`: Use existing lockfile. Env: `UV_FROZEN`.
- `--inexact`: Don't remove undeclared packages.
- `--dry-run`: Simulate.
- And global options.

### uv run
**Description**: Run a command or script in the project environment.
**Usage**: `uv run [OPTIONS] <COMMAND> [ARGS...]`
**Key Arguments**: COMMAND (e.g., `python script.py`).
**Main Options**:
- `--with <package>`: Add temporary dependency.
- `--isolated`: Run in isolated environment.
- And global options.

### uv venv
**Description**: Create a virtual environment.
**Usage**: `uv venv [OPTIONS] [PATH]`
**Key Arguments**: PATH (env directory, default: `.venv`).
**Main Options**:
- `--python <version>`: Specify Python version.
- `--seed`: Install seed packages (pip, setuptools, wheel).
- And global options.

### uv remove
**Description**: Remove dependencies from `pyproject.toml`.
**Usage**: `uv remove [OPTIONS] <PACKAGES>`
**Key Arguments**: PACKAGES.
**Main Options**:
- `--group <group>`: Remove from specific group.
- `--frozen`: Skip re-locking.
- And global options.

### uv tree
**Description**: Display dependency tree.
**Usage**: `uv tree [OPTIONS]`
**Key Arguments**: None.
**Main Options**:
- `--depth <depth>`: Max display depth.
- And global options.

### uv tool
**Description**: Run and install tools from packages.
**Usage**: `uv tool [SUBCOMMAND]`
**Subcommands**: install, run, list, etc.
**Main Options**: Vary by subcommand; see global.

### uv python
**Description**: Manage Python installations.
**Usage**: `uv python [SUBCOMMAND]`
**Subcommands**: install, list, find, dir.
**Main Options**: Vary; e.g., `--all` for list.

### uv cache
**Description**: Manage cache.
**Usage**: `uv cache [SUBCOMMAND]`
**Subcommands**: clean, dir, prune.
**Main Options**: `--size` for dir.

### uv build
**Description**: Build packages into SDists/wheels.
**Usage**: `uv build [OPTIONS] [PATH]`
**Key Arguments**: PATH (default: current).
**Main Options**: `--wheel`, `--sdist`.

### uv publish
**Description**: Upload distributions.
**Usage**: `uv publish [OPTIONS] <DISTRIBUTIONS...>`
**Key Arguments**: DISTRIBUTIONS (files).
**Main Options**: `--repository <repo>`.

### uv self
**Description**: Manage UV executable.
**Usage**: `uv self [SUBCOMMAND]`
**Subcommands**: update.

### uv version
**Description**: Display or update project version.
**Usage**: `uv version [OPTIONS]`
**Main Options**: `--set <version>`.

### uv help
**Description**: Display help for commands.
**Usage**: `uv help [COMMAND]`.

### uv pip
**Description**: Pip-compatible interface.
**Usage**: `uv pip [SUBCOMMAND]`
**Subcommands**: install, uninstall, list, etc.
**Main Options**: Similar to pip.

### uv export
**Description**: Export lockfile to other formats (e.g., requirements.txt).
**Usage**: `uv export [OPTIONS]`
**Key Arguments**: None.
**Main Options**:
- `--format <format>`: e.g., `requirements.txt`.
- `--all-extras`: Include extras.
- And global options.

## Final Guidelines for AI Assistants

### **Rule 15: Decision Making Framework**

**When to use UV commands:**

1. **Project Setup**: Always use `uv init` for new projects
2. **Dependency Management**: Use `uv add/remove` instead of editing files manually
3. **Environment Management**: Let UV handle virtual environments automatically
4. **Script Execution**: Always use `uv run` for Python scripts in projects
5. **Tool Installation**: Use `uv tool install` for global tools
6. **Python Management**: Use `uv python install/pin` for Python versions

**Command Selection Priority:**

1. **High-level commands first**: Prefer `uv add` over `uv pip install`
2. **Project context**: Use project commands (`uv run`, `uv sync`) when in a project
3. **Global context**: Use tool commands (`uv tool run`, `uvx`) for one-off tools
4. **Legacy compatibility**: Use `uv pip` interface only when needed for compatibility

**Error Recovery Strategy:**

1. **Try verbose mode**: Add `--verbose` to understand issues
2. **Check cache**: Use `uv cache clean` if packages seem corrupted
3. **Refresh dependencies**: Use `--refresh` flags to update metadata
4. **Check Python version**: Ensure compatible Python version is available
5. **Fallback to pip interface**: Use `uv pip` commands if high-level commands fail

### **Common Patterns to Remember:**

- **Always check UV installation first**
- **Prefer `uv run` over direct Python execution in projects**
- **Use `uv sync` to ensure environment consistency**
- **Let UV manage Python versions when possible**
- **Use `uv tool run` (or `uvx`) for temporary tool execution**
- **Keep lockfiles committed to version control**
- **Use dependency groups for organizing requirements**
- **Leverage UV's speed advantages in CI/CD pipelines**

## Quick Reference Card

```bash
# Essential UV Commands Quick Reference

# Project Management
uv init my-project              # Create new project
uv sync                        # Install/update dependencies
uv add package                 # Add dependency
uv remove package              # Remove dependency
uv run python script.py        # Run script in project

# Python Management
uv python install 3.11         # Install Python version
uv python pin 3.11            # Pin project to Python version
uv python list                 # List Python versions

# Tool Management
uv tool install ruff           # Install global tool
uv tool run ruff check .       # Run tool temporarily
uvx ruff check .              # Shorthand for tool run

# Environment Management
uv venv                        # Create virtual environment
uv pip install package        # Pip-compatible interface

# Utility
uv cache clean                 # Clean cache
uv tree                       # Show dependency tree
uv build                      # Build package
uv publish                    # Publish package
```

**Remember: UV is designed to be fast, reliable, and compatible with existing Python workflows while providing modern project management capabilities. When in doubt, consult the official documentation at https://docs.astral.sh/uv/ and always use UV's built-in help with `uv --help` or `uv command --help`.**

This guide ensures AIs can provide accurate, step-by-step assistance with UV. For advanced usage, refer users to the official docs.
