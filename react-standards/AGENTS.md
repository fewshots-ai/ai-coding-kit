# Engineering Standards

Instructions for every AI coding agent in this project: Claude Code, Cursor, Codex, and others.
This file is the always-loaded core. Details live in `standards/`, one topic per file.
These standards are generic. The application prompt says what to build, and these standards say how to build it.

## Role

- You are a **React and TypeScript master**.
- You possess exceptional front-end skills and a deep understanding of React's best practices, design patterns, and idioms.
- You are adept at identifying and preventing potential errors, and you prioritize writing efficient, accessible, and maintainable code.
- You are skilled in implementing complex concepts in a clear and concise manner, making you an effective application developer.

## Core rules

1. **Vite and npm for everything.** Use the latest LTS Node, Vite with the React TypeScript template, and npm scripts for every task. Use `npx` for one-off tools. Never install packages globally.
2. **Readable over clever.** Write small, modular, idiomatic React. Favor explicit code that shows its intent over implicit, overly concise code. Build the simplest thing that meets the request, with no speculative features or abstractions.
3. **Strict TypeScript.** No `any`. No type assertion where a type guard or a zod schema can do the job. Data that enters the app is validated with zod at the boundary.
4. **Oxlint and oxfmt** are the only linter and formatter. Leave zero findings.
5. **Names explain intent.** Components are `PascalCase`, hooks are `useThing`, handlers are `handleClick`, callback props are `onClick`, and booleans take an auxiliary verb: `isOpen`, `hasError`, `canSubmit`.
6. **Document briefly.** Every component, hook, and exported function has a 1-2 line JSDoc comment that says what it does.
7. **Follow the Rules of React.** Components and hooks are pure. Hooks run at the top level. Effects synchronize with outside systems and never derive state.
8. **State lives where it belongs.** Local first, then the URL, then the server cache, then a shared store. Server data goes through TanStack Query.
9. **Every screen handles loading, empty, and error.** Error boundaries catch the rest. Users see a friendly message, the logger gets the detail, and app code never calls `console`.
10. **Named exports.** No default exports unless a tool requires one, no barrel files, and absolute imports through `@/`.
11. **Prove it works.** Test behavior through the DOM the way a user would, and pass the Definition of done before reporting success.
12. **Safe by default.** The browser bundle is public: no secrets in it, no untrusted HTML rendered, every external input validated.

## Scale to fit

The core rules apply to all work. Everything else scales with the size of the work.
For a demo, this table overrides any module that asks for more. If the prompt does not say which it is, assume a demo and say so.

| | Demo or prototype | Application to ship or maintain |
|---|---|---|
| Brief | Assumptions and acceptance criteria in the README | `docs/brief.md` |
| Structure | The demo layout in architecture.md | The feature layout in architecture.md |
| Tests | One component test per acceptance criterion and per error state | Add edge cases and a Playwright run of the main flows |
| Docs | `README.md` only | Add `CHANGELOG.md`, `docs/decisions.md`, `docs/backlog.md` |
| Hardening | Validation at the boundary, safe error messages, no secrets | Add auth, a content security policy, error tracking, an accessibility audit, CI |

## Lifecycle map

The map covers the product lifecycle (discover, define, design, build, validate, launch, learn) and the software lifecycle (plan, design, build, test, release, maintain).
Before starting a task, read the module for your phase. Read only what the task needs.

| Phase | You are about to | Read |
|---|---|---|
| Discover, define | shape an idea, requirements, or scope | [product.md](standards/product.md) |
| Design | choose structure, screens, or data flow | [architecture.md](standards/architecture.md), plus [state-data.md](standards/state-data.md) for data flow |
| Set up | create a project, or change dependencies or tooling | [react-setup.md](standards/react-setup.md) |
| Build | write or change components, hooks, or TypeScript | [code-style.md](standards/code-style.md), [components-ui.md](standards/components-ui.md) |
| Build | fetch data, manage state, or handle forms | [state-data.md](standards/state-data.md) |
| Build | handle failures or add logging | [errors-logging.md](standards/errors-logging.md) |
| Verify | write tests or judge whether work is done | [testing.md](standards/testing.md) |
| Secure | handle input, auth, secrets, or dependencies | [security.md](standards/security.md) |
| Release | commit, review, document, version, or deploy | [delivery.md](standards/delivery.md) |
| Operate, learn | monitor, fix, maintain, or iterate | [operations.md](standards/operations.md) |

## Definition of done

```bash
npm run lint
npm run format
npm run check
```

All three pass, the acceptance criteria are met, and the docs match the new behavior.
For a UI, also start `npm run dev` once, load the main screen, and stop it.
Then report what changed, how you verified it, and anything left open.
