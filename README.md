# python-project-template

```
├── .github/workflows/ci.yml   # GitHub Actions CI
├── .env.example                # template for environment variables
├── .gitignore                  # files excluded from git
├── .pre-commit-config.yaml     # pre-commit hook configuration
├── pyproject.toml              # project metadata, dependencies, tool config
├── uv.lock                     # locked dependency versions
├── src/
│   └── main.py                 # application entry point
└── tests/
    └── test_placeholder.py     # example test
```

## Setup

```sh
cp .env.example .env  # then fill in values
uv sync
uv run pre-commit install
```

## Usage

```sh
uv run src/main.py
```

## Multi-Package Projects

For projects with multiple packages, create a folder per package under `src/` and
mirror the structure in `tests/`:

```
src/
├── main.py
├── api/
│   ├── __init__.py
│   ├── routes.py
│   └── middleware.py
├── db/
│   ├── __init__.py
│   ├── models.py
│   └── queries.py
└── utils/
    ├── __init__.py
    └── helpers.py

tests/
├── api/
│   ├── test_routes.py
│   └── test_middleware.py
├── db/
│   ├── test_models.py
│   └── test_queries.py
└── utils/
    └── test_helpers.py
```

Each package under `src/` needs an `__init__.py` to be importable
(e.g. `from api.routes import ...`). Test directories don't need `__init__.py` — pytest
discovers them automatically.

## Linting

Ruff is configured with the following lint rule sets:

- **E** — pycodestyle errors (style violations like whitespace issues)
- **F** — pyflakes (logical errors like unused imports, undefined names)
- **I** — isort (import sorting)
- **UP** — pyupgrade (suggests modern Python syntax for your target version)
- **B** — flake8-bugbear (common bugs and design problems)
- **C4** — flake8-comprehensions (better comprehensions)
- **PTH** — flake8-use-pathlib (prefer `pathlib` over `os.path`)
- **RUF** — ruff-specific rules

## Pre-commit hooks

Pre-commit hooks run automatically on `git commit` to catch issues before they reach CI.
The following hooks are configured:

- **ruff-check** — lints and auto-fixes (uses rules from pyproject.toml)
- **ruff-format** — enforces consistent formatting
- **trailing-whitespace** — removes trailing whitespace
- **end-of-file-fixer** — ensures files end with a newline
- **check-json** — validates JSON syntax
- **check-toml** — validates TOML syntax
- **check-yaml** — validates YAML syntax
- **pyright** — type checks

To run all hooks manually against every file:

```sh
uv run pre-commit run --all-files
```

Ruff and pyright run as local hooks via `uv run`, so they use the exact versions
pinned in `uv.lock`. This avoids version drift between pre-commit and your dev
environment — the same tool version runs everywhere (locally, in hooks, and in CI).
Update them with `uv lock --upgrade`.

The file-hygiene hooks (trailing-whitespace, etc.) are external since they're
lightweight and don't overlap with anything in the project's dependencies. Update
them with `uv run pre-commit autoupdate`.

## CI

GitHub Actions runs on every push to `main` and on pull requests. The workflow:

1. Checks out the code
2. Sets up uv and Python (version from `requires-python`)
3. Installs dependencies (`uv sync`)
4. Runs `ruff check` (linting)
5. Runs `ruff format --check` (formatting)
6. Runs `pyright` (type checking)
7. Runs `pytest` (tests)
