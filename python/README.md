# Python Standards for AI Coding Agents

A small, modular instruction set that makes Cursor, Claude Code, Codex, and other LLM tools build Python and FastAPI projects the same way, from idea to operation.

## How it works

- [AGENTS.md](AGENTS.md) is the entry point. It holds the role, the core rules, a lifecycle map, and the definition of done. Every tool loads it on every task.
- [standards/](standards/) holds one short module per topic. An agent reads a module only when the lifecycle map says the task needs it, so the context stays small.
- [CLAUDE.md](CLAUDE.md) is a one-line import of `AGENTS.md` for Claude Code.

## Use in a project

```bash
cp -R AGENTS.md CLAUDE.md standards /path/to/project/
```

The kit is generic and holds no application details. Give the application prompt and details separately, in chat or in a file of your own.

| Tool | How it loads the standards |
|---|---|
| Codex | Reads `AGENTS.md` automatically. |
| Cursor | Reads `AGENTS.md` automatically. |
| Claude Code | Reads `CLAUDE.md`, which imports `AGENTS.md`. Versions from 2.1.277 can also read `AGENTS.md` directly. |
| Other LLMs | Paste `AGENTS.md` and the modules you need into the system prompt. |

## Extend

1. Add `standards/<topic>.md` using the template below.
2. Add one row to the lifecycle map in `AGENTS.md`. That is the only registration step.
3. Keep a module near 40 lines of rules, and split it when it grows.
4. Write one rule per bullet, concrete enough to check. Prefer rules a tool can enforce.
5. Add a rule to "Core rules" only if it must hold on every task.
6. Keep application-specific details out of the kit. They belong in the application's own prompt and documents.

```markdown
# <Topic>

Read when <the situation in which an agent needs this module>.

- **<Rule name>.** <One concrete, checkable instruction.>
```

To load a module in every Claude Code session, add an import line for it to `CLAUDE.md`, in the same form as the `AGENTS.md` import.

## Verified against

The setup commands, the tool settings, and the code patterns were run end to end on 2026-10-03 with uv 0.10.9, Python 3.14, FastAPI 0.142, Pydantic 2.13, Ruff 0.16, and pytest 9.1. A fresh agent then built a small service from the kit, and its findings were folded back in.
