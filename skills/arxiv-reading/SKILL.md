---
name: arxiv-reading
description: Read, inspect, or summarize arXiv papers using a clean-markdown-first paper-reading workflow. Use when the user provides an arXiv URL or id, asks to read a paper, analyze a preprint, summarize a research paper, compare claims against evidence, or extract assumptions, limitations, strengths, and weak links from a paper.
---

# arXiv reading

Read papers as clean Markdown, never as PDF.
For the judgment side, what to extract from a paper and how to weigh it, use the `research-taste` skill.

## Convert before reading

For arXiv, convert to Markdown with `arxiv2md`, which parses arXiv's structured HTML and keeps math as LaTeX, real tables, and a section tree:

```bash
uvx --from arxiv2markdown arxiv2md <id> -o paper.md
```

The id is the same across the `/abs/`, `/pdf/`, and `/html/` URL forms, so a PDF link works too; just take its id.

## Figures

`arxiv2md` keeps the text, math, and tables but does not download figures.
Each figure appears in the Markdown as an inert line like `Refer to caption: 2501.11120v1/x1.png` — a relative asset path, not a URL and not a saved file.

To actually see a figure, resolve that path against `https://arxiv.org/html/`, download the asset, then open it with the Read tool (Read renders images visually):

```bash
curl -sL https://arxiv.org/html/2501.11120v1/x1.png -o fig1.png
```

Then `Read` `fig1.png`.
The pre-tool-use hook that routes paper reads through `arxiv2md` allows these image-asset downloads (`.png`, `.jpg`, `.svg`, …) while still blocking direct HTML/PDF paper fetches, so pull figures one file at a time rather than fetching the whole HTML page.
Only download the figures you actually need — most papers can be read from the text and a couple of key figures.

## Fallbacks

When `arxiv2md` has no HTML to work with (older papers arXiv hasn't rendered to HTML), in order:

1. WebFetch on `https://ar5iv.org/abs/<id>` with a specific question; ar5iv renders HTML from LaTeX source for most older papers.
2. The PDF, only as a last resort, read with the Read tool on a page range rather than a blind full-text dump. The abstract alone is often enough to decide it is not worth more.
