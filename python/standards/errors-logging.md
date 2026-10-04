# Errors and logging

Read when writing or changing Python code. Every function can fail, so this applies to all of it.

## Errors

- **Guard clauses first.** Check inputs, preconditions, and edge cases at the top of the function, and raise or return at once. Keep the happy path last and unindented. No `else` after `return` or `raise`.
- **Custom error types.** Raise subclasses of one base `AppError` from `app/core/errors.py`. Each has a stable `error_code`, a `status_code` for the API layer, and a `message` that is safe to show users.
- **Error factories.** Build recurring errors through a classmethod so the wording stays consistent: `NotFoundError.for_resource("Order", order_id)`.
- **Catch narrowly.** Catch only the specific exception you can handle, in the smallest `try` block. Never use bare `except:` or `except Exception: pass`.
- **Translate at boundaries.** Wrap third-party exceptions in an `AppError` with `raise ... from error` so the cause is kept.
- **Never fail silently.** Handle the error or let it propagate. Do not swallow an exception and return `None` or a default in its place.
- **Two audiences.** Users get a short, friendly, actionable message with no internals. Logs get the full detail and traceback.
- **Convert in one place.** A single outer layer turns errors into output: exception handlers in an API, `main()` in a command-line tool.

## Logging

- One logger per module: `logger = logging.getLogger(__name__)`. Never `print`.
- Configure logging once at startup, in one place. Take the level from settings when the app has them.
- Levels: `debug` for diagnostics, `info` for business events, `warning` for handled problems, `error` for failures that need attention.
- Log startup, shutdown, and each call to an external service with its duration.
- Pass values as arguments, not f-strings: `logger.info("Order paid: order_id=%s", order.id)`.
- Log each error once, where it is handled. Use `logger.exception(...)` inside `except` to keep the traceback.
- Add identifiers that make a line searchable, such as ids, counts, and durations. Never log secrets, tokens, or personal data.

## Pattern

```python
class AppError(Exception):
    """Base for expected failures. Its message is safe to show to users."""

    status_code: HTTPStatus = HTTPStatus.INTERNAL_SERVER_ERROR
    error_code: str = "internal_error"

    def __init__(self, message: str) -> None:
        super().__init__(message)
        self.message = message


class NotFoundError(AppError):
    """Raised when a requested resource does not exist."""

    status_code = HTTPStatus.NOT_FOUND
    error_code = "not_found"

    @classmethod
    def for_resource(cls, resource_name: str, resource_id: object) -> Self:
        """Build a consistently worded not-found error for any resource."""
        return cls(f"{resource_name} '{resource_id}' was not found.")


class OrderService:
    """Pays orders and enforces the payment rules."""

    def pay_order(self, order_id: int) -> Receipt:
        """Charge an unpaid order and return its receipt."""
        order = self._orders_by_id.get(order_id)
        if order is None:
            raise NotFoundError.for_resource("Order", order_id)
        if order.is_paid:
            raise ConflictError("This order has already been paid.")

        try:
            receipt = self._payment_client.charge(order.id, order.total)
        except PaymentTimeoutError as error:
            raise ExternalServiceError("Payment timed out. Please retry.") from error

        order.is_paid = True
        logger.info("Order paid: order_id=%s amount=%s", order.id, order.total)
        return receipt
```
