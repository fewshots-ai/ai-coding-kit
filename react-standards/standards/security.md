# Security

Read when handling input, auth, secrets, or dependencies.

## Always, demos included

- **The bundle is public.** Every `VITE_` variable and every line of source ships to the browser. Secrets, API keys with write access, and credentials never go in the front end; a server holds them.
- **Untrusted input.** Treat API responses, URL parameters, storage, and user input as untrusted. Parse them with zod and limit lengths and ranges.
- **No untrusted HTML.** Never use `dangerouslySetInnerHTML` with content from users or APIs. If rich text is required, sanitize it with a maintained library first.
- **Safe links.** Reject `javascript:` and `data:` URLs from user input. External links opened in a new tab carry `rel="noopener noreferrer"`.
- **Safe errors.** Error messages shown to users contain no stack traces, internal URLs, or secrets.
- **Dependencies.** Add one only when it is needed and well maintained. Commit `package-lock.json` and run `npm audit` before each release.
- **Personal data.** Collect the minimum, keep it out of logs and URLs, and use fake data in tests and demos.
- **Lint findings** from the security and accessibility rules are fixed, not silenced.

## Before real users or the public internet

- **Authentication.** The server owns sessions. Tokens live in `HttpOnly` cookies set by the server, never in `localStorage`. The client only checks "am I signed in" and redirects.
- **Authorization** happens on the server for every request. Hiding a button is not a permission check.
- **Content security policy** and HTTPS are set by the host. Avoid inline scripts so a strict policy can be enabled.
- **Third-party scripts** are loaded only when needed, from known origins, with their purpose recorded in the README.
- **Error tracking** receives errors without personal data.
- **Rate limits and abuse** are handled by the server, not the client.
