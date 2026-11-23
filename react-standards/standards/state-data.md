# State and data

Read when fetching data, managing state, or handling forms.

## Put state in the right place

| Kind of state | Where it lives | Tool |
|---|---|---|
| Local UI (open, selected, typed) | The component that owns it | `useState`, `useReducer` for several related values |
| Shareable UI (filters, page, selected id) | The URL | React Router search and path params |
| Server data (anything fetched) | The query cache | TanStack Query |
| Shared client state (session, theme, cart) | Context for values that rarely change; a Zustand store when many components update it often | `createContext`, `zustand` |
| Form state | The form | Native form, or `react-hook-form` with zod |

- Start local and lift state only when a second component needs it. Never put server data in a store.
- Derive values in render instead of storing them: `const isEmpty = todos.length === 0`.
- Never mutate state. Create new objects and arrays with spread or `toSorted`, `toSpliced`, and friends.

## Server data

- One function per endpoint in `api/`, returning data parsed with a zod schema through `fetchJson(path, schema)`. Components never call `fetch` directly.
- One hook per query or mutation in `hooks/`: `useTodos`, `useCreateTodo`. Query keys are arrays, declared once and reused for invalidation.
- A mutation invalidates the queries it affects in `onSuccess`. Use optimistic updates only when the prompt asks for instant feedback.
- Components read `isPending`, `isError`, and `data` from the hook and render the four states. Set `retry: false` in tests.
- Never fetch inside `useEffect`.

## Effects

- An effect synchronizes with something outside React: a subscription, a timer, the DOM, a browser API. Return a cleanup function.
- Do not use an effect to derive state from props or state, to fetch data, or to respond to a user event. Compute in render, use a query hook, or handle it in the event handler.
- List every dependency. If the dependency list feels wrong, move the logic rather than silencing the rule.

## Pattern

```ts
import { z } from "zod";

import { fetchJson } from "@/lib/api-client";

export const todoSchema = z.object({
  id: z.number().int(),
  title: z.string(),
  isDone: z.boolean(),
});

export type Todo = z.infer<typeof todoSchema>;

/** Load every todo. */
export function listTodos(): Promise<Todo[]> {
  return fetchJson("/api/v1/todos", z.array(todoSchema));
}
```

```ts
import { useMutation, useQuery, useQueryClient } from "@tanstack/react-query";

import { createTodo, listTodos } from "@/features/todos/api/todos";

export const todosQueryKey = ["todos"] as const;

/** Server state for the todo list, cached and refetched by TanStack Query. */
export function useTodos() {
  return useQuery({ queryKey: todosQueryKey, queryFn: listTodos });
}

/** Create a todo and refresh the list when the server confirms it. */
export function useCreateTodo() {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: createTodo,
    onSuccess: () => queryClient.invalidateQueries({ queryKey: todosQueryKey }),
  });
}
```
