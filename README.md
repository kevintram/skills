# Personal Agent Skills

The canonical source for the [agent skills](https://skills.sh) I own and edit directly, plus a manifest of the public skills I install from upstream.

A *skill* is a folder with a `SKILL.md` file that teaches a coding agent how to do one thing well — from committing changes to driving a CLI. This repo keeps mine in one place so I can install the same set across every agent I use (OpenCode, Claude Code, Codex, Cursor).

## How this repo is organized

- **`my-skills/`** — my own editable skills, one folder per skill. This is the source of truth; edit these directly. See each `SKILL.md` for what it does.
- **`vendor-skills.yaml`** — a human-maintained manifest (documentation, *not* an executable lockfile) describing the full intended skill set: which agents I target, which public skills I pull from upstream, and where the managed skills resolve on disk.

Public skills that I use as-is are **not** copied into this repo. They're installed from their upstream repositories and tracked in `vendor-skills.yaml` with their `skills.sh` review links, so upstream stays the source of truth.

## Install

Run these from the repo root. They install globally (`-g`) into every agent I target.

**My personal skills (my-skills):**

```bash
npx skills add . -g -a opencode -a claude-code -a codex -a cursor --skill '*' -y
```

**Public skills from upstream (vendor-skills.yaml):**

Install one command per vendor, following this template (source, then one `--skill` flag per skill you want from it):

```bash
npx skills add <source> -g -a opencode -a claude-code -a codex -a cursor --skill <skill-name> --skill <skill-name> -y
```

For example:

```bash
npx skills add kepano/obsidian-skills -g -a opencode -a claude-code -a codex -a cursor --skill defuddle --skill json-canvas -y
```

See `vendor-skills.yaml` for the full list of vendors and skills to install.

**Verify what's installed:**

```bash
npx skills list --global
```

## How resolution works

The `skills` CLI installs the shared set into `~/.agents/skills` and symlinks each agent's entries from there. Native agent directories (`~/.claude/skills`, `~/.codex/skills`, etc.) also symlink back to `~/.agents/skills`, so every CLI resolves the same managed set. The full list of `load_paths` lives in `vendor-skills.yaml`.
