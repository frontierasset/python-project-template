# python-project-template

A template for Python projects.

## Setup

```sh
uv sync
uv run pre-commit install
```

## Usage

```sh
uv run src/main.py
```

## Project structure

For projects with multiple sub-modules, create a folder per module under `src/` and
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

Each sub-module under `src/` needs an `__init__.py` to be importable as a package
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

- **ruff-check** — lints and auto-sorts imports (`--select I --fix`)
- **ruff-format** — enforces consistent formatting
- **trailing-whitespace** — removes trailing whitespace
- **end-of-file-fixer** — ensures files end with a newline
- **check-yaml** — validates YAML syntax
- **pyright** — type checks

To run all hooks manually against every file:

```sh
uv run pre-commit run --all-files
```

Periodically update hook versions with:

```sh
uv run pre-commit autoupdate
```

## CI

GitHub Actions runs on every push to `main` and on pull requests. The workflow:

1. Checks out the code
2. Sets up uv and Python (version from `requires-python`)
3. Installs dependencies (`uv sync`)
4. Runs `ruff check` (linting)
5. Runs `ruff format --check` (formatting)
6. Runs `pyright` (type checking)
7. Runs `pytest` (tests)
