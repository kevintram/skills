# Clings CLI Workflows

Reference for using clings to manage Things 3 tasks. Covers installation, capture, listing, search, filters, mutations, bulk operations, reviews, and shell completions.

## Environment

Install or upgrade:

```bash
brew install dan-hart/tap/clings
brew update && brew upgrade clings
```

Run diagnostics:

```bash
clings doctor
clings doctor --verbose
```

Set the Things URL-scheme auth token only when needed for features such as `--when` or `--heading`:

```bash
clings config set-auth-token <token>
```

The token comes from Things 3: Settings > General > Enable Things URLs > Copy auth token.

## Aliases

| Command | Aliases |
|---------|---------|
| `today` | `t` |
| `inbox` | `i` |
| `upcoming` | `u` |
| `someday` | `s` |
| `logbook` | `l` |
| `search` | `find`, `f` |
| `complete` | `done` |
| `delete` | `rm` |

## Capture

Natural-language capture supports dates, times, tags, projects, areas, deadlines, priority, notes, and checklist items:

```bash
clings add "draft changelog entry tomorrow #docs"
clings add "replace air filter friday 3pm #home !high"
clings add "review PR // needs careful testing - check auth - verify tests"
```

Preview parsing without creating:

```bash
clings add "Test task tomorrow #docs" --parse-only --json
```

Use explicit flags when ambiguity matters:

```bash
clings add "Task title" --when tomorrow --deadline "2026-06-30" --tags docs urgent --project "Documentation" --area "Writing" --notes "Additional context"
```

## Inspecting Tasks

Use these read-only commands before planning mutations:

```bash
clings today --json
clings inbox --json
clings upcoming --json
clings anytime --json
clings someday --json
clings logbook --json
clings projects --json
clings areas --json
clings tags list --json
clings show <id> --json
```

Use custom list formatting for human summaries:

```bash
clings today --format "{status} {name} [{project}] {tags}"
```

## Search and Filters

Use `search` for free text:

```bash
clings search "meeting" --json
```

Use `filter` for structured queries:

```bash
clings filter "status = open"
clings filter "due < today AND status = open"
clings filter "tags CONTAINS 'urgent'"
clings filter "name LIKE '%report%'"
clings filter "project IS NOT NULL"
```

**Operators:** `=`, `!=`, `<`, `>`, `<=`, `>=`, `LIKE`, `CONTAINS`, `IS NULL`, `IS NOT NULL`, `IN`

**Fields:** `status`, `due`, `tags`, `project`, `area`, `name`, `notes`, `created`

## Mutations

Prefer ID-based changes after inspecting with JSON:

```bash
clings show <id> --json
clings update <id> --name "New title"
clings update <id> --notes "Updated notes"
clings update <id> --due 2026-06-30
clings update <id> --tags work urgent
clings complete <id>
clings cancel <id>
```

Use title completion only when the title is specific enough or the user asked for it:

```bash
clings complete --title "buy milk"
```

Use `pick` when an interactive choice is preferable:

```bash
clings pick show release
clings pick complete docs
clings pick cancel follow-up
clings pick delete cleanup
```

Deletion moves to trash. Do not use `clings delete <id> --force` unless the user explicitly requested deletion without confirmation.

Undo recent supported writes:

```bash
clings undo --show
clings undo
```

## Bulk Operations

Always preview first, then repeat exactly without `--dry-run` only after the user approves or the request clearly authorizes the mutation:

```bash
clings bulk complete --where "tags CONTAINS 'done'" --dry-run
clings bulk cancel --where "project = 'Archive Prep'" --dry-run
clings bulk tag "urgent,priority" --where "tags CONTAINS 'docs'" --dry-run
clings bulk move --where "tags CONTAINS 'draft'" --to "Archive" --dry-run
```

Narrow the list with `--list today`, `--list inbox`, etc. when possible.

## Saved Views

Saved views store reusable filters:

```bash
clings views save docs-today "tags CONTAINS 'docs' AND due <= today" --note "Documentation queue"
clings views list
clings views run docs-today --json
clings views delete docs-today
```

## Templates

Templates store reusable task shapes:

```bash
clings template save weekly-review "Weekly review" --when "tomorrow morning" --checklist "Process inbox" "Review deadlines"
clings template list
clings template run weekly-review
clings template delete weekly-review
clings add "Weekly review prep" --template weekly-review
```

## Review, Focus, and Reporting

```bash
clings focus
clings focus --limit 5
clings review
clings review status
clings review clear
clings project audit
clings project audit --json
clings stats
clings stats --days 7
clings stats trends
clings stats heatmap
```

## Shell Completions

```bash
clings completions bash > ~/.bash_completion.d/clings
clings completions zsh > ~/.zfunc/_clings
clings completions fish > ~/.config/fish/completions/clings.fish
```

## Troubleshooting

**`clings doctor` fails:**
- "Things 3 not found" — Install Things 3 from the Mac App Store.
- "Automation permission denied" — Grant Terminal (or your shell) access in System Settings > Privacy & Security > Automation > Things 3.
- "Database not accessible" — Things 3 must have run at least once to create its database.

**Auth token rejected:**
- Ensure the token is copied exactly from Things 3: Settings > General > Enable Things URLs.
- Re-run `clings config set-auth-token <token>` with the fresh token.

**`clings open` not working:**
This command may be disabled in some builds. Check `clings open --help` to confirm availability before using.
