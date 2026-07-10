---
name: write-guide-on
description: Generate a grokkable yet detailed standalone HTML guide on a given topic. Use when the user asks to "write a guide on X", "explain X in a guide", or wants a self-contained HTML explainer for a concept, tool, or process.
---

# Write Guide On

Produce a single self-contained HTML file that teaches the requested topic —
clear enough to skim, detailed enough to actually learn from.

## Guidance

- Output one self-contained HTML file with an inline `<style>` block (no
  external dependencies) so it opens standalone in a browser.
- Keep it readable: clear headings, short paragraphs, comfortable line length
  and spacing, and styled callouts for tips and warnings. Choose fresh CSS that
  suits the topic.
- Add interactive visuals/explanations if you think they'd be helpful!
- Save as `<topic-slug>-guide.html` and tell the user the path.
