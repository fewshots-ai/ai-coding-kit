# Testing

Read when writing tests or judging whether work is done.

- **pytest only.** Tests live in `tests/` and mirror `app/`. Run them with `uv run pytest`.
- **Test behavior, not implementation.** One behavior per test, named for it: `test_create_user_rejects_duplicate_email`.
- **Arrange, act, assert**, separated by blank lines. No loops or conditionals in a test. Use `pytest.mark.parametrize` for variations.
- **Cover the paths.** Test the happy path and each error response. An application also tests every guard clause and the boundary values: empty, zero, maximum, missing, duplicate.
- **Trace to requirements.** Every acceptance criterion has a test. Every bug fix starts with a failing test that reproduces the bug.
- **Test the API through `TestClient`** built from `create_app()` and used in a `with` block, so `lifespan` runs and each test gets fresh state. Swap collaborators with `dependency_overrides`. Assert the status code and the body, including the error shape.
- **Isolate.** No real network, services, or shared state. Fake at the boundary: repositories and clients. Use fixtures for setup, `tmp_path` for files, and `monkeypatch` for environment and time.
- **Fast and deterministic.** The suite runs in seconds, in any order, with no sleeps and no unseeded randomness.
- **Coverage is a signal.** Check it with `uv run --with pytest-cov pytest --cov=app --cov-report=term-missing`. Cover the business logic in `services/` fully before chasing a number.
- **Warnings are work.** Fix deprecation warnings instead of silencing them.
- **Never weaken a test to get green.** Fix the code, or explain why the test was wrong.
