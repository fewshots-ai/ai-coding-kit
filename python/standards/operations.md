# Operations: run, observe, maintain, learn

Read when monitoring, fixing, maintaining, or iterating on a running app.

- **Observable by default.** Confirm the app answers `GET /health` and logs startup, shutdown, and external calls. An application in production emits structured logs with a request id.
- **Measure what matters.** Track the success measure from the brief, plus error rate, latency, and use of key features.
- **Stabilize, then diagnose.** In an incident, restore service first, usually by rolling back. Then write a short blameless note: what happened, impact, root cause, fix, prevention.
- **Fix bugs test-first.** Reproduce the bug, write a failing test, make the smallest fix, and record the root cause in the commit.
- **Root cause over symptom.** Do not hide a failure behind a retry, a sleep, or a broad `try`/`except`.
- **Keep dependencies current.** Before each release, or monthly, run `uv lock --upgrade` and `uv sync`, rerun the checks, and read the changelog of every major bump. Move to the newest stable Python once dependencies support it.
- **Refactor safely.** Keep behavior-preserving changes separate from feature changes, with tests green before and after.
- **Track debt.** Clean up within the scope of the task. Larger debt goes to the backlog. No untracked `TODO` comments.
- **Protect compatibility.** Do not break a public API or stored data without a version bump, a migration, and a changelog entry. Deprecate before removing.
- **Learn and iterate.** Feed feedback, incidents, and metrics into the backlog, update the brief for the next cycle, and retire unused features and dead code.
- **Improve these standards.** When the same mistake happens twice, propose a new or sharper rule for `standards/`. Do not edit the standards without approval.
