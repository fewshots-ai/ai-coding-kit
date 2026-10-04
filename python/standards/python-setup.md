# Python, uv, and FastAPI setup

Read when creating a project, or changing dependencies or tooling.

## Rules

- **Latest stable Python.** Run `uv python list` and choose the newest stable CPython, skipping alpha, beta, and rc builds. If a required package does not support it yet, step back one minor version and say why in the README.
- **Current uv.** If `uv --version` shows a build more than a few months old, ask the user to update uv. An old uv does not know the newest Python releases.
- **uv owns the toolchain**: Python versions, the virtual environment, dependencies, the lockfile, and running commands.
- **Native uv first.** Use the commands in the table below. Use `uv pip` only when no native command can do the job, and state the reason when you do.
- **Never use** `pip`, `pipx`, `poetry`, `conda`, `pyenv`, `python -m venv`, or a hand-maintained `requirements.txt`.
- **`pyproject.toml` is the single source** for metadata, dependencies, and tool settings. No `setup.py`, `ruff.toml`, or `pytest.ini`.
- **Run everything through `uv run`** so it uses the locked environment. Do not activate `.venv`.
- **Commit** `pyproject.toml`, `uv.lock`, and `.python-version`. Never commit `.venv/` or `.env`. Add `.env` and `.coverage` to `.gitignore` yourself, because `uv init` does not.

## Command map

| Task | Native uv command |
|---|---|
| See and install Python versions | `uv python list`, `uv python install 3.x` |
| Change the project's Python version | edit `requires-python`, then `uv python pin 3.x` and `uv sync` |
| Create an application in the current folder | `uv init --app --python 3.x` |
| Create a library or CLI package | `uv init --package --python 3.x` |
| Add or remove a runtime dependency | `uv add <package>`, `uv remove <package>` |
| Add a dev-only dependency | `uv add --dev <package>` |
| Adopt a legacy requirements file | `uv add -r requirements.txt` |
| Install exactly what is locked | `uv sync` locally, `uv sync --locked` in CI |
| Upgrade dependencies | `uv lock --upgrade` or `uv lock --upgrade-package <package>`, then `uv sync` |
| Run code or a project tool | `uv run <command>` |
| Run a one-off tool without installing it | `uvx <tool>` |
| Inspect the dependency tree | `uv tree` |
| Export for a platform that requires it | `uv export --format requirements.txt` |
| Bump the version, build the package | `uv version --bump patch`, `uv build` |

## New FastAPI project

Run this in the project root, the folder that holds `AGENTS.md`. The folder name becomes the project name unless you pass `--name`.

```bash
uv init --app --python 3.x
uv add "fastapi[standard]"
uv add --dev ruff pytest
```

Replace the generated `main.py` with the `app/` layout in [architecture.md](architecture.md), and add the tool settings below.
Add `pydantic-settings` with `uv add` when the app has settings.
Run the dev server with `uv run fastapi dev` (reload on, docs at `/docs`) and the production server with `uv run fastapi run`.

## Tool settings in `pyproject.toml`

```toml
[tool.ruff.lint]
select = [
    "E", "W", "F", "I", "N", "UP", "B", "SIM", "C4", "RUF",  # style, bugs, modern idioms
    "ANN", "D1",                                              # type hints and docstrings required
    "BLE", "T20", "LOG", "G",                                 # no blind except, no print, sound logging
    "S", "ASYNC", "FAST", "PT",                               # security, async, FastAPI, pytest
]
ignore = ["D100", "D104", "D105", "D107"]  # docstrings required on classes and functions only

[tool.ruff.lint.per-file-ignores]
"tests/**" = ["S101", "D1"]  # asserts allowed, test names replace docstrings

[tool.pytest.ini_options]
testpaths = ["tests"]
pythonpath = ["."]  # lets tests import the app package
addopts = ["--import-mode=importlib"]  # lets test files share a name across folders
```
