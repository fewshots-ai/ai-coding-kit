# Security

Read when handling input, auth, or secrets, or auditing dependencies.

## Always, demos included

- **Secrets.** Read them from environment variables into the config struct. Never hard-code, log, or commit them. `.env` stays git-ignored, and `.env.example` lists every setting with a placeholder value.
- **Untrusted input.** Treat request bodies, path and query parameters, headers, files, and third-party responses as untrusted. Parse them into validated domain types at the boundary and limit lengths and ranges.
- **No `unsafe`.** The crate forbids it. If a dependency needs it, that is a reason to review the dependency, not to allow it.
- **Injection.** Use parameterized queries. Never build SQL, shell commands, or file paths by joining strings. No `std::process::Command` with user input.
- **Safe errors.** Error responses never contain stack traces, SQL, file paths, or secrets. Only the `Display` text of an `AppError` reaches the user.
- **Timeouts.** Set a timeout on every outbound call and on the server as a whole.
- **Dependencies.** Add one only when it is needed and well maintained. Keep `Cargo.lock` committed. An application audits before each release with `cargo audit` or `cargo deny check`, installed per machine.
- **Personal data.** Collect the minimum, keep it out of logs, and use fake data in tests and demos.
- **Lint findings** are fixed, not allowed away.

## Before real users or the public internet

- **Authentication and authorization.** Authenticate in a middleware or extractor. Authorize every request on the server against the specific resource. Deny by default.
- **Passwords and tokens.** Use a maintained hashing crate such as `argon2`. Keep tokens short-lived. Use the `rand` crate's secure generator for anything security-related.
- **Transport.** Terminate HTTPS at the edge. Restrict CORS to known origins with `tower_http::cors` and never combine `*` with credentials.
- **Limits.** Cap body sizes with `RequestBodyLimitLayer`, and rate-limit public or costly endpoints.
- **Least privilege.** Database users, API keys, and containers get the minimum access they need. Containers run as a non-root user.
