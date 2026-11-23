# Code style

Read when writing or changing components, hooks, or TypeScript. Oxlint enforces much of this: fix findings, do not silence them.

## TypeScript

- Strict mode is on. Prefer `unknown` to `any`, and narrow with a type guard or a zod schema instead of a type assertion.
- Use `type` aliases for props, unions, and data shapes. Use `interface` only when declaration merging is needed.
- Derive types from zod schemas with `z.infer`, so the runtime check and the type never drift apart.
- Use `import type` for types. Never use enums; use string literal unions.
- Give a named constant to any number or string whose meaning is not obvious or that appears more than once.
- A disable comment needs the rule name and a reason on the same line.

## Components and hooks

- Write components as `function` declarations with a named export: `export function TodoList(props: TodoListProps)`.
- Type props as a `type` next to the component. Destructure props in the signature and put defaults there.
- Keep a component under about 150 lines with shallow JSX. Extract a subcomponent for repeated markup and a hook for reusable logic.
- Return early for loading, error, and empty states. Keep the success markup last and unindented.
- A hook starts with `use`, returns a plain object or tuple, and holds no JSX.
- Never define a component inside another component, and never call a component as a plain function.
- Inline arrow functions are fine for handlers. Move a handler out when it is longer than three lines or used twice.

## Naming

| Kind | Convention | Example |
|---|---|---|
| File, folder | `kebab-case` | `todo-list.tsx`, `use-todos.ts` |
| Component | `PascalCase` noun | `TodoList` |
| Hook | `use` plus what it gives you | `useTodos`, `useDebouncedValue` |
| Event handler | `handle` plus the event | `handleSubmit` |
| Callback prop | `on` plus the event | `onSubmit` |
| Boolean | auxiliary verb prefix | `isOpen`, `hasError`, `canSubmit`, `shouldRetry` |
| Type | `PascalCase`, no `I` or `T` prefix | `Todo`, `TodoListProps` |
| Constant | `UPPER_SNAKE_CASE` | `MAX_TITLE_LENGTH` |
| Collection | plural, or `xById` for a map | `todos`, `todosById` |

Names say what a thing is, not its type or history. Avoid `data`, `info`, `temp`, `item2`, and abbreviations.

## Documentation

- Every component, hook, and exported function has a 1-2 line JSDoc comment that says what it does and, when not obvious, what it returns or throws.
- Comments explain why, never what. Delete commented-out code.
- Tests are exempt: the test name is the description.

## Imports and exports

- Named exports only. A default export is allowed only where a tool requires it, such as a config file or a `React.lazy` route.
- No barrel files (`index.ts` that re-exports a folder). Import from the file that defines the name.
- Absolute imports through `@/` for anything outside the current folder. Relative imports only for siblings.
- Order imports in three groups separated by a blank line: Node and packages, then `@/`, then relative files and styles.
