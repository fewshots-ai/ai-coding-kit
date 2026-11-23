# Components and UI

Read when writing or changing anything the user sees.

## Component kinds

- **Screens** live in `app/`, map to a route, compose features, and hold no business logic.
- **Feature components** live in `features/<name>/components/` and may call that feature's hooks.
- **Shared UI components** live in `components/ui/`, are purely presentational, and never fetch data. They extend native elements: `type ButtonProps = ComponentProps<'button'> & { isBusy?: boolean }`.

## Every data-driven component shows four states

- **Loading**: an `<output>` or skeleton that keeps the layout stable.
- **Empty**: a sentence that says what will appear here and how to add it.
- **Error**: a `role="alert"` message with the user-safe error text and a retry action.
- **Success**: the content. Never flash the empty state while data is still loading.

## Accessibility

- Semantic HTML first: `button` for actions, `a` for navigation, `form` with a submit button, headings in order, `ul` for lists. Use `role` and `aria-*` only when no semantic element exists.
- Every input has a visible `label`, every image has `alt` text, every icon-only button has `aria-label`.
- Everything works with the keyboard alone. Dialogs trap focus and return it on close. A route change moves focus to the new heading.
- Do not convey state with color alone, and keep text contrast at WCAG AA.
- The `jsx-a11y` lint rules are fixed, not silenced.

## Forms

- Use a native `form` with `onSubmit` and `event.preventDefault()`. Disable the submit button while the request is pending and show the result next to the form.
- Validate with the same zod schema the API uses, and show each message next to its field.
- Use `react-hook-form` with its zod resolver when a form has more than a few fields or needs per-field validation.

## Styling

- CSS Modules by default: `todo-list.module.css` next to the component. Use Tailwind only when the prompt asks for it or the project already uses it.
- Design tokens are CSS variables on `:root` (colors, spacing, radius, font sizes). Components never hard-code a color.
- Mobile-first layouts with relative units. Test at a phone width and a desktop width.
- Inline `style` only for values computed at runtime.

## Performance

- Measure with the React DevTools profiler before optimizing. Do not add `useMemo`, `useCallback`, or `memo` on a hunch; the React Compiler makes most of them unnecessary.
- Split code by route with `lazy` and `Suspense`.
- Keys are stable ids, never the array index for lists that can reorder or change.
- Images declare `width`, `height`, and `loading="lazy"` below the fold. Lists beyond a few hundred rows are virtualized.
- Keep the production bundle lean: check `npm run build` output, and avoid importing a whole library for one function.
