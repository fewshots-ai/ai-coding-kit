# Security

Read when handling input, auth, or secrets, or auditing dependencies.

## Always, demos included

- **Secrets.** Read them from environment variables through configuration properties. Never hard-code, log, or commit them. `application.properties` holds defaults only, and `.env` files stay git-ignored.
- **Untrusted input.** Treat request bodies, path and query parameters, headers, files, and third-party responses as untrusted. Validate with Jakarta Validation at the controller and limit sizes and ranges.
- **Injection.** Use parameterized queries and Spring Data methods. Never build SQL, shell commands, or file paths by joining strings. No `Runtime.exec` with user input, no Java deserialization of untrusted bytes.
- **Safe errors.** Problem details never contain stack traces, SQL, file paths, or secrets. Keep `server.error.include-stacktrace=never`.
- **Timeouts.** Set connect and read timeouts on every HTTP client and database pool.
- **Dependencies.** Add one only when it is needed and well maintained. Let the Boot parent manage versions. Run the OWASP dependency check before each release of an application.
- **Personal data.** Collect the minimum, keep it out of logs, and use fake data in tests and demos.
- **Lint findings** from Error Prone are fixed, not suppressed.

## Before real users or the public internet

- **Authentication and authorization.** Use Spring Security. Authenticate at the edge and authorize every request on the server against the specific resource, with method security where it reads best. Deny by default.
- **Passwords and tokens.** Hash passwords with the Spring Security encoder. Keep tokens short-lived. Use `SecureRandom` for anything security-related.
- **Transport.** Terminate HTTPS at the edge. Restrict CORS to known origins and never combine `*` with credentials.
- **Limits.** Cap upload and body sizes, and rate-limit public or costly endpoints.
- **Least privilege.** Database users, API keys, and containers get the minimum access they need. Containers run as a non-root user.
- **Actuator.** Expose only `health` and `info` without authentication. Everything else sits behind authentication.
