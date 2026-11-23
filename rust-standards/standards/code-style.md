# Code style

Read when writing or changing Rust code. rustfmt and Clippy enforce much of this: fix findings, do not `allow` them away.

## Idiomatic and explicit

- Let the compiler carry the proof: no `unsafe`, no `unwrap` or `expect` in library and application code, no `as` casts where `From` or `try_into` fits.
- Propagate with `?` and return early. Keep the happy path last and unindented.
- Prefer iterators and combinators for simple transformations, and a plain loop when the logic has state or early exit. Never nest closures three deep.
- Take `&str`, `&[T]`, and `impl AsRef<Path>` in parameters. Return owned values. Clone only when you need an owned copy, and say why if it is in a hot path.
- Derive what you can: `Debug` on every type, `Clone`, `PartialEq`, `Eq`, `Hash`, `Default` where they make sense, `Serialize` and `Deserialize` on boundary types.
- Use `match` for enums and let the compiler check exhaustiveness. No `_ =>` arm on your own enums.
- Keep functions small and single-purpose: about 40 lines and at most 2 levels of nesting. More than 4 parameters is a sign to group them in a struct.
- Give a named constant to any number or string whose meaning is not obvious or that appears more than once.
- An `#[allow]` needs the narrowest scope and a comment with the reason.

## Naming

| Kind | Convention | Example |
|---|---|---|
| Crate, module, file | `snake_case` | `user_service.rs` |
| Type, trait, enum variant | `PascalCase` noun | `UserService`, `NotFound` |
| Function, method, variable | `snake_case` starting with a verb for functions | `create_user`, `retry_count` |
| Boolean | auxiliary verb prefix | `is_active`, `has_permission`, `can_edit`, `should_retry` |
| Collection | plural, or `x_by_y` for a map | `users`, `users_by_id` |
| Constant, static | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Newtype | the thing it wraps | `UserId(u64)` |
| Conversion method | `as_`, `to_`, `into_` by cost and ownership | `as_str`, `to_owned`, `into_inner` |

Names say what a thing is, not its type or history. Avoid `data`, `info`, `tmp`, `result2`, and abbreviations beyond the common ones (`id`, `len`, `idx`).

## Documentation

- Every module starts with a `//!` line that says what it holds. Every public item has a 1-2 line `///` comment that says what it does. The `missing_docs` lint enforces the public items.
- A fallible function has an `# Errors` section, and a function that can panic has a `# Panics` section. Clippy enforces both.
- Comments explain why, never what. Delete commented-out code.
- Tests are exempt: the test name is the description.

## Modules, imports, and visibility

- Declare modules in `lib.rs`. Keep `main.rs` to configuration, tracing setup, and one call into the library.
- `pub` only what another module or crate needs; `pub(crate)` for the rest of the crate; private by default.
- Group imports as `std`, then external crates, then `crate::`, separated by blank lines. No glob imports outside tests and preludes.
- One type or one feature per file. Split a file that passes about 300 lines or mixes concerns.
