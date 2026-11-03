# UV – Python's Modern Package and Project Manager

UV is a modern, extremely fast Python package and project manager developed by Astral and written in Rust

UV combines multiple Python development tools into one unified command-line tool.

Traditionally, Python developers use different tools for managing Python versions, virtual environments, dependencies, and packages. UV brings these functionalities together.

> *Official Documentation:* [uv Docs](https://docs.astral.sh/uv/)


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

### Major Functionalities of UV
> #### Python Version Management

**Command:** `uv python`

UV can download, install, discover, and manage multiple Python versions.

For example:

* Project A requires Python 3.10.
* Project B requires Python 3.11.
* Project C requires Python 3.12.

> we can manage these versions using UV without manually managing separate Python installations.

### Useful Commands

| UV Command | Purpose |
|---|---|
| `uv python list` | List available Python versions |
| `uv python install 3.12` | Install Python 3.12 |
| `uv python install 3.10 3.11 3.12` | Install multiple Python versions |
| `uv python find 3.12` | Find an installed Python version |
| `uv python pin 3.12` | Pin a Python version for a project |
| `uv python uninstall 3.10` | Uninstall Python 3.10 |

Understanding .python-version

When we execute:

bash
uv python pin 3.12

UV creates a file named .python-version.

It contains:

text
3.12

This tells UV which Python version to use for that project.

> **Use Case:** Useful when multiple projects require different Python versions.

---

#### Virtual Environment Management

**Command:** `uv venv`

A virtual environment creates an isolated Python environment for each project.

For example:

text
Project A/
    └── .venv/
        ├── FastAPI
        └── Pydantic

Project B/
    └── .venv/
        ├── Django
        └── NumPy


Dependencies installed in one environment don't automatically become available in the other.

Useful Commands
bash
##### Create a virtual environment
uv venv

##### Create with a specific Python version
uv venv --python 3.12

##### Create with a custom name
uv venv myenv

Activating the Environment

**Linux/macOS:**

bash
source .venv/bin/activate


**Windows PowerShell:**

powershell
.venv\Scripts\Activate.ps1


However, UV also allows you to execute commands without manually activating the environment.

**bash**
uv run python main.py


**UV automatically uses the project's environment.**

#### Package Management

**Commands: uv add, uv remove, uv pip**

Package management is one of the most important functionalities of UV.

Traditionally, you install Python packages using pip:

bash
pip install fastapi
pip install pydantic
pip install langchain


With UV, there are two approaches.

Approach A: Project-Based Dependency Management
bash
##### Add a dependency to the project
uv add fastapi

##### Add multiple dependencies
uv add fastapi pydantic langchain

##### Remove a dependency
uv remove fastapi


UV automatically updates:

pyproject.toml
uv.lock
Approach B: pip-Compatible Package Management
bash

##### Install a package
uv pip install fastapi

##### Install multiple packages
uv pip install fastapi pydantic

##### List installed packages
uv pip list

##### Show package information
uv pip show fastapi

##### Uninstall a package
uv pip uninstall fastapi

##### Check dependency compatibility
uv pip check

| `uv add` | `uv pip install` |
|---|---|
| Project-oriented | Environment-oriented |
| Updates `pyproject.toml` | Does not update project dependencies |
| Updates the project lockfile | Does not automatically update `uv.lock` |
| Recommended for managed projects | Useful for existing pip workflows |

Recommendation: Use uv add for new projects managed by UV.

#### Dependency Management and Locking

**Commands: uv lock, uv sync**

This functionality is particularly important when working in teams.

Imagine you install:

bash
pip install langchain


Today, it installs one version. A few weeks later, another developer installs it and receives a newer version.

This can cause compatibility issues.

UV helps solve this using a lockfile named uv.lock.

##### What Is a Lockfile?

A lockfile records the exact resolved dependency versions and other information required to reproduce a project's dependency installation.

For example, your project might declare:

text
fastapi
pydantic
langchain


The lockfile records the resolved versions of these packages and their transitive dependencies.

Useful Commands
bash
##### Generate or update the lockfile
uv lock

##### Install dependencies based on the lockfile
uv sync

##### View dependency tree
uv tree

##### Export dependencies
uv export

##### Important Difference

| File | Purpose |
|---|---|
| `pyproject.toml` | Describes project metadata and declared dependencies |
| `uv.lock` | Records exact resolved dependency information |
| `.venv/` | Contains the installed project environment |

##### Why Is This Important?

Understanding the difference helps you manage dependencies, maintain reproducible environments, and ensure consistent installations across different machines.

Suppose you push your project to GitHub.

Another developer clones the repository and executes:

bash
uv sync


UV recreates the project environment using the declared dependencies and lockfile.

Recommendation: Commit both pyproject.toml and uv.lock to GitHub.

#### Running Python Scripts

Command: uv run

Normally, you execute a Python file using:

bash
python main.py


With UV:

bash
uv run main.py


The advantage is that UV can automatically manage the project's environment and dependencies before executing the script.

Run with a Specific Python Version
bash
uv run --python 3.12 main.py

Inline Script Dependencies

UV also supports defining dependencies directly inside standalone Python scripts.

Initialize script metadata:

bash
uv init --script example.py --python 3.12


Add dependencies:

bash
uv add --script example.py requests


Execute the script:

bash
uv run example.py


UV handles the script's dependencies in an isolated environment.

Use Cases:

Quick experiments
Automation scripts
Prototypes
Small standalone Python programs
3.6 Running Developer Tools

Commands: uvx, uv tool

As a developer, you frequently need tools such as:

Tool	Purpose
Ruff	Python linting and formatting
Black	Python code formatting
Pytest	Testing
HTTPie	HTTP client

Installing these tools globally can sometimes create dependency conflicts.

UV provides isolated environments for standalone command-line tools.

Run a Tool Without Permanently Installing It
bash
uvx ruff check .

Install a Tool Permanently for Your User
bash
uv tool install ruff

Other Useful Commands
bash
##### List installed tools
uv tool list

##### Uninstall a tool
uv tool uninstall ruff

##### Upgrade installed tools
uv tool upgrade --all

Example: Traditional vs UV

Without UV:

bash
pip install ruff
ruff check .


With UV:

bash
uvx ruff check .


The second approach runs Ruff without permanently installing it.

This functionality is similar to the purpose of pipx.

#### Building and Publishing Python Packages

Commands: uv build, uv publish

Suppose you develop a reusable Python library.

For example, you build a custom Python package containing reusable RAG utilities.

You may want to distribute it so other developers can install it.

UV supports this workflow.

Build a Package
bash
uv build


This generates distribution files, generally:

.whl – Wheel distribution.
.tar.gz – Source distribution.
Publish a Package
bash
uv publish


This uploads your package to a configured package index, such as PyPI.

You need appropriate credentials and publishing configuration.

Use Cases:

Developing reusable Python libraries
Creating internal Python packages
Distributing Python utilities
Publishing open-source packages
3.8 Cache Management

Command: uv cache

UV maintains a global cache to avoid repeatedly downloading and building the same dependencies.

For example:

You install FastAPI in Project A.
You install FastAPI in Project B.
UV can reuse cached package data instead of downloading everything again.

This improves installation speed and reduces redundant storage.

Useful Commands
bash
##### Show cache directory
uv cache dir

##### Remove unused cache entries
uv cache prune

##### Clear the cache
uv cache clean


UV uses efficient package caching and reuse, so separate project environments don't necessarily require separate full copies of every dependency.

### Understanding UV Project Structure

Let's connect everything.

Create a new project:

bash
uv init my-ai-project
cd my-ai-project


**A typical project structure looks like this:**

text
my-ai-project/
│
├── .venv/
│
├── .python-version
│
├── main.py
│
├── pyproject.toml
│
├── uv.lock
│
└── README.md

Understanding Each File
| File / Folder | Purpose |
|---|---|
| `.venv/` | Isolated Python environment |
| `.python-version` | Preferred Python version |
| `main.py` | Application entry point |
| `pyproject.toml` | Project metadata and declared dependencies |
| `uv.lock` | Exact resolved dependency information |
| `README.md` | Project documentation |

#### Understanding pyproject.toml

This is one of the most important files in modern Python development.

Example:

toml
[project]
name = "my-ai-project"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "fastapi",
    "pydantic",
    "langchain",
]


