---
name: clings
description: Manage Things 3 tasks via the clings CLI — list, capture, search, filter, bulk-edit, review, and automate. If a needed feature is missing, extend the CLI using the local Swift package.
---

# Clings

Clings is a command-line interface for Things 3 on macOS. Use it to manage tasks without leaving the terminal.

## First Checks

Before running commands:

```bash
command -v clings
clings doctor
```

If Things 3, automation permissions, or auth-token setup are missing, report the specific blocker and the fix. For command syntax, use built-in help:

```bash
clings --help
clings <command> --help
```

## References

- **cli-workflows.md** — How to use clings: listing, capture, search, filters, mutations, bulk ops, reviews. Start here.
- **development.md** — How to modify clings: repo structure, build, test, release. Read only when extending the CLI.

## Safety Rules

- Use `--json` for automation and parsing.
- Use `--dry-run` before every `clings bulk ...` mutation.
- Get specific IDs from `clings ... --json` or `clings show <id>` before changing tasks.
- Do not use `--yes`, `--force`, `delete`, or broad bulk filters unless the user explicitly approves.
- Never write directly to the Things 3 SQLite database — clings reads via SQLite but writes through Things automation APIs.
- Unless otherwise told, tag all created projects/tasks with `🤖 Codex` to give transparency that they were created by an agent.