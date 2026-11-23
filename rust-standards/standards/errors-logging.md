# Errors and logging

Read when writing or changing Rust code. Every I/O call and parse can fail, so this applies to most of it.

## Errors

- **Guard clauses first.** Check inputs, preconditions, and edge cases at the top of the function and return `Err` early. Keep the happy path last and unindented.
- **An error enum per boundary.** The application has one `AppError` in `error.rs`, derived with `thiserror`, whose variants are the expected failures: `NotFound`, `Conflict`, `ExternalService`, and so on. Each maps to a status code and a stable error code.
- **Error factories.** Build recurring errors through an associated function so the wording stays consistent: `AppError::for_resource("User", user_id)`.
- **Propagate with `?`.** Add a `From` impl or a `map_err` at the point where a lower-level error becomes an `AppError`, keeping the source with `#[source]` or `#[from]`.
- **No panics on the request path.** `unwrap`, `expect`, indexing, and `panic!` are for impossible states only, each with a comment that says why it is impossible. A poisoned lock is recovered with `unwrap_or_else(PoisonError::into_inner)`.
- **`anyhow` in binaries only.** The library and handlers return typed errors. `main` may use `anyhow::Result` to add context on startup failures.
- **Never fail silently.** No `let _ = fallible()`, no `.ok()` to discard an error you should handle.
- **Two audiences.** Users get a short, friendly, actionable message from the error's `Display`. Logs get the variant, the source chain, and what the user was doing.
- **Convert in one place.** `impl IntoResponse for AppError` turns errors into the response shape. Handlers never build error bodies.

## Logging

- Use `tracing` macros with structured fields, not formatted strings: `tracing::info!(user_id = user.id, "user created")`. Never `println!`, `eprintln!`, or `dbg!` in application code.
- Initialize `tracing_subscriber` once in `main` with an `EnvFilter`, default `info`, so `RUST_LOG` controls verbosity.
- Levels: `debug` for diagnostics, `info` for business events, `warn` for handled problems, `error` for failures that need attention.
- Log each failure once, where it is handled. Never log tokens, passwords, or personal data.
- An application adds a `TraceLayer` so every request gets a span with method, path, status, and latency, and emits JSON logs in production.

## Pattern

```rust
/// An expected failure. Its message is safe to show to users.
#[derive(Debug, thiserror::Error)]
pub enum AppError {
    /// A requested resource does not exist.
    #[error("{0}")]
    NotFound(String),
    /// The request conflicts with the current state.
    #[error("{0}")]
    Conflict(String),
}

impl AppError {
    /// Build a consistently worded not-found error for any resource.
    pub fn for_resource(resource_name: &str, resource_id: impl std::fmt::Display) -> Self {
        Self::NotFound(format!("{resource_name} '{resource_id}' was not found."))
    }
}

impl UserService {
    /// Return one user by id.
    ///
    /// # Errors
    /// Returns `AppError::NotFound` when the id is unknown.
    pub fn get_user(&self, user_id: u64) -> Result<User, AppError> {
        let users_by_id =
            self.users_by_id.lock().unwrap_or_else(std::sync::PoisonError::into_inner);
        users_by_id.get(&user_id).cloned().ok_or_else(|| AppError::for_resource("User", user_id))
    }
}
```
