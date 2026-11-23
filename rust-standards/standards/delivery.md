# Delivery: commit, review, document, release

Read when committing, reviewing, documenting, versioning, or deploying.

## Commits

- Commit or push only when asked. Never commit secrets, `.env`, or `target/`. Do commit `Cargo.lock`.
- Make one logical change per commit. On a shared repository, work on a branch such as `feat/user-export`, not on `main`.
- Use Conventional Commits with an imperative subject under 72 characters: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`. The body says why.

## Review

- Review your own diff first. It contains only what the task needs: no drive-by refactors, debug output, or commented-out code.
- Check correctness and edge cases, errors and logging, names and docs, tests, security, and docs.
- A pull request states what changed, why, how it was verified, and how to roll it back.

## Documentation

- `README.md` always works: what the app is, the toolchain it needs, setup and run commands, configuration, how to test, known limitations.
- Update docs in the same change as the behavior they describe.
- `CHANGELOG.md` gets one line per user-visible change.
- `cargo doc --no-deps` builds without warnings; the public API docs are part of the deliverable.

## CI

- When the repository has CI, it runs `cargo fmt --check`, `cargo clippy --all-targets -- -D warnings`, and `cargo test` on stable on every push. A red pipeline blocks the merge.

## Release and deploy

- Use semantic versioning. Bump `version` in `Cargo.toml`, commit, and tag `v1.2.0`.
- `cargo build --release` produces one static binary. Deploy that binary, configured by environment variables.
- Containers use a multi-stage build: a Rust image compiles, a minimal base image runs the binary as a non-root user.
- Every release has a smoke test (`/health` plus the main flow), a migration step if data changes, and a rollback step.
- Before presenting to an audience, load sample data, run the demo path once, and keep a reset step ready.
