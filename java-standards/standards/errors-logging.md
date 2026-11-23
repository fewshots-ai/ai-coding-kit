# Errors and logging

Read when writing or changing Java code. Every call can fail, so this applies to most of it.

## Errors

- **Guard clauses first.** Check inputs, preconditions, and edge cases at the top of the method, and throw or return at once. Keep the happy path last and unindented.
- **Custom exception types.** Throw subclasses of one base `AppException` from `core/`. Each carries an `HttpStatus`, a stable `code`, and a message that is safe to show users.
- **Error factories.** Build recurring errors through a static factory so the wording stays consistent: `NotFoundException.forResource("User", userId)`.
- **Unchecked by default.** Domain exceptions extend `RuntimeException`. Catch a checked exception at the boundary where it occurs and rethrow it as an `AppException` with the cause attached.
- **Catch narrowly.** Catch only the specific exception you can handle, in the smallest `try` block. `catch (Exception e)` belongs only in the exception handler.
- **Never fail silently.** No empty `catch`, no returning `null` or a default to hide a failure.
- **Two audiences.** Users get a short, friendly, actionable message. Logs get the exception, the stack trace, and what the user was doing.
- **Convert in one place.** `ApiExceptionHandler` turns every exception into a problem detail. Controllers and services never build error responses.
- **Close resources** with try-with-resources, never with manual `finally` blocks.

## Logging

- One logger per class: `private static final Logger log = LoggerFactory.getLogger(UserService.class);`. Never `System.out` or `printStackTrace`.
- Use SLF4J placeholders, not concatenation: `log.info("User created: userId={}", user.id())`.
- Levels: `debug` for diagnostics, `info` for business events, `warn` for handled problems, `error` for failures that need attention, with the exception as the last argument.
- Log each failure once, where it is handled. Never log passwords, tokens, or personal data.
- Production logs are JSON with a request id; `application.properties` configures that, not code.

## Pattern

```java
/** Base class for expected failures. Carries a status, a stable code, and a message safe to show users. */
public abstract class AppException extends RuntimeException {

    private final HttpStatus status;
    private final String code;

    protected AppException(HttpStatus status, String code, String message) {
        super(message);
        this.status = status;
        this.code = code;
    }

    public HttpStatus status() {
        return status;
    }

    public String code() {
        return code;
    }
}

/** Raised when a requested resource does not exist. */
public final class NotFoundException extends AppException {

    private NotFoundException(String message) {
        super(HttpStatus.NOT_FOUND, "not_found", message);
    }

    /** Build a consistently worded not-found error for any resource. */
    public static NotFoundException forResource(String resourceName, Object resourceId) {
        return new NotFoundException("%s '%s' was not found.".formatted(resourceName, resourceId));
    }
}
```
