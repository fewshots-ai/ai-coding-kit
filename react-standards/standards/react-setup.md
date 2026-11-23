# React, Vite, and npm setup

Read when creating a project, or changing dependencies or tooling.

## Rules

- **Latest LTS Node.** Check `node --version`. If it is older than the current LTS, ask the user to update Node. Record the major version in `.nvmrc`.
- **npm is the package manager.** Commit `package-lock.json`. Use `npm ci` wherever the lockfile must win, such as CI. Never run `npm install -g`.
- **Vite with the `react-ts` template.** Use the `react-compiler-ts` template when the prompt asks for the React Compiler.
- **Strict TypeScript** in `tsconfig.app.json`, with the `@/*` path alias. The build runs `tsc -b` first, so a type error fails the build.
- **Oxlint and oxfmt** are the linter and formatter, with type-aware linting on. Do not add ESLint or Prettier unless the project already uses them.
- **Vitest, jsdom, and Testing Library** for component tests. Playwright for the end-to-end flows of an application.
- **Run everything through npm scripts** so every tool uses the project's own versions.
- **Never commit** `node_modules/`, `dist/`, `coverage/`, or `.env*.local` files. Add `coverage` to `.gitignore`, because the template does not.

## Command map

| Task | Command |
|---|---|
| Create a project | `npm create vite@latest my-app -- --template react-ts --no-interactive` |
| Install exactly what is locked | `npm ci` |
| Add or remove a runtime dependency | `npm install <package>`, `npm uninstall <package>` |
| Add a dev-only dependency | `npm install -D <package>` |
| See and apply upgrades | `npm outdated`, then `npm update` within ranges or `npm install <package>@latest` |
| Run a one-off tool without installing it | `npx <tool>` |
| Audit dependencies | `npm audit` |
| Bump the version | `npm version patch`, `minor`, or `major` |
| Dev server, build, preview the build | `npm run dev`, `npm run build`, `npm run preview` |

## New project

Run this in the project root, the folder that holds `AGENTS.md`. Never pass `--overwrite` to create-vite: it deletes every existing file, including these standards.

```bash
npm create vite@latest my-app -- --template react-ts --no-interactive
cp -R my-app/. . && rm -rf my-app
npm install
npm install @tanstack/react-query react-router zod
npm install -D vitest@latest @vitest/coverage-v8@latest jsdom @testing-library/react @testing-library/jest-dom @testing-library/user-event oxfmt oxlint-tsgolint
npx oxfmt --init
```

Then delete the template's `App.tsx`, `App.css`, and `assets/`, set the project name in `package.json`, and add the settings below.
Install `vitest` and its coverage package with explicit `@latest` tags, as above; without them npm can pair mismatched versions and fail.

## Settings

`package.json` scripts:

```json
{
  "dev": "vite",
  "build": "tsc -b && vite build",
  "preview": "vite preview",
  "typecheck": "tsc -b",
  "lint": "oxlint --fix",
  "format": "oxfmt",
  "test": "vitest run",
  "test:watch": "vitest",
  "test:coverage": "vitest run --coverage",
  "check": "tsc -b && oxlint && oxfmt --check && vitest run && vite build"
}
```

`tsconfig.app.json`, inside `compilerOptions`:

```json
{
  "strict": true,
  "paths": { "@/*": ["./src/*"] }
}
```

`vite.config.ts`:

```ts
/// <reference types="vitest/config" />
import { fileURLToPath } from "node:url";

import react from "@vitejs/plugin-react";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: { "@": fileURLToPath(new URL("./src", import.meta.url)) },
  },
  test: {
    environment: "jsdom",
    setupFiles: ["./src/test/setup.ts"],
    css: false,
    coverage: { include: ["src/**"], exclude: ["src/test/**", "src/main.tsx"] },
  },
});
```

`.oxlintrc.json`:

```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "plugins": ["react", "typescript", "oxc", "unicorn", "import", "jsx-a11y", "vitest"],
  "options": { "typeAware": true },
  "categories": { "correctness": "error", "suspicious": "warn", "perf": "warn" },
  "rules": {
    "react/rules-of-hooks": "error",
    "react/exhaustive-deps": "error",
    "react/only-export-components": ["warn", { "allowConstantExport": true }],
    "react/react-in-jsx-scope": "off",
    "typescript/no-explicit-any": "error",
    "typescript/no-floating-promises": "error",
    "typescript/consistent-type-imports": "error",
    "import/no-cycle": "error",
    "import/no-unassigned-import": ["error", { "allow": ["**/*.css", "@testing-library/jest-dom/vitest"] }],
    "no-console": "error"
  },
  "overrides": [{ "files": ["src/test/**", "**/*.test.{ts,tsx}"], "rules": { "no-console": "off" } }],
  "ignorePatterns": ["dist/**", "coverage/**"]
}
```

`.oxfmtrc.json`:

```json
{
  "$schema": "./node_modules/oxfmt/configuration_schema.json",
  "ignorePatterns": ["dist/**", "coverage/**", "**/*.md"]
}
```

Markdown is excluded so that `npm run format` never rewrites these standards.
