# bearcli command reference

Use this reference for command-specific behavior. The installed `bearcli` also supports `bearcli help <subcommand>` and `bearcli help all`.

## Global conventions

- Read formats: `--format tsv|csv|json`; prefer JSON for machine parsing.
- Field selection: `--fields f1,f2` or `--fields all`.
- Mutating commands are silent on success and use exit code as the signal.
- Exit codes: `0` success, `1` business error, `64` usage error.
- JSON errors are shaped as `{"error":{"code":"...","message":"..."}}`.
- Tags can be input with or without `#`; nested tags use `/`; multi-word tags should be quoted.
- Timestamps are ISO 8601 UTC.
- `--content` reads stdin when omitted.
- Text flags unescape `\n`, `\t`, `\r`, and `\\`; stdin is not unescaped.
- Note lookup generally accepts either `<note-id>` or `--title <title>`.
- Encrypted note content is not accessible.

## Read commands

### `cat`

Print raw note content.

```bash
bearcli cat <id>
bearcli cat --title "Mars" --format json
bearcli cat <id> --offset 0 --limit 500
```

JSON output is `{"content":"..."}`.

### `show`

Show structured note fields. Default fields are `id,title,tags`.

Useful fields: `id,title,locked,tags,hash,length,created,modified,pins,location,todos,done,attachments,content`.

```bash
bearcli show <id> --format json --fields id,title,tags,hash,modified,attachments
bearcli show --title "Mars" --format json --fields all
bearcli show <id> --fields all,content
```

`content` is excluded from `all`; request `all,content` explicitly.

### `list`

List notes without a search query.

```bash
bearcli list --format json --fields id,title,tags,modified -n 20
bearcli list --tag work --sort modified:asc --fields id,title,modified --format json
bearcli list --location archive --count --format json
```

Options include `--sort pinned,modified|created|title`, `--limit`, `--offset`, `--location notes|trash|archive|all`, `--tag`, and `--count`.

### `search`

Search using Bear search syntax.

```bash
bearcli search "meeting notes" --format json --fields id,title,tags,matches
bearcli search "@today @todo" --format json
bearcli search --query "- [ ]" --fields id,title,matches --format json
bearcli search "@todo" --count --format json
```

Search syntax includes text terms, quoted phrases, `-negation`, `#tag`, `!#tag`, `#*/tag`, `@today`, `@yesterday`, `@last7days`, `@date(YYYY-MM-DD)`, `@ctoday`, `@created7days`, `@todo`, `@done`, `@task`, `@tagged`, `@untagged`, `@title`, `@pinned`, `@images`, `@files`, `@attachments`, `@code`, `@locked`, `@readonly`, `@empty`, `@untitled`, `@wikilinks`, `@backlinks`, and `@ocr`.

Use `--query` for queries that start with `-`.

### `search-in`

Find exact case-insensitive string occurrences within one note.

```bash
bearcli search-in <id> --string "TODO" --format json
bearcli search-in --title "Mars" --string "water" --context 200 --format json
bearcli search-in <id> --string "TODO" --count --format json
```

Default fields are `offset,snippet`.

## Write commands

### `edit`

Find exact text and replace it or insert around it.

```bash
bearcli edit <id> --find "TODO" --replace "DONE"
bearcli edit <id> --find "## Notes" --insert-after "\nNew line"
bearcli edit <id> --find "cat" --replace "dog" --all --word
```

Useful flags: `--all`, `--ignore-case`, `--word`, `--no-update-modified`, `--force`.

Attachment removals are rejected unless `--force` is supplied. Only use `--force` after confirming the removal is intentional.

### `overwrite`

Replace a note's entire content.

```bash
bearcli show <id> --format json --fields hash,attachments
printf '%s' "$content" | bearcli overwrite <id> --base <hash>
bearcli overwrite <id> --content "# Title\nBody"
```

Bear derives title from the first heading and tags from hashtags. Attachment markdown links must remain in the new content or attachments are removed. Pass `--base <hash>` from `show` for optimistic concurrency.

### `create`

Create a new note.

```bash
bearcli create "My Note" --content "Body text" --format json
printf '# Quick Capture\nSome thoughts' | bearcli create --tags inbox --format json
bearcli create "My Note" --tags "work,draft" --if-not-exists --format json
```

When a title is supplied, Bear auto-generates the heading. Prefer `--tags` for tags so Bear places them according to settings.

### `append`

Append or prepend content.

```bash
bearcli append <id> --content "New paragraph"
printf 'New content' | bearcli append <id>
bearcli append --title "Mars" --content "Update" --position beginning
```

Positions are `beginning` or `end`; default is `end`.

## Tags

```bash
bearcli tags list --format json
bearcli tags list <id> --format json
bearcli tags add <id> work "work/meetings"
bearcli tags remove --title "Mars" draft
bearcli tags rename old-tag new-tag
bearcli tags delete --name "work/old"
```

`tags rename` refuses to merge into an existing tag unless `--force` is used. `tags delete` removes the tag from all notes.

## Pins

```bash
bearcli pin list --format json
bearcli pin list <id> --format json
bearcli pin add <id> global
bearcli pin add <id> work projects
bearcli pin remove --title "Mars" global work
```

Pin targets are `global` or tag names. Tag-specific pin operations are atomic: if any tag does not exist, no pins are changed.

## State changes

```bash
bearcli trash <id>
bearcli archive --title "Finished Project"
bearcli restore <id>
bearcli open --title "Mars" --header "Moons" --edit
```

`trash` is a soft delete. `archive` hides from active notes. `restore` moves archived or trashed notes back to the active notes list. `open` brings Bear to the foreground.

## Attachments

```bash
bearcli attachments list <id> --format json
bearcli attachments add <id> --filename photo.jpg < photo.jpg
bearcli attachments delete <id> --filename photo.jpg
bearcli attachments save <id> --filename photo.jpg > photo.jpg
bearcli attachments save <id> --filename photo.jpg --format json
```

Attachment `save` writes raw bytes for default TSV and refuses raw output on a TTY. Use JSON or CSV to receive base64 in structured output.

## MCP server

`bearcli mcp-server` exposes the same operation set over stdio JSON-RPC. Prefer direct CLI commands in Codex unless the user asks to configure an MCP-aware client.
