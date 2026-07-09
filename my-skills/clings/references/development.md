# Clings Development

Reference for modifying the clings Swift package. Use this when you need to add a feature, fix a bug, or understand the codebase.

Preferred local development clone: `~/Projects/Code/clings`
Primary fork: https://github.com/kevintram/clings
Upstream repository: https://github.com/dan-hart/clings

## Project Structure

Clings is a Swift package:

- Executable product: `clings`, target `ClingsCLI`
- Library product: `ClingsCore`
- Tests: `ClingsCoreTests`, `ClingsCLITests`
- Key dependencies: `swift-argument-parser`, `GRDB.swift`, `SwiftDate`
- Platform in `Package.swift`: macOS

Main source areas:

```text
Sources/ClingsCLI/Commands/      CLI command implementations (add, today, filter, etc.)
Sources/ClingsCLI/Support/       Runtime helpers, output formatting for CLI
Sources/ClingsCore/ThingsClient/ SQLite reads + JXA/AppleScript writes to Things 3
Sources/ClingsCore/NLP/          Natural-language task parsing ("tomorrow #docs !high")
Sources/ClingsCore/Filter/       Filter expression parser and evaluator
Sources/ClingsCore/Output/       JSON and pretty-print formatters
Sources/ClingsCore/Config/       Local JSON state: saved views, templates, undo, review
Sources/ClingsCore/Analysis/     Focus scoring, review workflow, stats, project audit
Tests/                           Unit and integration tests
```

## Architecture Rules

Respect the hybrid data model:

- Reads use direct SQLite access to the Things 3 database.
- Writes use JXA/AppleScript through Things automation APIs.
- Scheduling and headings may require Things URL scheme auth.
- Never add direct SQLite writes.

Keep command parsing thin in `ClingsCLI`; put business logic, data access, parsing, formatting, and testable behavior in `ClingsCore`.

## Build and Test

Use these commands from the repository root:

```bash
cd ~/Projects/Code/clings
swift build
swift run clings --help
swift run clings today
swift test
swift build -c release
```

## Remotes

Kevin's fork is `origin`; the upstream source is `upstream`. This setup allows pulling upstream changes while pushing work to the fork.

```bash
git remote -v
git fetch upstream --prune
git rev-list --left-right --count upstream/main...origin/main
```

Push development branches to `origin` unless the user explicitly asks for another remote.

## Pre-Commit Checks

Before committing changes:

```bash
bash scripts/asp-preflight.sh --staged --strict
swift build
swift test
```

Before release or help/docs-sensitive changes:

```bash
bash scripts/release-docs-check.sh
```

## Implementation Guidance

### Style

- Use Swift ArgumentParser patterns already present in neighboring command files.
- Avoid force unwraps in production code.
- Preserve JSON output contracts for scriptable commands.

### Testing

- Add or update tests for command parsing, output formatting, filters, NLP parsing, config stores, and mutation behavior as applicable.
- Prefer deterministic tests with mocks or fixture data rather than requiring live Things data.

### Error Handling

- Handle automation permission, missing Things, missing auth token, and not-found cases with actionable user-facing errors.

### Documentation

- When changing command help, update README and `docs/cli/command-reference.md` if behavior or examples drift.
- When adding or changing CLI features, update `~/.codex/skills/clings/references/cli-workflows.md` to reflect the new capabilities.

## Release Process

For a release:

```bash
bash scripts/release-docs-check.sh
swift build -c release
swift test
curl -sL https://github.com/dan-hart/clings/archive/refs/tags/v<VERSION>.tar.gz | shasum -a 256
```

The Homebrew tap lives at `dan-hart/homebrew-tap`, formula `Formula/clings.rb`. Do not push tags, publish releases, or update the tap unless the user explicitly asks.
