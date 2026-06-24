---
name: arxiv-reading
description: Read, inspect, or summarize arXiv papers using Leo's paper-reading workflow. Use when the user provides an arXiv URL or id, asks to read a paper, analyze a preprint, summarize a research paper, compare claims against evidence, or extract assumptions, limitations, strengths, and weak links from a paper.
---

# arXiv reading

Use this skill before reading arXiv papers.

## Workflow

1. Read `references/READING.md`.
2. Convert arXiv papers to clean Markdown with `arxiv2md` before reading whenever possible.
3. Prefer the paper's structured HTML-derived Markdown over PDFs.
4. Use the fallbacks in `READING.md` only when arXiv HTML is unavailable.
5. When summarizing, separate what the paper shows from what the authors imply.
6. Identify hidden assumptions, strongest evidence, weakest link, limitations, and what would need to be true for the result to matter.

## Command

Use `uvx` for the converter:

```bash
uvx --from arxiv2markdown arxiv2md <id> -o paper.md
```
