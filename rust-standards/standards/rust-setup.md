# Rust and Cargo setup

Read when creating a project, or changing dependencies or tooling.

## Rules

- **Latest stable toolchain.** Check `rustc --version`. If it is more than a few months behind the current stable release, ask the user to run `rustup update`. Use the 2024 edition for new crates.
- **Cargo owns the toolchain**: dependencies, the lockfile, building, testing, formatting, linting, and docs. Prefer `cargo add` and `cargo remove` over hand-editing dependency lines.
- **Lints live in `Cargo.toml`** under `[lints]`, so every machine and CI run applies the same rules. Do not pass lint flags on the command line except `-D warnings`.
- **rustfmt** with the `rustfmt.toml` below. **Clippy** with the `all` and `pedantic` groups, plus the restriction rules below.
- **Commit** `Cargo.toml`, `Cargo.lock`, and `rustfmt.toml`. Never commit `target/` or `.env`.
- **Few, well-known crates.** Add a dependency only when it is needed and well maintained. Enable only the features you use.
- **Optional tools** such as `cargo-nextest`, `cargo-deny`, and `cargo-audit` are installed per machine with `cargo install`. Ask before installing one; they are not required by the Definition of done.

## Command map

| Task | Command |
|---|---|
| Create a binary crate, or a library | `cargo new my-app`, `cargo new --lib my-lib` |
| Add or remove a dependency | `cargo add <crate>`, `cargo add <crate> --features a,b`, `cargo remove <crate>` |
| Add a test-only dependency | `cargo add --dev <crate>` |
| Format, or check formatting | `cargo fmt`, `cargo fmt --check` |
| Lint everything, warnings as errors | `cargo clippy --all-targets -- -D warnings` |
| Run tests, or one test by name | `cargo test`, `cargo test <name>` |
| Run the app | `cargo run`, with settings in environment variables |
| Build for release | `cargo build --release` |
| Build and open the docs | `cargo doc --no-deps --open` |
| See the dependency tree | `cargo tree` |
| Update within the lockfile's ranges | `cargo update` |

## New project

Run this in the project root, the folder that holds `AGENTS.md`. The folder name becomes the crate name.

```bash
cargo init --name my-app
cargo add axum tokio --features tokio/full
cargo add serde --features derive
cargo add serde_json thiserror tracing tracing-subscriber --features tracing-subscriber/env-filter
cargo add --dev tower --features util
cargo add --dev http-body-util
```

Then add the settings below and run the Definition of done once before writing code.

## Settings

Append to `Cargo.toml`:

```toml
[lints.rust]
unsafe_code = "forbid"
missing_docs = "warn"

[lints.clippy]
all = { level = "warn", priority = -1 }
pedantic = { level = "warn", priority = -1 }
unwrap_used = "warn"
module_name_repetitions = "allow"
print_stdout = "warn"
print_stderr = "warn"
dbg_macro = "warn"
```

`rustfmt.toml`:

```toml
max_width = 100
use_small_heuristics = "Max"
```

Clippy runs with `--all-targets`, so tests see the same lints. Use `expect("why")` in tests, or add `#![allow(clippy::unwrap_used)]` at the top of a test file that prefers `unwrap`.
