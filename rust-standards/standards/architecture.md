# Architecture and project structure

Read when choosing structure, modules, or a data model.

## Principles

- **Simple first.** Pick the simplest design that meets today's requirements. Add a trait only when a second real implementation exists, and a generic only when a concrete type will not do.
- **Design before code.** For non-trivial work, settle the data types, the API contract, the main flow, and the failure modes first. An application records the choice in `docs/decisions.md`.
- **Library plus thin binary.** Logic lives in `src/lib.rs` and its modules so tests and other binaries can use it. `src/main.rs` only reads configuration, sets up tracing, and calls into the library.
- **Modules own their feature.** A feature module holds its types, rules, and routes. Modules talk through public functions, never through each other's internals.
- **Dependencies point one way.** Routes call services. Services call repositories and clients. Nothing calls back up, and no layer is skipped.
- **Make invalid states unrepresentable.** Use enums for closed sets, newtypes for ids and units, and constructors that validate. Parse input into these types once, at the boundary.
- **Own by default, borrow at the edges.** Take `&str` and `&[T]` in function parameters, return owned values, and reach for `Arc` and `Mutex` only for state shared across tasks.
- **Configuration from the environment.** One config struct, read once at startup from environment variables. No hard-coded URLs, keys, or paths.
- **Flat over nested.** Create a module file only when it has something to hold. A demo starts with the layout below and grows into the full one.

## Layout

A demo uses a few files:

```text
src/
├── main.rs      # config, tracing, serve
├── lib.rs       # build_app() and module declarations
├── error.rs     # AppError and its IntoResponse
├── users.rs     # types, rules, and routes for one feature
└── ...
tests/           # behavior tests through the public API
```

An application groups by feature. Paths named in the other modules refer to this layout, and a demo uses the matching single file.

```text
my-app/
├── AGENTS.md, CLAUDE.md     # these standards, with standards/ beside them
├── Cargo.toml               # dependencies, lints, and metadata, the single source
├── Cargo.lock               # committed
├── rustfmt.toml
├── .env.example             # every setting with a safe example value
├── docs/                    # applications only: brief.md, decisions.md, backlog.md
├── src/
│   ├── main.rs              # config, tracing, serve
│   ├── lib.rs               # build_app() and module declarations
│   ├── config.rs            # Config read from the environment
│   ├── error.rs             # AppError, error codes, IntoResponse
│   └── users/
│       ├── mod.rs           # public types for the feature
│       ├── routes.rs        # handlers and the feature router
│       ├── service.rs       # business rules
│       └── repository.rs    # storage or external clients
└── tests/                   # behavior tests through the public API
```
