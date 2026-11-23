# Delivery: commit, review, document, release

Read when committing, reviewing, documenting, versioning, or deploying.

## Commits

- Commit or push only when asked. Never commit secrets, `.env*.local`, `node_modules/`, `dist/`, or `coverage/`.
- Make one logical change per commit. On a shared repository, work on a branch such as `feat/todo-filters`, not on `main`.
- Use Conventional Commits with an imperative subject under 72 characters: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`. The body says why.

## Review

- Review your own diff first. It contains only what the task needs: no drive-by refactors, debug output, or commented-out code.
- Check the four states of each changed screen, keyboard access, names and comments, tests, security, and docs.
- A pull request states what changed, why, how it was verified, and how to roll it back. Include a screenshot for visible changes.

## Documentation

- `README.md` always works: what the app is, prerequisites, setup and run commands, configuration, how to test, known limitations.
- Update docs in the same change as the behavior they describe.
- `CHANGELOG.md` gets one line per user-visible change.
- `.env.example` lists every `VITE_` variable with a safe example value.

## CI

- When the repository has CI, it runs `npm ci` and then `npm run check` on every push. A red pipeline blocks the merge.

## Release and deploy

- Use semantic versioning. Bump with `npm version patch`, `minor`, or `major`, which also tags the commit.
- `npm run build` produces `dist/`, a static bundle. Deploy that folder to static hosting or a CDN, with the API address set per environment through `VITE_` variables at build time.
- Serve `index.html` for unknown paths so client-side routes work on reload, and cache hashed assets for a year.
- Check the production build with `npm run preview` and walk the main flow before releasing.
- Every release has a smoke test of the main flow and a rollback step, which for static hosting is redeploying the previous build.
- Before presenting to an audience, load sample data, run the demo path once, and keep a reset step ready.
