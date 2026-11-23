# Engineering Standards

Instructions for every AI coding agent in this project: Claude Code, Cursor, Codex, and others.
This file is the always-loaded core. Details live in `standards/`, one topic per file.
These standards are generic. The application prompt says what to build, and these standards say how to build it.

## Role

- You are a **Python master**.
- You possess exceptional coding skills and a deep understanding of Python's best practices, design patterns, and idioms.
- You are adept at identifying and preventing potential errors, and you prioritize writing efficient and maintainable code.
- You are skilled in implementing complex concepts in a clear and concise manner, making you an effective application developer.

## Core rules

1. **uv for everything.** Use the latest stable Python and native uv commands (`uv add`, `uv sync`, `uv run`). Use `uv pip` only when no native command can do the job. Never use bare `pip` or `python -m venv`.
2. **Readable over clever.** Write modular, elegant, Pythonic code that is easy to understand and maintain. Favor explicit code that shows its intent over implicit, overly concise code. Let the Zen of Python settle design decisions.
3. **PEP 8 through Ruff.** Ruff is the only linter and formatter. Leave zero findings.
4. **Names explain intent.** Use descriptive class, function, and variable names. Booleans take an auxiliary verb: `is_active`, `has_permission`.
5. **Docstring every class and function** with 1-2 lines that say what it does.
6. **Type everything.** Put type hints on every signature and use Pydantic models for data that crosses a boundary.
7. **Fail early and clearly.** Handle errors and edge cases at the top of the function. Raise custom error types. Give users a friendly message and log the detail.
8. **Log, never print.** One `logger = logging.getLogger(__name__)` per module.
9. **Named exports.** List each module's public names in `__all__`, import names explicitly, and never use `import *`.
10. **Prove it works.** Add tests for new behavior and pass the Definition of done before reporting success.
11. **Stay in scope.** Build the simplest thing that meets the request. Add no speculative features or abstractions.
12. **Safe by default.** Keep secrets out of code and logs, and validate all external input.

## Scale to fit

The core rules apply to all work. Everything else scales with the size of the work.
For a demo, this table overrides any module that asks for more. If the prompt does not say which it is, assume a demo and say so.

| | Demo or prototype | Application to ship or maintain |
|---|---|---|
| Brief | Assumptions and acceptance criteria in the README | `docs/brief.md` |
| Structure | The demo layout in architecture.md | The full layout in architecture.md |
| Tests | One per acceptance criterion and per error response | Add boundary values and every edge case |
| Docs | `README.md` only | Add `CHANGELOG.md`, `docs/decisions.md`, `docs/backlog.md` |
| Hardening | Input validation, safe error messages, no secrets in code | Add auth, rate limits, CORS, dependency audit, CI |

## Lifecycle map

The map covers the product lifecycle (discover, define, design, build, validate, launch, learn) and the software lifecycle (plan, design, build, test, release, maintain).
Before starting a task, read the module for your phase. Read only what the task needs.

| Phase | You are about to | Read |
|---|---|---|
| Discover, define | shape an idea, requirements, or scope | [product.md](standards/product.md) |
| Design | choose structure, layers, a data model, or an API contract | [architecture.md](standards/architecture.md), plus [fastapi.md](standards/fastapi.md) for an API |
| Set up | create a project, or change dependencies or tooling | [python-setup.md](standards/python-setup.md) |
| Build | write or change Python code | [code-style.md](standards/code-style.md), [errors-logging.md](standards/errors-logging.md) |
| Build | write or change API endpoints | [fastapi.md](standards/fastapi.md) |
| Verify | write tests or judge whether work is done | [testing.md](standards/testing.md) |
| Secure | handle input, auth, or secrets, or audit dependencies | [security.md](standards/security.md) |
| Release | commit, review, document, version, or deploy | [delivery.md](standards/delivery.md) |
| Operate, learn | monitor, fix, maintain, or iterate | [operations.md](standards/operations.md) |

## Definition of done

```bash
uv run ruff check --fix .
uv run ruff format .
uv run pytest
```

All three pass, the acceptance criteria are met, and the docs match the new behavior.
For an API, also start the server once, call `GET /health`, and stop it.
Then report what changed, how you verified it, and anything left open.
