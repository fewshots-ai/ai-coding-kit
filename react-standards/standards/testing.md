# Testing

Read when writing tests or judging whether work is done.

- **Vitest, jsdom, and Testing Library.** Tests sit next to the code as `name.test.tsx`. Run them with `npm run test`.
- **Test behavior through the DOM.** Query by role, label, and text, the way a user finds things: `getByRole("button", { name: "Add" })`. Use `data-testid` only when no accessible query exists, and treat that as a hint that the markup needs a label.
- **Never test implementation.** No assertions on state, props, or internal functions. Hooks are tested through a component that uses them. Pure functions are tested directly.
- **Render through `renderWithProviders`** in `src/test/render.tsx`, which gives every test a fresh query client with `retry: false` and a memory router.
- **Interact with `userEvent`.** Call `userEvent.setup()`, `await` every interaction, and wait with `findBy` or `waitFor`. No sleeps.
- **Arrange, act, assert**, separated by blank lines. One behavior per test, named for it: `it("shows the server message when loading fails")`.
- **Mock the network at the boundary.** Mock `fetch` with `vi.spyOn(globalThis, "fetch")` or use MSW in an application. Never call a real API from a test.
- **Cover the states.** For each data-driven component, test loading, empty, error, and success. An application also tests edge cases and boundary values.
- **Trace to requirements.** Every acceptance criterion has a test. Every bug fix starts with a failing test that reproduces the bug.
- **End to end.** An application runs its main flows in Playwright against the built app, in a real browser.
- **Coverage is a signal.** Check it with `npm run test:coverage`. Cover features and `lib/` fully before chasing a number.
- **Warnings are work.** Fix `act` warnings and deprecation warnings instead of silencing them.
- **Never weaken a test to get green.** Fix the code, or explain why the test was wrong.

## Setup

`src/test/setup.ts`:

```ts
import "@testing-library/jest-dom/vitest";
import { cleanup } from "@testing-library/react";
import { afterEach } from "vitest";

afterEach(() => {
  cleanup();
});
```

`src/test/render.tsx`:

```tsx
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { render } from "@testing-library/react";
import type { ReactElement } from "react";
import { MemoryRouter } from "react-router";

/** Render a component inside fresh providers, so every test starts with an empty cache. */
export function renderWithProviders(ui: ReactElement, { route = "/" } = {}) {
  const queryClient = new QueryClient({ defaultOptions: { queries: { retry: false } } });
  return render(
    <QueryClientProvider client={queryClient}>
      <MemoryRouter initialEntries={[route]}>{ui}</MemoryRouter>
    </QueryClientProvider>,
  );
}
```
