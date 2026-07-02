---
name: bibcite
description: Manage citations and .bib files through the bibcite CLI instead of hand-editing them. Use whenever the task involves adding a reference or \cite for a paper, building or cleaning a bibliography, upgrading arXiv entries to their published versions, checking a .bib file before submission, or normalizing BibTeX.
---

# bibcite

`bibcite` resolves a paper to verified, canonical BibTeX and manages `.bib` files end to end:
it verifies metadata against online sources, dedupes against the target file, writes, tidies, and prints the final citation key as JSON.

The one rule this skill exists to enforce: route every `.bib` change through `bibcite`, and never manually edit `.bib` files.

## Commands

```bash
bibcite add refs.bib <arXiv id | arXiv URL | DOI | title>  # cite a paper: resolve → dedupe → write → tidy
bibcite add refs.bib --bibtex '-' <<'EOF'                  # user pasted BibTeX: still route it through add ('-' reads stdin)
@inproceedings{...}
EOF
bibcite get <query> [--json]         # look a paper up, no .bib file in play: prints BibTeX, writes nothing
bibcite fix refs.bib                 # "clean up my bibliography": upgrade preprints → tidy → lint
bibcite upgrade refs.bib [--dry-run] # just the preprint→published step; --dry-run first on large files
bibcite tidy refs.bib                # formatting only (bibtex-tidy with canonical flags)
bibcite check refs.bib               # read-only lint: duplicates, preprints, missing fields
```

- One paper per `add`/`get` invocation: extra arguments are joined into a single title query (`bibcite add refs.bib attention is all you need` is one lookup), so loop over papers rather than listing several ids in one call.

## Reading the output

Every command except `get`/`tidy` prints a single JSON object on stdout; diagnostics go to stderr, so parse stdout only.

```json
{
  "action": "added",
  "key": "devlin2019bert",
  "title": "{BERT:} Pre-training of Deep Bidirectional Transformers for Language Understanding",
  "venue": "Proceedings of the Annual Conference of the Nations of the Americas Chapter of the Association for Computational Linguistics (NAACL)",
  "published": true,
  "source": "dblp",
  "file": "refs.bib",
  "tidied": true
}
```

- `action` is `added`, `exists` (already in the file — fine, not an error), or `upgraded` (preprint replaced by the published version, key kept).
- `key` is what goes into `\cite{...}`.
  Keys are regenerated on write, so read the key from the JSON every time — never guess one or reuse one remembered from earlier in the session.

## When resolution fails

- Exit code 2 means no source could resolve the query.
  Report that to the user and ask for a stronger identifier (an arXiv id or DOI beats a fuzzy title) instead of fabricating an entry.
- Sources rate-limit occasionally; bibcite falls back across arXiv, DBLP, Semantic Scholar, Crossref, and OpenAlex automatically.
  If every source fails, wait and retry rather than writing the entry by hand.
