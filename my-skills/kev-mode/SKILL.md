---
name: kev-mode
description: End-to-end delivery workflow — mark Linear issue(s) In Progress, implement, run review-and-clean, run an adversarial mitchellh-judge subagent, commit, push and open a PR, and attach evidence screenshots to the Linear issue.
disable-model-invocation: true
---

# Kev Mode

Execute the requested task end to end using this workflow. Complete each phase in order before moving to the next.

## 1. Linear & Branch Setup

- Use the available Linear skill or integration (e.g., official Linear plugin/MCP/CLI) to locate the relevant Linear issue(s) and move them to **In Progress**.
- Make sure you are on a dedicated feature branch (not `main`/`master`). If the Linear issue specifies a branch name, prefer it.

## 2. Implement & Verify

- Implement the requested change.
- Run the project's relevant build, typecheck, lint, and automated test checks.
- Run and test the change **end to end** (exercise the actual app, UI, CLI, or API flow—not just unit tests or typechecks) to verify it works in practice.

## 3. Review & Clean (`review-and-clean`)

- Follow `/review-and-clean` (read `~/.agents/skills/review-and-clean/SKILL.md` and apply it directly) on your changes:
    - Question the diff from first principles, simplify overengineering, check abstractions, and prune unnecessary tests.
- Re-run verification after cleanup edits.

## 4. Adversarial Subagent Review (`mitchellh-judge`)

- Spawn a **fresh subagent** and instruct it to follow `/mitchellh-judge` (`~/.agents/skills/mitchellh-judge/SKILL.md`) against the full diff.
- Triage every finding honestly: fix legitimate bugs, edge cases, regressions, or weak spots, then re-run verification.

## 5. Commit (`commit`)

- Follow `/commit` (`~/.agents/skills/commit/SKILL.md`) to split the changes into logical, well-explained commits with message bodies and passing validation.

## 6. Push & Open PR

- Push the branch to the remote and open a pull request (e.g., via `gh pr create`).
- Include a concise summary, verification notes, and links/references to the relevant Linear issue(s).

## 7. Evidence Screenshots on Linear

- Capture screenshots demonstrating the working change (using the appropriate tool for the project — e.g., Playwright/browser tools for web apps, simulator/MobileBuildMCP for Apple apps). Save screenshots outside the repo (or clean them up) so no untracked artifacts are left behind.
    - If the change has no visual UI surface (e.g., pure backend/CLI/refactor), skip screenshots and note why.
- Attach the screenshots (and the PR link, if not auto-linked) to the relevant Linear issue(s).
    - If the available Linear integration cannot upload image files directly, state that clearly and provide the saved screenshot paths so the user can attach them.
