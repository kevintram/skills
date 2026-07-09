---
name: bearcli
description: Work with local Bear notes through the `bearcli` command-line tool. Use when the user asks Codex to find, list, read, create, update, append to, tag, pin, archive, trash, restore, open, or manage attachments for Bear notes; when a task mentions Bear.app, Bear notes, note IDs, Bear tags, Bear search syntax, or `bearcli`; or when safe local note edits need optimistic concurrency and attachment-preservation checks.
---

# Bear CLI

Use `bearcli` for local Bear note workflows. Prefer structured JSON for reads, preserve note content carefully for writes, and treat mutating commands as silent-on-success operations where the exit code is the result.

## Quick Start

- Check availability with `command -v bearcli`; if missing, tell the user `bearcli` is not installed.
- For reads, use `--format json` and narrow output with `--fields`.
- For large result sets, add `-n` and select only needed fields before fetching note content.
- For exact note lookup, prefer note ID when available. Use `--title` only when the title is user-supplied and likely unique.
- For write operations, read the current note first and preserve headings, tags, attachment references, and relevant metadata.
- Mutating commands such as `edit`, `overwrite`, `append`, `tags add/remove`, `pin add/remove`, `trash`, `archive`, `restore`, `open`, and attachment writes produce no stdout on success.

Read `references/command-reference.md` when you need command-specific flags, fields, search syntax, attachment behavior, or examples beyond the quick workflow below.

## Read Workflow

1. Discover candidate notes with `bearcli search` or `bearcli list`.
2. Request JSON and minimal fields:

```bash
bearcli search "project notes" --format json --fields id,title,tags,modified,matches -n 20
bearcli list --tag work --format json --fields id,title,tags,modified -n 20
```

3. Fetch metadata with `show` and raw content with `cat`:

```bash
bearcli show <id> --format json --fields id,title,tags,hash,modified,attachments
bearcli cat <id> --format json
```

4. Use `search-in` for exact strings inside one note before editing:

```bash
bearcli search-in <id> --string "TODO" --format json
```

## Write Workflow

Use the least destructive command that satisfies the task:

- Use `append` for adding new content at the beginning or end.
- Use `edit` for exact replacements or insertions around known text.
- Use `tags add/remove` for tag-only changes.
- Use `overwrite` only when replacing or restructuring the whole note.

Before `overwrite`, read `hash` with `show` and pass it as `--base` so the command rejects if the note changed since the read:

```bash
bearcli show <id> --format json --fields id,title,hash,attachments
printf '%s' "$new_content" | bearcli overwrite <id> --base <hash>
```

When overwriting, include the first heading, desired hashtags, and existing attachment markdown links unless the user explicitly wants them removed. If `bearcli` rejects an edit because attachments would be removed, report the filenames and only retry with `--force` after explicit user confirmation.

## Creating Notes

Prefer stdin for multiline content:

```bash
printf '# Title\n\nBody text\n' | bearcli create --tags "work,draft" --format json --fields id,title,tags,hash
```

If a note should not be duplicated, pass `--if-not-exists` with a title:

```bash
bearcli create "Meeting Notes" --content "Agenda" --if-not-exists --format json
```

## Search And Tags

Bear search syntax is inline, not flag-based. Common patterns:

- `@today @todo` for today's incomplete tasks.
- `#work meeting` for notes tagged `work` that mention meeting.
- `!#work` for exact tag match without nested children.
- `#*/projects` for subtags only.
- `--query "- [ ]"` when the query begins with a hyphen.

Tags can be supplied with or without `#`; nested tags use slashes, and multi-word tags must be quoted.

## Safety Rules

- Do not run destructive or broad commands such as `trash`, `tags delete`, `tags rename --force`, attachment deletion, or whole-note overwrite unless the user asked for that operation or confirmed it.
- Prefer a read-before-write flow for every note edit.
- Never parse default TSV when JSON is available.
- Treat encrypted note content as inaccessible through the CLI.
- If a command returns no output, inspect the exit code and stderr rather than assuming failure.
