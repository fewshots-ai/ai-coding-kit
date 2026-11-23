# Security

Read when handling input, auth, or secrets, or auditing dependencies.

## Always, demos included

- **Secrets.** Load them from the environment through settings, typed as `SecretStr`. Never hard-code, log, or commit them. `.env` stays git-ignored, and `.env.example` lists every setting with a placeholder value. If a secret leaks, say so and rotate it.
- **Untrusted input.** Treat request data, files, environment values, third-party responses, and LLM output as untrusted. Validate with Pydantic at the edge and limit sizes and ranges.
- **Injection.** Use parameterized queries. Never build SQL, shell commands, or file paths by joining strings. No `eval`, `exec`, `shell=True`, or `pickle` on untrusted data.
- **Output.** Responses never contain stack traces, SQL, file paths, or secrets. Return only the fields in the response model.
- **Timeouts.** Set a timeout on every outbound call.
- **Dependencies.** Add one only when it is needed and well maintained. Keep `uv.lock` committed.
- **Personal data.** Collect the minimum, keep it out of logs, and use fake data in tests and demos.
- **Ruff `S` findings** are fixed, not silenced. A justified exception carries a `# noqa` with the code and the reason.

## Before real users or the public internet

- **Authentication and authorization.** Authenticate in a FastAPI dependency. Authorize every request on the server against the specific resource. Deny by default.
- **Cryptography.** Use vetted libraries for password hashing and tokens. Use `secrets`, not `random`, for anything security-related. Never invent your own scheme.
- **Transport.** Use HTTPS. Restrict CORS to known origins and never combine `*` with credentials.
- **Limits.** Cap upload and body sizes, and rate-limit public or costly endpoints.
- **Least privilege.** Give API keys, database users, and containers the minimum access they need. Containers run as a non-root user.
- **Audit.** Before each release, run `uv run --with pip-audit pip-audit`, or `uv audit` if your uv version has it.