It describes:

Project name
Project version
Required Python version
Project dependencies

When you execute:

bash
uv add fastapi


UV updates the dependencies in this file and resolves the lockfile.

#### Understanding uv.lock

This file contains the resolved package versions and dependency information.

You generally shouldn't edit it manually.

When another developer clones your project, they can execute:

bash
uv sync


UV uses the project configuration and lockfile to recreate the environment.

### Hands-On Example: Building an AI Project Using UV

Let's build a simple FastAPI backend for an LLM application.

Step 1: Install UV

Linux/macOS:

bash
curl -LsSf https://astral.sh/uv/install.sh | sh


Windows PowerShell:

powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"


Verify installation:

bash
uv --version

Step 2: Create a Project
bash
uv init ai-backend
cd ai-backend

Step 3: Select Python Version
bash
uv python install 3.12
uv python pin 3.12

Step 4: Add Dependencies
bash
uv add fastapi uvicorn pydantic
uv add openai langchain langgraph

Step 5: Create Your Application

Create main.py:

python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def home():
    return {"message": "AI Backend is running!"}

Step 6: Run Your Application
bash
uv run uvicorn main:app --reload

Step 7: Install Dependencies on Another Machine

After cloning the repository:

bash
uv sync

Step 8: Run the Application
bash
uv run uvicorn main:app --reload


That's it! You now have a reproducible Python development workflow.

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

### Important UV Commands – Cheat Sheet

Keep this table handy while developing Python projects.

##### Command	Functionality
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

Recommended approach: Use UV as your default Python project and dependency manager for new projects.