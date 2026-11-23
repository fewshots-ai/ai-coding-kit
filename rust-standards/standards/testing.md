# Testing

Read when writing tests or judging whether work is done.

- **Built-in test harness.** Unit tests sit in a `#[cfg(test)] mod tests` at the bottom of the file they test. Behavior tests sit in `tests/` and use only the public API. Run them with `cargo test`.
- **Test behavior, not implementation.** One behavior per test, named for it: `create_user_rejects_duplicate_email`. No assertions on private fields.
- **Arrange, act, assert**, separated by blank lines. No loops or conditionals in a test. Write a small helper or a table of cases for variations.
- **Test the API through the router.** Build it with `build_app()` and send requests with `tower::ServiceExt::oneshot`. Assert the status and the JSON body, including the error shape.
- **Test services directly** as plain values. Introduce a trait and a fake only when a collaborator talks to the network or a database.
- **Async tests** use `#[tokio::test]`. No `std::thread::sleep`, no timing assumptions.
- **Isolate.** No real network or services. An application uses a test container or an in-memory implementation behind the repository trait.
- **Tests may unwrap.** Add `#![allow(clippy::unwrap_used)]` at the top of a test file, and use `expect("why")` where the message helps.
- **Doc tests** on public functions show the intended use and run with the suite.
- **Trace to requirements.** Every acceptance criterion has a test. Every bug fix starts with a failing test that reproduces the bug.
- **Warnings are work.** Fix deprecation warnings instead of silencing them.
- **Never weaken a test to get green.** Fix the code, or explain why the test was wrong.

## Pattern

```rust
async fn send(app: axum::Router, request: Request<Body>) -> (StatusCode, Value) {
    let response = app.oneshot(request).await.expect("request should complete");
    let status = response.status();
    let bytes = response.into_body().collect().await.expect("body should be readable").to_bytes();
    let body = serde_json::from_slice(&bytes).unwrap_or(Value::Null);
    (status, body)
}

#[tokio::test]
async fn read_user_returns_not_found_for_unknown_id() {
    let request =
        Request::get("/api/v1/users/999").body(Body::empty()).expect("request should build");

    let (status, body) = send(build_app(), request).await;

    assert_eq!(status, StatusCode::NOT_FOUND);
    assert_eq!(
        body,
        json!({"error": {"code": "not_found", "message": "User '999' was not found."}})
    );
}
```
