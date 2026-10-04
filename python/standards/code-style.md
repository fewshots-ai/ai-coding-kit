# Code style

Read when writing or changing Python code. Ruff enforces most of this: fix findings, do not silence them.

## Pythonic and explicit

- Follow PEP 8. Ruff is the only linter and formatter, at its default line length of 88.
- Keep the Zen of Python in mind: explicit over implicit, simple over complex, flat over nested, readability counts.
- Favor explicit code that shows its intent over implicit, overly concise code. No nested comprehensions, chained ternaries, or dense one-liners.
- Keep functions small and single-purpose: about 40 lines and at most 2 levels of nesting. More than 4 parameters is a sign to group them in a model.
- Use modern idioms: f-strings, `pathlib`, context managers, `enumerate`, `zip`, `match`, and single-level comprehensions.
- Give a named constant to any number or string whose meaning is not obvious or that appears more than once. No mutable default arguments.
- A `# noqa` needs the rule code and a reason on the same line.

## Naming

| Kind | Convention | Example |
|---|---|---|
| Module, package | short `snake_case` | `user_service.py` |
| Class | `PascalCase` noun | `UserService` |
| Exception | `PascalCase` ending in `Error` | `NotFoundError` |
| Function, method | `snake_case` starting with a verb | `create_user` |
| Variable | descriptive `snake_case` noun | `retry_count`, not `rc` |
| Boolean | auxiliary verb prefix | `is_active`, `has_permission`, `can_edit`, `should_retry` |
| Collection | plural, or `x_by_y` for a mapping | `users`, `users_by_id` |
| Constant | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Private | leading underscore | `_parse_row` |

Names say what a thing is, not its type or history. Avoid `data`, `info`, `tmp`, `result2`, and abbreviations.

## Docstrings and comments

- Every class and function has a 1-2 line docstring that says what it does. Mention what it returns or raises when that is not obvious. Do not repeat the signature.
- `__init__` and other dunder methods are exempt, because the class docstring covers them.
- Comments explain why, never what. Delete commented-out code.
- Tests are exempt: the test name is the description.

## Type hints and Pydantic

- Type-hint every parameter, every return value including `-> None`, and every class attribute.
- Use built-in generics and unions: `list[str]`, `dict[str, int]`, `str | None`. Import abstract types from `collections.abc`.
- Avoid `Any`. Prefer a precise type, a `Protocol`, or a generic.
- Use a Pydantic model for anything that crosses a boundary: requests, responses, settings, files, external APIs, LLM output. Validate once at the edge with `Model.model_validate(...)`, then trust the types.
- Constrain fields with `Field(...)` and narrow types such as `EmailStr`, `Literal`, and `Enum` instead of hand-written checks.

## Named exports

- Each module in `app/` lists its public names in `__all__`, right after the imports. Private helpers start with an underscore. The module `logger` and route functions need neither.
- Import names explicitly with absolute imports: `from app.services.user_service import UserService`. Never `from module import *`.
- Keep `__init__.py` empty unless it re-exports a deliberate public API, which it also lists in `__all__`.
