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
bibcite add refs.bib --from ids.txt  # several papers: one query per line — use this instead of looping
bibcite add refs.bib <query> --replace # overwrite the matching entry (keeps its key); ERRORS if nothing matches
bibcite add refs.bib <query> --key k1  # replace exactly entry k1 (when title drift defeats auto-matching)
bibcite remove refs.bib <key>        # delete an entry — never delete by hand-editing
bibcite get <query> [--json]         # look a paper up, no .bib file in play: prints BibTeX, writes nothing
bibcite fix refs.bib                 # "clean up my bibliography": upgrade preprints → tidy → lint
bibcite upgrade refs.bib [--dry-run] # just the preprint→published step; --dry-run first on large files
bibcite tidy refs.bib                # formatting only (bibtex-tidy with canonical flags)
bibcite check refs.bib               # read-only lint: duplicates, preprints, missing fields
```

- One paper per `add`/`get` query: extra arguments are joined into a single title lookup. For several papers, write them to a file and use `--from` (one process = shared rate-limit state and one tidy pass) instead of looping `add`.
- An entry that is legitimately preprint-only (will never be published): set `pubstate = {preprint}` on it via `--bibtex --replace` to mute the check/upgrade preprint warnings.

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

- `action` is `added`, `exists` (already in the file — fine, not an error), `upgraded` (preprint replaced by the published version, key kept), or `replaced` (`--replace`, key kept).
- `key` is what goes into `\cite{...}` — always read it from the JSON, never guess one or reuse one remembered from earlier in the session.
- `upgrade` reports unmatched entries with a `reason`: `no_published_version` (trustworthy miss) vs `sources_unavailable` (sources were down — retry later, do not conclude anything).
- A preprint result carries `published_check`: `complete` means every core source answered and the paper really has no published version; `incomplete` means rate limits interfered — re-run those queries later (`bibcite upgrade` will pick them up) instead of accepting the preprint status.
- Camera-ready titles often differ from arXiv ones; bibcite fuzzy-matches the drift (source `dblp-fuzzy`) and updates the entry to the published title — trust its title over the preprint's.

## When resolution fails

- Exit code 2: the paper could not be found.
  Report that to the user and ask for a stronger identifier (an arXiv id or DOI beats a fuzzy title) instead of fabricating an entry.
- Exit code 3: bibcite itself or its sources failed (rate limits, outages) — the paper may well exist.
  Wait and retry, or retry with `--no-cache` off the table; never fall back to writing the entry by hand.
- Successful matches are cached locally (`~/.cache/bibcite/`), so retries and `fix` re-runs are cheap.
