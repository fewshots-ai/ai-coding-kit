# Architecture and project structure

Read when choosing structure, layers, or a data model.

## Principles

- **Simple first.** Pick the simplest design that meets today's requirements. Add an abstraction only when a second real use needs it.
- **Design before code.** For non-trivial work, settle the data model, the API contract, the main flow, and the failure modes first. An application records the choice in `docs/decisions.md`.
- **One responsibility per unit.** Each module, class, and function does one thing. Split a file that mixes concerns or passes about 300 lines.
- **Dependencies point one way.** Routes call services. Services call repositories and clients. Nothing calls back up, and no layer is skipped.
- **Composition over inheritance.** Pass collaborators in as parameters so tests can replace them.
- **Typed boundaries.** Data crossing a layer or process boundary is a Pydantic model, never a loose `dict`.
- **Configuration from the environment.** Use one typed settings object. No hard-coded URLs, keys, or paths.
- **No mutable module-level state.** Create shared state once at startup and pass it to whatever needs it.
- **Flat over nested.** Create a folder only when it has something to hold. A demo starts with the single-file layout below and grows into the full one.

## Layout

Every folder under `app/` has an `__init__.py`. Without it the dev server cannot import the app.

A demo uses single files:

```text
app/
├── main.py                  # create_app(), lifespan, error handlers
├── routes.py                # routers and their dependencies
├── schemas.py               # request and response models
├── services.py              # business logic and in-memory data
└── errors.py                # AppError and its subclasses
tests/
```

Add `config.py` when there are settings and `repositories.py` when there is real data access.
An application uses the full layout. Paths named in the other modules refer to it, and a demo uses the matching single file.

```text
my-app/
├── AGENTS.md, CLAUDE.md     # these standards, with standards/ beside them
├── pyproject.toml           # dependencies and all tool settings
├── uv.lock                  # committed
├── .env.example             # every setting with a safe example value
├── README.md
├── docs/                    # applications only: brief.md, decisions.md, backlog.md
├── app/
│   ├── main.py              # create_app(), lifespan, routers, error handlers
│   ├── api/
│   │   ├── dependencies.py  # shared Depends providers
│   │   └── routes/          # one APIRouter per feature, HTTP concerns only
│   ├── core/                # config.py, errors.py, logging.py
│   ├── schemas/             # Pydantic request and response models
│   ├── models/              # domain and database models
│   ├── services/            # business logic, no HTTP and no SQL
│   └── repositories/        # data access and external API clients
└── tests/                   # mirrors app/
```

A library or command-line tool uses the `src/<package>/` layout from `uv init --package` instead of `app/`.
