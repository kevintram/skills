---
name: summarize-changes
description: Summarize code changes by file and function, including why each change was made or the most likely rationale. Use when asked to summarize, explain, or report on a diff, commit, pull request, or other set of code changes.
---

Please summarize the specified changes in each file with functions, and if you made the changes, please also write a line about why you made the change. If you weren't use your best judgement about why the change was made.

## Inspiration

- [Dax (@thdxr), July 4, 2026](https://x.com/thdxr/status/2073238046296924466): After a large change, a per-file summary makes unusual decisions easy to spot and refine without reading the entire diff. Focus on the affected files and function signatures rather than every implementation detail.

  > “files + functions signatures i need to know, care less about function body”

- [Dax (@thdxr), July 4, 2026](https://x.com/thdxr/status/2073239676677452010): Give each changed file one line that explains what changed and why.

  > “i ask for each file and a line about what it did to it and why”
