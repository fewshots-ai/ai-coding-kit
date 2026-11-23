# Errors and logging

Read when handling failures or adding logging. Every fetch, parse, and user action can fail, so this applies to most code.

## Errors

- **Guard clauses first.** Check inputs and edge cases at the top of a function or component and return early. Keep the success path last and unindented.
- **Typed errors.** `ApiError` in `lib/api-client.ts` carries the HTTP status and a message that is safe to show users. Throw it for every failed response. Other failures stay plain `Error`s with a clear message.
- **Validate at the boundary.** Parse API responses, URL parameters, and storage with zod. A parse failure is a bug in the contract, so let it throw and surface it, do not patch around it.
- **Error boundaries.** Wrap the app root and each route in an error boundary with a fallback that explains what happened and offers a retry. Use the `react-error-boundary` package rather than writing a class component.
- **Async failures.** Query and mutation errors render in place through `isError` and `error`. Set `throwOnError` only when a screen cannot show anything without the data.
- **Never swallow.** No empty `catch`, no ignored promise. Either handle the failure, show it, or rethrow it.
- **Two audiences.** Users get a short, friendly, actionable message. The logger gets the error object, the status, and what the user was doing. Raw messages reach the UI only when they come from `ApiError`.
- **Fail fast at startup.** Missing configuration throws in `lib/env.ts` before the app renders.

## Logging

- One logger in `lib/logger.ts`, with `debug`, `info`, `warn`, and `error`. App code imports it and never calls `console` (the lint rule enforces this).
- The logger prints in development and stays quiet below `warn` in production. An application forwards `error` to its error tracking service.
- Log events with context objects, not string concatenation: `logger.error("Todo create failed", { status, title })`.
- Log each failure once, where it is handled. Never log tokens, passwords, or personal data.

## Pattern

```ts
import { z } from "zod";

import { env } from "@/lib/env";

/** An API response the server described as a failure. Its message is safe to show users. */
export class ApiError extends Error {
  readonly status: number;

  constructor(status: number, message: string) {
    super(message);
    this.name = "ApiError";
    this.status = status;
  }
}

const errorBodySchema = z.object({ error: z.object({ message: z.string() }) });

/** Call the API and return its JSON body validated against `schema`, or throw an ApiError. */
export async function fetchJson<T>(
  path: string,
  schema: z.ZodType<T>,
  init?: RequestInit,
): Promise<T> {
  const headers = new Headers(init?.headers);
  headers.set("content-type", "application/json");
  const response = await fetch(`${env.VITE_API_URL}${path}`, { ...init, headers });
  if (!response.ok) {
    const body = errorBodySchema.safeParse(await response.json().catch(() => null));
    throw new ApiError(
      response.status,
      body.success ? body.data.error.message : "The request failed.",
    );
  }
  return schema.parse(await response.json());
}
```
