# Delivery: commit, review, document, release

Read when committing, reviewing, documenting, versioning, or deploying.

## Commits

- Commit or push only when asked. Never commit secrets, `.env` files, `target/`, or IDE files.
- Make one logical change per commit. On a shared repository, work on a branch such as `feat/user-export`, not on `main`.
- Use Conventional Commits with an imperative subject under 72 characters: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`. The body says why.

## Review

- Review your own diff first. It contains only what the task needs: no drive-by refactors, debug output, or commented-out code.
- Check correctness and edge cases, errors and logging, names and Javadoc, tests, security, and docs.
- A pull request states what changed, why, how it was verified, and how to roll it back.

## Documentation

- `README.md` always works: what the app is, the JDK it needs, setup and run commands, configuration, how to test, known limitations.
- Update docs in the same change as the behavior they describe.
- `CHANGELOG.md` gets one line per user-visible change.
- An application documents its API with springdoc-openapi, so `/swagger-ui.html` stays accurate.

## CI

- When the repository has CI, it runs `./mvnw -q verify` on the LTS JDK on every push. A red pipeline blocks the merge.

## Release and deploy

- Use semantic versioning. Set the version with `./mvnw versions:set -DnewVersion=1.2.0` and tag `v1.2.0`.
- `./mvnw -q package` builds one runnable jar. Run it with `java -jar`, configured by environment variables.
- Containers come from `./mvnw spring-boot:build-image`, which needs no Dockerfile, or from a slim JRE image running the jar as a non-root user.
- Every release has a smoke test (`/health` plus the main flow), a Flyway migration if data changes, and a rollback step.
- Before presenting to an audience, load sample data, run the demo path once, and keep a reset step ready.
