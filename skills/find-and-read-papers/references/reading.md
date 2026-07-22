# Reading details

Load this for figures, non-arXiv alphaXiv ids, linked code, or when `arxiv2md` cannot convert the paper.

## Overview and slug ids

```bash
curl -sL "https://www.alphaxiv.org/overview/<id>.md"
```

Classic-id overview 404 means the report is not ready — continue with `arxiv2md`.
Slug id: do not run `arxiv2md`. Use extracted text and/or the source:

```bash
curl -sL "https://www.alphaxiv.org/abs/<id>.md"
curl -sL "https://api.alphaxiv.org/papers/v3/<id>"
```

`abs/<id>.md` is a last-resort prose dump when `arxiv2md` and ar5iv both fail on a real arXiv paper.

## arxiv2md

```bash
uvx --from arxiv2markdown arxiv2md <id> \
  --section-filter-mode include \
  --sections "Abstract,Method,Experiments" \
  -o paper.md
```

Unknown section titles:

```bash
uvx --from arxiv2markdown arxiv2md <id> --remove-refs --remove-toc -o paper.md
```

Flags: `--section-filter-mode include|exclude`, `--sections "A,B,C"`, `--remove-refs`, `--remove-toc`, `-o paper.md` or `-o -`.
Do not call `https://arxiv2md.org/api/...`.
Read `paper.md`. Widen the section list only when a needed detail is missing.

## Figures

`arxiv2md` leaves figures as inert names like `Refer to caption: x1.png`.
Resolve against the paper's HTML directory (versionless id), download, then Read:

```bash
curl -sL "https://arxiv.org/html/<id>/x1.png" -o fig1.png
```

If the caption already includes a directory prefix, use that path under `https://arxiv.org/html/` instead.
Fetch one figure at a time.

## Linked code

When implementation details matter and a GitHub URL is attached to the paper, shallow-clone into a temp dir and inspect configs / entrypoints.
Confirm the repo matches the paper before treating it as ground truth.

## Fallback when there is no arXiv HTML

1. WebFetch `https://ar5iv.org/abs/<id>` with a specific question.
2. alphaXiv `abs/<id>.md` if present.
3. PDF via Read on a page range — last resort, not a full dump.
