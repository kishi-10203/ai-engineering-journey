# UV – Python's Modern Package and Project Manager

## 1. What Is `uv`?

`uv` is a modern Python package and project manager developed by Astral and written in Rust.

It combines multiple Python development tools into one unified command-line interface. Traditionally, developers use separate tools to manage Python versions, virtual environments, dependencies, and packages. `uv` brings these workflows together.

> *Official documentation:* [uv Docs](https://docs.astral.sh/uv/)

---
## 2. Why Do We Need `uv`?

Imagine building an AI application using:

- Python 3.12
- FastAPI
- LangChain
- LangGraph
- OpenAI SDK
- PostgreSQL
- Pydantic

A project like this requires you to manage several things:

- Which Python version should the project use?
- How do you isolate project dependencies?
- How do you install packages?
- How do you manage package versions?
- How can another developer recreate your environment?
- How do you execute your application?

Traditionally, multiple tools might be needed to accomplish these tasks. `uv` simplifies the workflow by providing a unified interface.

---
## 3. `uv` Commands – Cheat Sheet

| Command | Purpose |
|---|---|
| `uv --version` | Check the `uv` version |
| `uv init` | Initialize a project |
| `uv python list` | List available Python versions |
| `uv python install 3.12` | Install Python |
| `uv python pin 3.12` | Pin a Python version |
| `uv venv` | Create a virtual environment |
| `uv add package` | Add a project dependency |
| `uv remove package` | Remove a project dependency |
| `uv pip install package` | Install a package into an environment |
| `uv lock` | Generate or update the lockfile |
| `uv sync` | Synchronize the project environment |
| `uv run main.py` | Execute a Python script |
| `uv tree` | Display the dependency tree |
| `uvx ruff check .` | Run a standalone tool |
| `uv tool install ruff` | Install a CLI tool |
| `uv build` | Build a Python package |
| `uv publish` | Publish a Python package |
| `uv cache clean` | Clear the `uv` cache |
| `uv self update` | Update `uv` |

---
## 4. Traditional Tools vs. `uv`

| Traditional tool | Purpose | `uv` equivalent |
|---|---|---|
| Python / pyenv | Python version management | `uv python` |
| venv / virtualenv | Virtual environment management | `uv venv` |
| pip | Package installation | `uv pip` / `uv add` |
| pip-tools | Dependency locking | `uv lock` |
| pipx | Running standalone CLI tools | `uvx` / `uv tool` |
| Poetry | Project and dependency management | `uv init`, `uv add`, `uv sync` |
| build | Building Python packages | `uv build` |
| twine | Publishing Python packages | `uv publish` |

> *Note:* These are functional comparisons, not exact one-to-one replacements in every workflow.

---
## 5. Major Functionalities of `uv`

> ### 5.1 Python Version Management

`uv` can download, install, discover, and manage multiple Python versions.

For example:

- Project A requires Python 3.10.
- Project B requires Python 3.11.
- Project C requires Python 3.12.

You can manage these versions using `uv` without manually managing separate Python installations.

| Command | Purpose |
|---|---|
| `uv python list` | List available Python versions |
| `uv python install 3.12` | Install Python 3.12 |
| `uv python install 3.10 3.11 3.12` | Install multiple Python versions |
| `uv python find 3.12` | Find an installed Python version |
| `uv python pin 3.12` | Pin a Python version for a project |
| `uv python uninstall 3.10` | Uninstall Python 3.10 |

#### Pinning a Python Version

```bash
uv python pin 3.12
```

This creates a file named `.python-version` containing:

```text
3.12
```

*This tells `uv` which Python version to use for the project.*

> ### 5.2 Virtual Environment Management

A virtual environment creates an isolated Python environment for each project. Dependencies installed in one environment do not automatically become available in another.

| Command | Purpose |
|---|---|
| `uv venv` | Create a virtual environment |
| `uv venv --python 3.12` | Create a virtual environment with Python 3.12 |
| `uv venv myenv` | Create a virtual environment with a custom name |

#### Activating the Environment

**Linux/macOS:**

```bash
source .venv/bin/activate
```

**Windows PowerShell:**

```powershell
.venv\Scripts\Activate.ps1
```

You can also execute commands without manually activating the environment:

```bash
uv run python main.py
```

`uv` automatically uses the project's environment.

> ### 5.3 Package Management

Package management is one of the most important functionalities of `uv`.

Traditionally, packages are installed using `pip`:

```bash
pip install fastapi
pip install pydantic
pip install langchain
```

With `uv`, there are two approaches.

#### Approach A: Project-Based Dependency Management

| Command | Purpose |
|---|---|
| `uv add fastapi` | Add a dependency to the project |
| `uv add fastapi pydantic langchain` | Add multiple dependencies to the project |
| `uv remove fastapi` | Remove a dependency from the project |

`uv` automatically updates:

- `pyproject.toml`
- `uv.lock`

#### Approach B: pip-Compatible Package Management

| Command | Purpose |
|---|---|
| `uv pip install fastapi` | Install a package |
| `uv pip install fastapi pydantic` | Install multiple packages |
| `uv pip list` | List installed packages |
| `uv pip show fastapi` | Show package information |
| `uv pip uninstall fastapi` | Uninstall a package |
| `uv pip check` | Check dependency compatibility |

#### Difference Between `uv add` and `uv pip install`

| `uv add` | `uv pip install` |
|---|---|
| Project-oriented | Environment-oriented |
| Updates `pyproject.toml` | Does not update project dependencies |
| Updates the project lockfile | Does not automatically update `uv.lock` |
| Recommended for managed projects | Useful for existing `pip` workflows |

> *Recommendation:* Use `uv add` for new projects managed by `uv`.

> ### 5.4 Dependency Management and Locking

Dependency locking is particularly important when working in teams.

Imagine installing a package:

```bash
pip install langchain
```

Today, this may install one version. A few weeks later, another developer may install a newer version, which can cause compatibility issues.

`uv` helps address this using a lockfile named `uv.lock`.

#### What Is a Lockfile?

A lockfile records the exact resolved dependency versions and other information required to reproduce a project's dependency installation.

For example, a project might declare:

```text
fastapi
pydantic
langchain
```

The lockfile records the resolved versions of these packages and their transitive dependencies.

| Command | Purpose |
|---|---|
| `uv lock` | Generate or update the lockfile |
| `uv sync` | Install dependencies based on the lockfile |
| `uv tree` | View the dependency tree |
| `uv export` | Export project dependencies |

#### Important Files

| File | Purpose |
|---|---|
| `pyproject.toml` | Describes project metadata and declared dependencies |
| `uv.lock` | Records exact resolved dependency information |
| `.venv/` | Contains the installed project environment |

Understanding the difference helps you manage dependencies, maintain reproducible environments, and ensure consistent installations across machines.

For example, after pushing a project to GitHub, another developer can clone the repository and execute:

```bash
uv sync
```

`uv` recreates the project environment using the declared dependencies and lockfile.

> *Recommendation:* Commit both `pyproject.toml` and `uv.lock` to GitHub.

> ### 5.5 Running Python Scripts

Normally, a Python file is executed using:

```bash
python main.py
```

With `uv`:

```bash
uv run main.py
```

`uv` can automatically manage the project's environment and dependencies before executing the script.

`uv` also supports defining dependencies directly inside standalone Python scripts. It handles the script's dependencies in an isolated environment.

#### Use Cases

- Quick experiments
- Automation scripts
- Prototypes
- Small standalone Python programs

> ### 5.6 Running Developer Tools

Developers frequently need tools such as Ruff. Installing these tools globally can sometimes create dependency conflicts.

`uv` provides isolated environments for standalone command-line tools.

| Command | Purpose |
|---|---|
| `uv tool list` | List installed tools |
| `uv tool uninstall ruff` | Uninstall a tool |
| `uv tool upgrade --all` | Upgrade all installed tools |

> ### 5.7 Building and Publishing Python Packages

Suppose you develop a reusable Python library containing custom RAG utilities and want to distribute it so other developers can install it.

`uv` supports building the package:

```bash
uv build
```

This generates distribution files, generally:

- `.whl` — Wheel distribution
- `.tar.gz` — Source distribution

To publish a package:

```bash
uv publish
```

This uploads the package to a configured package index, such as PyPI.

> *Note:* Appropriate credentials and publishing configuration are required.

> ### 5.8 Cache Management

`uv` maintains a global cache to avoid repeatedly downloading and building the same dependencies. This improves installation speed and reduces redundant storage.

`uv` uses efficient package caching and reuse, so separate project environments do not necessarily require separate full copies of every dependency.

| Command | Purpose |
|---|---|
| `uv cache dir` | Show the `uv` cache directory |
| `uv cache prune` | Remove unused cache entries |
| `uv cache clean` | Clear the `uv` cache |

---

## 6. Difference Between `pyproject.toml` and `uv.lock`

| Feature | `pyproject.toml` | `uv.lock` |
|---|---|---|
| Purpose | Defines project configuration and metadata | Records exact resolved dependency information |
| Project name | Stores project name | Not its primary purpose |
| Project version | Stores project version | Not its primary purpose |
| Python version | Specifies required Python version | Records Python compatibility and resolution details |
| Dependencies | Declares direct project dependencies | Records resolved package versions |
| Package versions | Specifies version constraints | Records exact resolved versions |
| Dependency resolution | Defines dependency requirements | Stores the resolved dependency graph |
| Manual editing | Can be edited manually | Generally should not be edited manually |
| Updated by | `uv add`, manual edits | `uv lock`, `uv add` |
| Used for | Project configuration and dependency management | Reproducible dependency installation |
| Environment recreation | Provides dependency requirements | Helps recreate consistent environments using `uv sync` |

---

## 7. `uv` vs. Traditional Python Tools

| Functionality | Traditional tool | `uv` command |
|---|---|---|
| Python version management | `pyenv` | `uv python` |
| Virtual environments | `venv` | `uv venv` |
| Package installation | `pip` | `uv add` / `uv pip` |
| Dependency locking | `pip-tools` | `uv lock` |
| Environment synchronization | `pip install -r` | `uv sync` |
| Running scripts | `python` | `uv run` |
| Running CLI tools | `pipx` | `uvx` |
| Project management | Poetry | `uv init`, `uv add` |
| Building packages | `build` | `uv build` |
| Publishing packages | `twine` | `uv publish` |

These comparisons describe overlapping capabilities, not identical implementations.

---

## Final Takeaway

`uv` is not just another replacement for `pip`. It is a Python development workflow tool that handles:

- Python installations
- Virtual environments
- Dependency management
- Package installation
- Project execution
- Standalone developer tools
- Python package building and publishing
- Dependency caching

Once you understand `uv`, managing Python environments for FastAPI, LangChain, LangGraph, and RAG projects becomes considerably easier.
