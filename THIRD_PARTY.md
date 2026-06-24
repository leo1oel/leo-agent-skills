# Third-party skills

This repository vendors selected third-party skills so a single dotfiles setup can install one stable skills source.

## Vendored sources

- `skills/grill-with-docs` originated from `mattpocock/skills`, path `skills/engineering/grill-with-docs`.
  It has since been rewritten into a self-contained research-grilling skill that maintains project-owned research docs and is adapted for the Nemo firstmate workflow, so it no longer tracks upstream.
- `skills/skill-creator` comes from `anthropics/skills`, path `skills/skill-creator`.

## Licenses

- Matt Pocock skills license: `licenses/mattpocock-skills-LICENSE` (retained because `grill-with-docs` derives from that source).
- Anthropic skill creator license: `licenses/anthropics-skill-creator-LICENSE.txt`.

When updating a still-tracked vendored skill (`skill-creator`), replace its directory from upstream and refresh the matching license file if upstream changed it.
`grill-with-docs` has diverged from upstream and is maintained here directly.
