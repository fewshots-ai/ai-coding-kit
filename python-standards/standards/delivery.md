# Delivery: commit, review, document, release

Read when committing, reviewing, documenting, versioning, or deploying.

## Commits

- Commit or push only when asked. Never commit secrets, `.env`, `.venv/`, or generated files.
- Make one logical change per commit. On a shared repository, work on a branch such as `feat/order-export`, not on `main`.
- Use Conventional Commits with an imperative subject under 72 characters: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`. The body says why.

## Review

- Review your own diff first. It contains only what the task needs: no drive-by refactors, debug code, or commented-out code.
- Check correctness and edge cases, errors and logging, names and docstrings, tests, security, and docs.
- A pull request states what changed, why, how it was verified, and how to roll it back.

## Documentation

- `README.md` always works: what the app is, prerequisites, setup and run commands, configuration, how to test, known limitations.
- Update docs in the same change as the behavior they describe.
- `CHANGELOG.md` gets one line per user-visible change.
- API docs come from FastAPI's OpenAPI output. Keep route docstrings and schema descriptions accurate.

## CI

- When the repository has CI, it runs the Definition of done on every push: `uv sync --locked`, `uv run ruff check .`, `uv run ruff format --check .`, `uv run pytest`. A red pipeline blocks the merge.

## Release and deploy

- Use semantic versioning. Bump with `uv version --bump patch`, `minor`, or `major`, then tag `vX.Y.Z`.
- Build one immutable artifact and promote it unchanged. All configuration comes from environment variables.
- Containers use a slim official Python image, install with `uv sync --locked --no-dev`, and run as a non-root user.
- Every release has a smoke test (`/health` plus the main flow), a migration step if data changes, and a rollback step.
- Before presenting to an audience, load sample data, run the demo path once, and keep a reset command ready.
