# Architecture and project structure

Read when choosing structure, layers, or a data model.

## Principles

- **Simple first.** Pick the simplest design that meets today's requirements. Add an abstraction only when a second real use needs it. No interface with a single implementation.
- **Design before code.** For non-trivial work, settle the data model, the API contract, the main flow, and the failure modes first. An application records the choice in `docs/decisions.md`.
- **Package by feature.** A feature package holds its controller, service, repository, and records. Features talk through their services, never through each other's repositories.
- **Dependencies point one way.** Controllers call services. Services call repositories and clients. Nothing calls back up, and no layer is skipped.
- **Constructor injection.** Every collaborator arrives through the constructor as a `final` field. No field injection, no static singletons, no service locators.
- **Typed boundaries.** Data crossing a layer or process boundary is a record validated with Jakarta Validation, never a `Map` or a raw entity.
- **Configuration from the environment.** One `@ConfigurationProperties` record per concern, bound from `application.properties` and environment variables. No hard-coded URLs, keys, or paths.
- **No mutable shared state.** Services are stateless. State lives in a store behind a repository, or in a thread-safe structure created once.
- **Flat over nested.** Create a package only when it has something to hold. A demo starts with the layout below and grows into the full one.

## Layout

A demo uses one package beside the application class:

```text
src/main/java/com/example/app/
├── AppApplication.java      # @SpringBootApplication
├── ApiExceptionHandler.java # @RestControllerAdvice, one error shape
├── AppException.java        # base exception and its subclasses
├── UserController.java      # thin HTTP layer
├── UserService.java         # business rules
└── User.java, UserCreate.java  # records
src/test/java/com/example/app/
```

An application packages by feature. Paths named in the other modules refer to this layout, and a demo uses the matching flat file.

```text
my-app/
├── AGENTS.md, CLAUDE.md             # these standards, with standards/ beside them
├── pom.xml                          # dependencies and plugins, the single source
├── mvnw, mvnw.cmd, .mvn/            # committed Maven wrapper and JVM settings
├── docs/                            # applications only: brief.md, decisions.md, backlog.md
├── src/main/java/com/example/app/
│   ├── AppApplication.java
│   ├── api/                         # ApiExceptionHandler, HealthController, shared web config
│   ├── core/                        # AppException and subclasses, config records, logging
│   └── user/                        # UserController, UserService, UserRepository, records
├── src/main/resources/
│   ├── application.properties       # defaults for every setting
│   └── db/migration/                # Flyway migrations, when there is a database
└── src/test/java/com/example/app/   # mirrors src/main
```
