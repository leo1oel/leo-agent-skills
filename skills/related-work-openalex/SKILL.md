---
name: related-work-openalex
description: Find related research papers, citations, references, and research lineages using Leo's OpenAlex workflow. Use when the user asks for related work, literature search, citation mapping, reference chasing, prior art, seed-paper expansion, or research context around a paper or idea.
---

# Related work with OpenAlex

Use this skill before mapping related work.

## Workflow

1. Read `references/SEARCHING.md`.
2. Use OpenAlex as the primary index instead of ad-hoc web search.
3. Start from precise title and abstract search when no seed paper is known.
4. Start from the citation graph when a seed paper is known.
5. Expand `related_works`, `referenced_works`, and citing papers.
6. Triage by title, venue, year, citation count, and abstract when needed.
7. Hand promising arXiv papers to the `arxiv-reading` workflow.

## API note

Use `OPENALEX_API_KEY` when it is available.
Omit it when the environment variable is unset.
