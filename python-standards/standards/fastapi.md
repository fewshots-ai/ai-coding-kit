# FastAPI

Read when designing, writing, or changing API endpoints.

- **App factory.** `app/main.py` defines `create_app()` and `app = create_app()`. Use a `lifespan` context manager for startup and shutdown.
- **One router per feature** in `app/api/routes/`, each with a `prefix` and `tags`. Mount feature routers under `/api/v1`.
- **Thin routes.** A route reads input, calls one service method, and returns the result. No business logic and no data access.
- **A schema per purpose.** Use separate Pydantic models for input and output: `UserCreate`, `UserUpdate`, `UserRead`. Never expose database or internal models.
- **Declare the contract.** The return annotation is the response model. Set `status_code` for anything other than 200. The route docstring becomes the OpenAPI description.
- **Inject dependencies.** Provide settings, services, sessions, and the current user with `Annotated[..., Depends(...)]`, defined once as an alias such as `UserServiceDep` in `app/api/dependencies.py`.
- **Shared state.** Create app-wide objects in `lifespan`, keep them on `app.state`, and hand them out through a dependency. Requests run concurrently, so the class that owns in-memory data guards it with a lock, held across each check and the write that depends on it.
- **Typed settings.** When the app has configuration, keep it in one `pydantic-settings` class in `app/core/config.py`, served by a cached `get_settings()`, and keep `.env.example` complete.
- **One error shape.** Services raise `AppError` subclasses. A handler registered in `create_app()` returns them as an `ErrorResponse` schema: `{"error": {"code": "...", "message": "..."}}`. FastAPI's own 422, 404, and 405 responses keep the framework's default shape. Do not raise `HTTPException` outside the API layer.
- **Unexpected errors.** A catch-all handler returns a generic 500 message in the same shape and logs one line. The server logs the traceback, so the handler does not.
- **Async correctly.** Use `async def` only when the route awaits its I/O. Otherwise use plain `def`, which FastAPI runs in a thread pool. Never block the event loop.
- **REST conventions.** Use plural nouns (`/users/{user_id}`) and the right status codes: 201 created, 204 deleted, 404 missing, 409 conflict, 422 invalid. An application gives every list endpoint `limit` and `offset` with a default and a maximum.
- **Operable.** Provide `GET /health` at the root, outside `/api/v1`, returning `{"status": "ok"}`.

## Pattern

```python
@router.get("/{user_id}")
def read_user(user_id: int, user_service: UserServiceDep) -> UserRead:
    """Return one user by id."""
    return user_service.get_user(user_id)


async def handle_app_error(request: Request, error: AppError) -> JSONResponse:
    """Turn an expected failure into the standard error response."""
    logger.warning("%s on %s: %s", error.error_code, request.url.path, error.message)
    body = ErrorResponse.from_error(error)
    return JSONResponse(status_code=error.status_code, content=body.model_dump())
```
