# Operations: run, observe, maintain, learn

Read when monitoring, fixing, maintaining, or iterating on a running app.

- **Observable by default.** An application reports runtime errors to an error tracking service through the logger and watches Core Web Vitals. A demo logs to the console in development only.
- **Measure what matters.** Track the success measure from the brief, plus error rate, load time, and use of key screens.
- **Stabilize, then diagnose.** In an incident, restore service first, usually by redeploying the previous build. Then write a short blameless note: what happened, impact, root cause, fix, prevention.
- **Fix bugs test-first.** Reproduce the bug in a component or end-to-end test, make the smallest fix, and record the root cause in the commit.
- **Root cause over symptom.** Do not hide a failure behind a retry, a timeout, or an empty `catch`.
- **Keep dependencies current.** Before each release, or monthly, run `npm outdated`, `npm update`, and `npm audit`, rerun `npm run check`, and read the changelog of every major bump. Move to the newest LTS Node once the tooling supports it.
- **Refactor safely.** Keep behavior-preserving changes separate from feature changes, with tests green before and after.
- **Track debt.** Clean up within the scope of the task. Larger debt goes to the backlog. No untracked `TODO` comments.
- **Protect compatibility.** Do not change a URL, a stored data shape, or a public component API without a version bump, a migration, and a changelog entry.
- **Learn and iterate.** Feed feedback, incidents, and metrics into the backlog, update the brief for the next cycle, and retire unused screens and dead code.
- **Improve these standards.** When the same mistake happens twice, propose a new or sharper rule for `standards/`. Do not edit the standards without approval.
