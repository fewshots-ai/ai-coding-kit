# Architecture and project structure

Read when choosing structure, screens, or data flow.

## Principles

- **Simple first.** Pick the simplest design that meets today's requirements. Add an abstraction only when a second real use needs it.
- **Design before code.** For non-trivial work, settle the screens, the data each screen needs, the API contract, and the failure modes first. An application records the choice in `docs/decisions.md`.
- **Features own their code.** A feature folder holds its own components, hooks, API calls, and types. Features never import from each other. Screens in `app/` compose them.
- **Imports flow one way.** Shared code (`components/`, `hooks/`, `lib/`) is imported by `features/`, and `features/` is imported by `app/`. Nothing imports upward.
- **One component per file**, named like the file. Split a component that passes about 150 lines or mixes data fetching with layout.
- **Composition over configuration.** Build screens from small components and `children`, not from one component with many boolean props.
- **Typed boundaries.** Everything that enters the app is parsed with a zod schema: API responses, URL parameters, storage, environment variables.
- **Configuration from the environment.** `src/lib/env.ts` validates the `VITE_` variables once. Nothing else reads `import.meta.env`.
- **Flat over nested.** Create a folder only when it has something to hold. A demo starts with the layout below and grows into the feature layout.

## Layout

Tests sit next to the file they test, as `name.test.tsx`. The `@/` alias points at `src/`.

A demo uses a flat `src/`:

```text
src/
├── main.tsx            # mounts the app inside its providers
├── app.tsx             # routes and screens
├── api.ts              # fetch functions and their zod schemas
├── hooks.ts            # TanStack Query hooks
├── components/         # one component per file
└── lib/                # api-client.ts, env.ts, logger.ts
```

An application groups by feature. Paths named in the other modules refer to this layout, and a demo uses the matching flat file.

```text
my-app/
├── AGENTS.md, CLAUDE.md     # these standards, with standards/ beside them
├── package.json             # scripts and dependencies, the single source
├── package-lock.json        # committed
├── .env.example             # every VITE_ setting with a safe example value
├── index.html
├── public/                  # static files served as is
├── docs/                    # applications only: brief.md, decisions.md, backlog.md
├── src/
│   ├── main.tsx             # mounts the app inside its providers
│   ├── app/                 # app.tsx (routes), providers.tsx, screen components
│   ├── components/ui/       # shared presentational components, no data fetching
│   ├── features/<name>/     # api/, components/, hooks/, types.ts
│   ├── hooks/               # shared hooks
│   ├── lib/                 # api-client.ts, env.ts, logger.ts
│   ├── stores/              # shared client state, only when needed
│   └── test/                # setup.ts, render.tsx, fixtures
└── e2e/                     # Playwright tests, applications only
```
