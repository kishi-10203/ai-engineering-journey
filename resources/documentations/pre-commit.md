# Pre-commit in Python Projects Using `uv`

## 1. What Is `pre-commit`?

`pre-commit` is a development tool that automatically runs configured
quality checks before a Git commit is created.

It helps catch common issues such as:

-   Formatting and linting problems.
-   Syntax and configuration errors.
-   Trailing whitespace and missing final newlines.
-   Merge-conflict markers and debugging statements.
-   Accidentally committed secrets.
-   Failures from custom validation scripts.

In a team, it provides a consistent quality-control checkpoint before
changes enter the shared repository.

---

## 2. How `pre-commit` Works

The workflow has three main components:

1.  **Git hook:** Git invokes the installed hook when a commit is
    attempted.
2.  **Configuration:** `.pre-commit-config.yaml` specifies which hooks
    to run and how.
3.  **Hooks:** Individual tools inspect or modify files.

### Workflow

1.  A developer edits and stages files.
2.  The developer runs `git commit`.
3.  Git invokes `.git/hooks/pre-commit`.
4.  `pre-commit` reads `.pre-commit-config.yaml` and runs applicable
    hooks.
5.  If checks pass, Git creates the commit.
6.  If a check fails---or a hook changes a file---the commit is stopped.
    Review and stage any changes, then commit again.

For example, a hook may detect this formatting issue:

``` python
def calculate_total(price, tax):
    return price+tax
```

---

## 3. Set Up `pre-commit` with `uv`

#### Step 1: Create a project

``` bash
uv init my-python-project
cd my-python-project
git init
```

Initialize Git only if the repository has not already been initialized.

#### Step 2: Install `pre-commit`

For a project-managed development dependency:

``` bash
uv add --dev pre-commit
uv run pre-commit --version
```

This declares the tool in `pyproject.toml` and updates `uv.lock`.

Alternatively, install it as a standalone CLI tool:

``` bash
uv tool install pre-commit
pre-commit --version
```

| Approach | Best suited for |
|---|---|
| `uv add --dev pre-commit` | Team projects with reproducible development dependencies |
| `uv tool install pre-commit` | A separately installed CLI tool |
| `uvx pre-commit` | Running the tool without a permanent installation |


#### Step 3: Create `.pre-commit-config.yaml`

Create this file in the project root:

``` yaml
repos:
  # General-purpose Git hooks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-toml
      - id: check-merge-conflict
      - id: debug-statements

  # Python linting and formatting
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.14.0
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
```

These revisions are examples. Check compatibility and choose verified
revisions before adopting them.

#### Step 4: Install dependencies and the Git hook

``` bash
uv sync --group dev
uv run pre-commit install
```

`uv sync --group dev` synchronizes the project's development
dependencies. `pre-commit install` installs the Git hook.

#### Step 5: Run the checks

``` bash
uv run pre-commit run --all-files
```

This validates all applicable tracked files and is useful when
introducing hooks to an existing repository.

---

## 4. Understanding the Configuration

### `repos`

The `repos` section identifies external repositories that provide hooks.
For example, `pre-commit-hooks` supplies general repository checks,
while `ruff-pre-commit` supplies Ruff hooks.

### `rev`

`rev` specifies the version or Git revision of the hook repository.
Pinning a revision makes the workflow more predictable across
developers.

### `hooks`

The `hooks` section lists the checks to execute.

| Hook | Purpose |
|---|---|
| `trailing-whitespace` | Removes trailing spaces |
| `end-of-file-fixer` | Ensures files end with a newline |
| `check-yaml` | Validates YAML syntax |
| `check-json` | Validates JSON syntax |
| `check-toml` | Validates TOML syntax |
| `check-merge-conflict` | Detects merge-conflict markers |
| `debug-statements` | Detects Python debugging statements |
| `ruff` | Checks Python linting issues |
| `ruff-format` | Formats Python code |


### `args`

`args` passes additional command-line arguments to a hook. For example:

``` yaml
- id: ruff
  args: [--fix]
```

`--fix` asks Ruff to automatically fix supported linting issues.

### `files` and `exclude`

These options control which files a hook processes:

``` yaml
- id: ruff
  files: \.py$
  exclude: ^tests/fixtures/
```

This example selects Python files and excludes files under
`tests/fixtures/`.

---

## 5. Manual Run Commands

| Command | Use case |
|---|---|
| `uv run pre-commit --version` | Check the installed version |
| `uv run pre-commit install` | Install the Git hook |
| `uv run pre-commit run` | Run hooks on staged files |
| `uv run pre-commit run --all-files` | Run hooks on all applicable tracked files |
| `uv run pre-commit autoupdate` | Update configured hook revisions |
| `uv run pre-commit uninstall` | Remove the installed Git hook |

---

## 6. What Happens When a Check Fails?

Suppose a file contains a debugging statement or formatting issue:

``` python
def calculate_total(price, tax):
    print("Debugging")
    return price+tax
```

If a configured hook reports a problem, the commit is stopped until the
issue is resolved. Some hooks can modify files automatically; review
those changes and stage them again.

``` bash
git add main.py
git commit -m "Add calculation function"
```

If all checks pass, Git proceeds with the commit.

---

## 7. Integrate `pre-commit` into CI/CD

Git hooks run locally and can be bypassed with:

``` bash
git commit --no-verify
```

Therefore, local hooks should complement---not replace---CI checks. For
example, a GitHub Actions workflow can run the same checks on pushes and
pull requests:

``` yaml
name: Python Quality Checks

on:
  push:
  pull_request:

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v7

      - name: Set up Python
        run: uv python install

      - name: Install dependencies
        run: uv sync --group dev

      - name: Run pre-commit
        run: uv run pre-commit run --all-files
```

For production repositories, pin GitHub Actions to verified commit SHAs
and follow organizational dependency and security policies.

---

## 8. Best Practices

1.  Keep `pre-commit` in development dependencies for team projects.
2.  Commit `uv.lock` to support consistent dependency resolution.
3.  Pin hook revisions rather than using moving references.
4.  Use Ruff for Python linting and formatting when appropriate.
5.  Run all hooks before merging.
6.  Run checks in CI/CD as well as locally.
7.  Review and stage changes made by automatic fixers.
8.  Document setup commands for contributors.

---

## 9. Complete Workflow Cheat Sheet

| Step | Command or action | Purpose |
|---|---|---|
| Create project | `uv init my-project` | Initialize the project |
| Add tool | `uv add --dev pre-commit` | Declare the development dependency |
| Configure | Create `.pre-commit-config.yaml` | Choose hooks and revisions |
| Sync | `uv sync --group dev` | Install project development dependencies |
| Install hook | `uv run pre-commit install` | Run hooks automatically on commit |
| Validate | `uv run pre-commit run --all-files` | Check tracked files |
| Commit | `git add .` then `git commit -m "..."` | Commit after checks pass |

---

## 10. Final Takeaway

`pre-commit` automates repository checks before commits, helping teams
catch common quality issues early and apply consistent standards. With
`uv`, the tool can be managed as a reproducible development dependency.
Local hooks provide fast feedback, while CI/CD offers an additional
validation layer.
