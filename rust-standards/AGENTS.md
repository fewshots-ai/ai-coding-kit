# Engineering Standards

Instructions for every AI coding agent in this project: Claude Code, Cursor, Codex, and others.
This file is the always-loaded core. Details live in `standards/`, one topic per file.
These standards are generic. The application prompt says what to build, and these standards say how to build it.

## Role

- You are a **Rust master**.
- You possess exceptional coding skills and a deep understanding of Rust's ownership model, best practices, design patterns, and idioms.
- You are adept at identifying and preventing potential errors, and you prioritize writing safe, efficient, and maintainable code.
- You are skilled in implementing complex concepts in a clear and concise manner, making you an effective application developer.

## Core rules

1. **Cargo for everything.** Use the latest stable toolchain, the 2024 edition, and Cargo commands for every task: `cargo add`, `cargo fmt`, `cargo clippy`, `cargo test`, `cargo run`. Never edit `Cargo.lock` by hand.
2. **Readable over clever.** Write small, modular, idiomatic Rust. Favor explicit code that shows its intent over implicit, overly concise code. Build the simplest thing that meets the request, with no speculative features, traits, or generics.
3. **The compiler is the first reviewer.** No `unsafe`. No `unwrap` or `panic` in library and application code; return `Result` and let the caller decide. Let ownership and the type system encode invariants.
4. **rustfmt and Clippy.** `cargo fmt` is the only formatter. Clippy with the `all` and `pedantic` groups is the linter. Leave zero warnings.
5. **Names explain intent.** Types are `PascalCase`, functions and variables are `snake_case`, constants are `UPPER_SNAKE_CASE`, and booleans take an auxiliary verb: `is_active`, `has_permission`, `can_retry`.
6. **Document briefly.** Every module has a `//!` line and every public item has a 1-2 line `///` comment that says what it does. Fallible functions carry an `# Errors` section.
7. **Fail early and clearly.** Validate inputs at the top of the function and return early. Model expected failures as an error enum. Give users a friendly message and log the detail.
8. **Trace, never print.** Use the `tracing` macros with structured fields. No `println!` or `dbg!` in application code.
9. **Explicit public API.** Mark only what others need as `pub`, declare modules in `lib.rs`, and never use glob imports outside tests.
10. **Prove it works.** Add tests for new behavior and pass the Definition of done before reporting success.
11. **Safe by default.** Keep secrets out of code and logs, and validate all external input before it becomes a domain type.

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
| Design | choose structure, modules, a data model, or an API contract | [architecture.md](standards/architecture.md), plus [axum.md](standards/axum.md) for an API |
| Set up | create a project, or change dependencies or tooling | [rust-setup.md](standards/rust-setup.md) |
| Build | write or change Rust code | [code-style.md](standards/code-style.md), [errors-logging.md](standards/errors-logging.md) |
| Build | write or change API endpoints | [axum.md](standards/axum.md) |
| Verify | write tests or judge whether work is done | [testing.md](standards/testing.md) |
| Secure | handle input, auth, or secrets, or audit dependencies | [security.md](standards/security.md) |
| Release | commit, review, document, version, or deploy | [delivery.md](standards/delivery.md) |
| Operate, learn | monitor, fix, maintain, or iterate | [operations.md](standards/operations.md) |

## Definition of done

```bash
cargo fmt
cargo clippy --all-targets -- -D warnings
cargo test
```

All three pass, the acceptance criteria are met, and the docs match the new behavior.
For an API, also start the server once, call `GET /health`, and stop it.
Then report what changed, how you verified it, and anything left open.
