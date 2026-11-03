# UV – Python's Modern Package and Project Manager

UV is a modern, extremely fast Python package and project manager developed by Astral and written in Rust

UV combines multiple Python development tools into one unified command-line tool.

Traditionally, Python developers use different tools for managing Python versions, virtual environments, dependencies, and packages. UV brings these functionalities together.

> *Official Documentation:* [uv Docs](https://docs.astral.sh/uv/)

### UV Commands – Cheat Sheet

| UV Command | Purpose |
|---|---|
| `uv --version` | Check UV version |
| `uv init` | Initialize a project |
| `uv python list` | List available Python versions |
| `uv python install 3.12` | Install Python |
| `uv python pin 3.12` | Pin Python version |
| `uv venv` | Create virtual environment |
| `uv add package` | Add project dependency |
| `uv remove package` | Remove project dependency |
| `uv pip install package` | Install package into an environment |
| `uv lock` | Generate or update lockfile |
| `uv sync` | Synchronize project environment |
| `uv run main.py` | Execute Python script |
| `uv tree` | Display dependency tree |
| `uvx ruff check .` | Run a standalone tool |
| `uv tool install ruff` | Install a CLI tool |
| `uv build` | Build Python package |
| `uv publish` | Publish Python package |
| `uv cache clean` | Clear UV cache |
| `uv self update` | Update UV |

---

### Traditional Tools vs UV

| Traditional Tool | Purpose | UV Equivalent |
|---|---|---|
| Python / pyenv | Python version management | `uv python` |
| venv / virtualenv | Virtual environment management | `uv venv` |
| pip | Package installation | `uv pip` / `uv add` |
| pip-tools | Dependency locking | `uv lock` |
| pipx | Running standalone CLI tools | `uvx` / `uv tool` |
| Poetry | Project and dependency management | `uv init`, `uv add`, `uv sync` |
| build | Building Python packages | `uv build` |
| twine | Publishing Python packages | `uv publish` |

> *Note: These are functional comparisons, not exact one-to-one replacements in every workflow.*

---

### Why Do We Need UV?

> Imagine building an AI application using:

* Python 3.12
* FastAPI
* LangChain
* LangGraph
* OpenAI SDK
* PostgreSQL
* Pydantic

> we need to manage several things:

* Which Python version should the project use?
* How do you isolate project dependencies?
* How do you install packages?
* How do you manage package versions?
* How do you ensure another developer can recreate your environment?
* How do you execute your application? 

> *Traditionally, we might need multiple tools to accomplish these tasks.*
*UV simplifies this workflow by providing a unified interface.*

---

## Major Functionalities of UV
> ### Python Version Management

UV can download, install, discover, and manage multiple Python versions.

For example:

* Project A requires Python 3.10.
* Project B requires Python 3.11.
* Project C requires Python 3.12.

> we can manage these versions using UV without manually managing separate Python installations.


| UV Command | Purpose |
|---|---|
| `uv python list` | List available Python versions |
| `uv python install 3.12` | Install Python 3.12 |
| `uv python install 3.10 3.11 3.12` | Install multiple Python versions |
| `uv python find 3.12` | Find an installed Python version |
| `uv python pin 3.12` | Pin a Python version for a project |
| `uv python uninstall 3.10` | Uninstall Python 3.10 |


**When we execute:**
bash

>`uv python pin 3.12`
>
> uv creates a file named **.python-version.**
> It contains: **text** - `3.12`

*This tells UV which Python version to use for that project.*

---

> ### Virtual Environment Management

A virtual environment creates an isolated Python environment for each project.

Dependencies installed in one environment don't automatically become available in the other.

| UV Command | Purpose |
|---|---|
| `uv venv` | Create a virtual environment |
| `uv venv --python 3.12` | Create a virtual environment with a specific Python version (3.12) |
| `uv venv myenv` | Create a virtual environment with a custom name (`myenv`) |


> #### Activating the Environment

##### Linux/macOS:
> `source .venv/bin/activate`


##### Windows PowerShell:
> `.venv\Scripts\Activate.ps1`


However, UV also allows you to execute commands without manually activating the environment.

##### bash
>`uv run python main.py`

UV automatically uses the project's environment.

---

> ### Package Management

commands: `uv add`, `uv remove`, `uv pip`

Package management is one of the most important functionalities of UV.

Traditionally, you install Python packages using pip:

bash
>`pip install fastapi`
>`pip install pydantic`
>`pip install langchain`


With UV, there are **two approaches.**

> ### *Approach A*: Project-Based Dependency Management

| UV Command | Purpose |
|---|---|
| `uv add fastapi` | Add a dependency to the project |
| `uv add fastapi pydantic langchain` | Add multiple dependencies to the project |
| `uv remove fastapi` | Remove a dependency from the project |


#### UV automatically updates:

> pyproject.toml
> uv.lock


> ### *Approach B*: pip-Compatible Package Management

| UV Command | Purpose |
|---|---|
| `uv pip install fastapi` | Install a package |
| `uv pip install fastapi pydantic` | Install multiple packages |
| `uv pip list` | List installed packages |
| `uv pip show fastapi` | Show package information |
| `uv pip uninstall fastapi` | Uninstall a package |
| `uv pip check` | Check dependency compatibility |
| `uv add` | `uv pip install` |
|---|---|

> ### Difference Between `uv add` and `uv pip install`

| `uv add` | `uv pip install` |
|---|---|
| Project-oriented | Environment-oriented |
| Updates `pyproject.toml` | Does not update project dependencies |
| Updates the project lockfile | Does not automatically update `uv.lock` |
| Recommended for managed projects | Useful for existing pip workflows |

> *Recommendation: Use uv add for new projects managed by UV.*


---

> ### Dependency Management and Locking

commands: `uv lock`,`uv sync`

This functionality is particularly important when working in teams.

Imagine you install:
> `pip install langchain`

Today, it installs one version. A few weeks later, another developer installs it and receives a newer version.

This can cause compatibility issues.

*UV helps solve this using a lockfile named uv.lock.*

> ### What Is a Lockfile?

A lockfile records the exact resolved dependency versions and other information required to reproduce a project's dependency installation.

For example, your project might declare:

>`text`
>`fastapi`
>`pydantic`
>`langchain`


The lockfile records the resolved versions of these packages and their transitive dependencies.

| UV Command | Purpose |
|---|---|
| `uv lock` | Generate or update the lockfile |
| `uv sync` | Install dependencies based on the lockfile |
| `uv tree` | View the dependency tree |
| `uv export` | Export project dependencies |


> ### Important Difference

| File | Purpose |
|---|---|
| `pyproject.toml` | Describes project metadata and declared dependencies |
| `uv.lock` | Records exact resolved dependency information |
| `.venv/` | Contains the installed project environment |

> #### Why Is This Important?

Understanding the difference helps you manage dependencies, maintain reproducible environments, and ensure consistent installations across different machines.

Suppose you push your project to GitHub.

Another developer clones the repository and executes:

**bash**
> `uv sync`

*UV recreates the project environment using the declared dependencies and lockfile.*

> *Recommendation: Commit both pyproject.toml and uv.lock to GitHub.*

---

> ### Running Python Scripts

Normally, you execute a Python file using:

**bash**
> `python main.py`


**With UV:**
> `uv run main.py`


The advantage is that UV can automatically manage the project's environment and dependencies before executing the script.

UV also supports defining dependencies directly inside standalone Python scripts.

UV handles the script's dependencies in an isolated environment.

> **Use Cases:**

* Quick experiments
* Automation scripts
* Prototypes
* Small standalone Python programs
* Running Developer Tools
   > * As a developer, you frequently need tools such as:
   > * Installing these tools globally can sometimes create dependency conflicts.
   > * UV provides isolated environments for standalone command-line tools.


| UV Command | Purpose |
|---|---|
| `uv tool list` | List installed tools |
| `uv tool uninstall ruff` | Uninstall a tool |
| `uv tool upgrade --all` | Upgrade all installed tools |


---

> ### Building and Publishing Python Packages

Suppose you develop a reusable Python library, say a custom Python package containing reusable RAG utilities.

You may want to distribute it so other developers can install it.

> UV supports this - `uv build`

This generates distribution files, generally:

> .whl – Wheel distribution.
>.tar.gz – Source distribution.
> Publish a Package - `uv publish`


This uploads your package to a configured package index, such as PyPI.

> *You need appropriate credentials and publishing configuration.*


---

> ### Cache Management

UV maintains a global cache to avoid repeatedly downloading and building the same dependencies.

This improves installation speed and reduces redundant storage.

UV uses efficient package caching and reuse, so separate project environments don't necessarily require separate full copies of every dependency.

| UV Command | Purpose |
|---|---|
| `uv cache dir` | Show the UV cache directory |
| `uv cache prune` | Remove unused cache entries |
| `uv cache clean` | Clear the UV cache |

---

> ### Difference Between `pyproject.toml` and `uv.lock`

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

### UV vs Traditional Python Tools
| Functionality | Traditional Tool | UV Command |
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

### Final Takeaway

UV is not just another replacement for pip.

It is a complete Python development workflow tool that handles:

* Python installations
* Virtual environments
* Dependency management
* Package installation
* Project execution
* Standalone developer tools
* Python package building and publishing
* Dependency caching

Once you understand UV, managing Python environments for FastAPI, LangChain, LangGraph, and RAG projects becomes considerably easier.