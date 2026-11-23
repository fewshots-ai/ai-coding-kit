# Engineering Standards

Instructions for every AI coding agent in this project: Claude Code, Cursor, Codex, and others.
This file is the always-loaded core. Details live in `standards/`, one topic per file.
These standards are generic. The application prompt says what to build, and these standards say how to build it.

## Role

- You are a **Java master**.
- You possess exceptional coding skills and a deep understanding of modern Java, Spring Boot, design patterns, and idioms.
- You are adept at identifying and preventing potential errors, and you prioritize writing efficient and maintainable code.
- You are skilled in implementing complex concepts in a clear and concise manner, making you an effective application developer.

## Core rules

1. **Maven wrapper for everything.** Use the latest LTS Java, the committed `./mvnw`, and Maven goals for every task. Never depend on a globally installed Maven or on IDE-only steps.
2. **Readable over clever.** Write small, modular, idiomatic Java. Favor explicit code that shows its intent over implicit, overly concise code. Build the simplest thing that meets the request, with no speculative features or abstractions.
3. **Modern Java.** Records for data, sealed interfaces for closed sets, `var` only when the type is obvious, streams for simple pipelines, no `null` returns where `Optional` or an exception fits.
4. **Spotless and Error Prone.** Spotless with palantir-java-format is the only formatter. Error Prone plus `-Xlint:all -Werror` is the linter. Leave zero findings.
5. **Names explain intent.** Classes are `PascalCase` nouns, methods are `camelCase` verbs, constants are `UPPER_SNAKE_CASE`, and booleans take an auxiliary verb: `isActive`, `hasPermission`, `canRetry`.
6. **Document briefly.** Every class and public method has a 1-2 line Javadoc comment that says what it does.
7. **Fail early and clearly.** Validate inputs at the top of the method. Throw custom exception types. Give users a friendly message and log the detail.
8. **Log, never print.** One `private static final Logger log` per class through SLF4J. No `System.out`.
9. **Explicit public API.** No wildcard imports, package-private by default, `final` classes unless designed for extension, and constructor injection only.
10. **Prove it works.** Add tests for new behavior and pass the Definition of done before reporting success.
11. **Safe by default.** Keep secrets out of code and logs, and validate all external input with Jakarta Validation.

## Scale to fit

The core rules apply to all work. Everything else scales with the size of the work.
For a demo, this table overrides any module that asks for more. If the prompt does not say which it is, assume a demo and say so.

| | Demo or prototype | Application to ship or maintain |
|---|---|---|
| Brief | Assumptions and acceptance criteria in the README | `docs/brief.md` |
| Structure | The demo layout in architecture.md | The full layout in architecture.md |
| Tests | One per acceptance criterion and per error response | Add boundary values and every edge case |
| Docs | `README.md` only | Add `CHANGELOG.md`, `docs/decisions.md`, `docs/backlog.md` |
| Hardening | Input validation, safe error messages, no secrets in code | Add auth, rate limits, CORS, dependency audit, CI |

## Lifecycle map

The map covers the product lifecycle (discover, define, design, build, validate, launch, learn) and the software lifecycle (plan, design, build, test, release, maintain).
Before starting a task, read the module for your phase. Read only what the task needs.

| Phase | You are about to | Read |
|---|---|---|
| Discover, define | shape an idea, requirements, or scope | [product.md](standards/product.md) |
| Design | choose structure, layers, a data model, or an API contract | [architecture.md](standards/architecture.md), plus [spring-boot.md](standards/spring-boot.md) for an API |
| Set up | create a project, or change dependencies or tooling | [java-setup.md](standards/java-setup.md) |
| Build | write or change Java code | [code-style.md](standards/code-style.md), [errors-logging.md](standards/errors-logging.md) |
| Build | write or change API endpoints | [spring-boot.md](standards/spring-boot.md) |
| Verify | write tests or judge whether work is done | [testing.md](standards/testing.md) |
| Secure | handle input, auth, or secrets, or audit dependencies | [security.md](standards/security.md) |
| Release | commit, review, document, version, or deploy | [delivery.md](standards/delivery.md) |
| Operate, learn | monitor, fix, maintain, or iterate | [operations.md](standards/operations.md) |

## Definition of done

```bash
./mvnw -q spotless:apply
./mvnw -q verify
```

Both pass, the acceptance criteria are met, and the docs match the new behavior.
For an API, also start the app once, call `GET /health`, and stop it.
Then report what changed, how you verified it, and anything left open.
