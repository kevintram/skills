---
name: roughdraft
description: Open and collaboratively review local Markdown files with the Roughdraft browser editor and Roughdraft-flavored CriticMarkup. Use when the user mentions Roughdraft or `rd`, invokes `$roughdraft`, asks to open a Markdown document for review, wants to leave or process inline comments and suggested edits, or wants a Markdown plan reviewed interactively.
---

# Roughdraft

Use Roughdraft as a synchronous review loop over one local Markdown file. Treat `rd` in natural-language requests as shorthand for Roughdraft, but never create an `rd` shell alias, executable, or symlink.

## Check Availability

Run:

```bash
roughdraft help
```

If the command is missing, install it with `npm i -g roughdraft` only when the user explicitly requested installation. Otherwise, ask before installing a global package.

Use `roughdraft help criticmarkup` when local syntax details are needed.

## Open a Review

1. Ensure the document exists as a local `.md` file. When this skill is active for a plan request, write the plan to Markdown before requesting review.
2. Resolve the absolute path and open exactly one file:

```bash
roughdraft open "/absolute/path/to/file.md"
```

3. Leave the command running in the foreground. Never interrupt, kill, background, detach, or treat the wait as cleanup. Roughdraft exits after the user clicks **Done Reviewing**; that exit is the signal to resume.
4. Tell the user they may leave comments, questions, or suggested edits and click **Done Reviewing**.

## Process Feedback

After the command exits:

1. Read the Markdown file from disk again.
2. Interpret CriticMarkup outside inline and fenced code as review feedback. Treat markers inside code as literal examples.
3. Preserve unrelated Markdown, local links, images, frontmatter, unknown YAML keys, existing review ids, and legacy inline attribute metadata.
4. Do not silently accept insertions, deletions, or substitutions unless the user asks. Address questions and comments inline using the extended format below.
5. Save the file and reopen it with `roughdraft open` when replies or unresolved feedback should continue the document conversation.

## Roughdraft-Flavored CriticMarkup

Base markers:

```text
Comment:      {>>comment<<}
Insertion:    {++new text++}
Deletion:     {--old text--}
Substitution: {~~old text~>new text~~}
Highlight:    {==text==}
```

For new feedback, use compact inline references and final YAML endmatter:

```markdown
Review {==this sentence==}{>>Needs a source.<<}{#c1}.
Add {++one concrete example++}{#s1}.

---
comments:
  c1:
    by: AI
    at: "2026-04-28T12:00:00.000Z"
  c2:
    body: I can add one from the introduction.
    by: AI
    at: "2026-04-28T12:05:00.000Z"
    re: c1
suggestions:
  s1:
    by: AI
    at: "2026-04-28T12:10:00.000Z"
```

Generate stable document-local ids in sequence: `c1`, `c2`, and so on for comments; `s1`, `s2`, and so on for suggestions. Set `by` to the agent or author label, `at` to the current ISO timestamp, and `re` for replies to an existing comment or suggestion. Root comment bodies and suggested text remain inline; replies live in `comments.<id>.body` with a `re` pointer.

Consult the official specification at <https://roughdraft.md/spec/roughdraft-flavored-markdown.md> only when exact parsing, compatibility, or schema behavior is material.
