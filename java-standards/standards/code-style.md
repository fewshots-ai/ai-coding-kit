# Code style

Read when writing or changing Java code. Error Prone and the compiler enforce much of this: fix findings, do not suppress them.

## Modern and explicit

- Use records for data and DTOs, sealed interfaces for closed type sets, enums for fixed value sets, and switch expressions with pattern matching instead of `instanceof` chains.
- Make everything `final` that is not reassigned: fields, local variables where it aids reading, and classes not designed for extension.
- Use `var` only when the type is obvious from the right-hand side. Spell out the type for method call results.
- Return `Optional` for a value that may be absent, an empty collection instead of `null`, and never accept `Optional` as a parameter.
- Prefer streams for short, linear pipelines and loops for anything with state or early exit. Never nest streams.
- Keep methods small and single-purpose: about 30 lines and at most 2 levels of nesting. More than 4 parameters is a sign to group them in a record.
- Give a named constant to any number or string whose meaning is not obvious or that appears more than once.
- A `@SuppressWarnings` needs the narrowest scope and a comment with the reason.

## Naming

| Kind | Convention | Example |
|---|---|---|
| Package | lower case, by feature | `com.example.app.user` |
| Class, record, interface | `PascalCase` noun, no `I` prefix | `UserService`, `UserCreate` |
| Exception | `PascalCase` ending in `Exception` | `NotFoundException` |
| Method | `camelCase` starting with a verb | `createUser` |
| Variable, parameter | descriptive `camelCase` noun | `retryCount`, not `rc` |
| Boolean | auxiliary verb prefix | `isActive`, `hasPermission`, `canEdit`, `shouldRetry` |
| Collection | plural, or `xById` for a map | `users`, `usersById` |
| Constant | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Test method | behavior, not method name | `createUserRejectsDuplicateEmail` |

Names say what a thing is, not its type or history. Avoid `data`, `info`, `tmp`, `result2`, and abbreviations.

## Javadoc and comments

- Every class and public method has a 1-2 line Javadoc comment that says what it does. Mention what it returns or throws when that is not obvious. Do not repeat the signature with `@param` tags.
- Comments explain why, never what. Delete commented-out code.
- Tests are exempt: the test name is the description.

## Imports and visibility

- No wildcard imports. Static imports only for test assertions and matchers.
- Package-private is the default. Make a type `public` only when another package needs it.
- No public mutable fields and no static mutable state. Inject collaborators through the constructor.
