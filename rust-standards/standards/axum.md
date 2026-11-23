# Axum

Read when designing, writing, or changing API endpoints.

- **App builder.** `lib.rs` exposes `pub fn build_app() -> Router` that creates the state, mounts feature routers under `/api/v1`, and adds layers. `main.rs` binds the listener and calls `axum::serve`.
- **Shared state.** Put app-wide objects in one state type behind `Arc`, pass it with `.with_state`, and read it in handlers through `State<Arc<T>>`. Never use globals or `static mut`.
- **Thin handlers.** A handler extracts input, calls one service method, and returns the result. No business logic and no storage access. Handlers are private functions; only the feature `router()` is public.
- **Typed input and output.** Request bodies derive `Deserialize`, responses derive `Serialize`, and both live in the feature module. Validate in the service constructor or a `TryFrom`, so the handler never sees invalid data.
- **Declare the contract.** Return `Result<Json<T>, AppError>`, or `(StatusCode::CREATED, Json<T>)` for creation. Use `Path`, `Query`, and `Json` extractors with concrete types, never `Value`.
- **One error shape.** Every error is an `AppError`, and its `IntoResponse` returns `{"error": {"code": "...", "message": "..."}}`. Axum's own rejections for bad JSON or paths keep their default shape.
- **Locks and awaits.** Hold a `std::sync::Mutex` only around the check and the write that depends on it, and never across an `.await`. Use `tokio::sync` locks when you must await while holding one.
- **Blocking work** runs in `tokio::task::spawn_blocking`. Never block the runtime with synchronous I/O or heavy CPU in a handler.
- **Operable.** Provide `GET /health` at the root, outside `/api/v1`, returning `{"status": "ok"}`. An application adds `TraceLayer`, a request timeout, a body size limit, and graceful shutdown.
- **REST conventions.** Plural nouns (`/users/{user_id}`) and the right status codes: 201 created, 204 deleted, 404 missing, 409 conflict, 422 invalid. An application gives every list endpoint `limit` and `offset` with a default and a maximum.

## Pattern

```rust
/// The user routes, mounted by the app under `/api/v1`.
pub fn router() -> Router<Arc<UserService>> {
    Router::new().route("/users", post(create_user)).route("/users/{user_id}", get(read_user))
}

/// Register a new user.
async fn create_user(
    State(user_service): State<Arc<UserService>>,
    Json(new_user): Json<UserCreate>,
) -> Result<(StatusCode, Json<User>), AppError> {
    let user = user_service.create_user(new_user)?;
    Ok((StatusCode::CREATED, Json(user)))
}

/// Return one user by id.
async fn read_user(
    State(user_service): State<Arc<UserService>>,
    Path(user_id): Path<u64>,
) -> Result<Json<User>, AppError> {
    Ok(Json(user_service.get_user(user_id)?))
}
```
