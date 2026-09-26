# Fabryka AI Notes

Technical reports, design notes and proposals shared between teams (SlayerLab, Fabryka AI and friends) for know-how
exchange and attribution. **PDFs only. One folder per paper.**

## Quick start (people and AI agents)

1. Pick your team folder under `papers/` (create it once, lowercase, e.g. `papers/slayerlabs/`, `papers/fabryka/`).
2. Create a folder for the paper: `papers/<team>/YYYY-MM-DD_short-slug/` (date of the **first** version, lowercase,
   hyphens, no spaces).
3. Put the reviewed PDF there as `paper.pdf`.
4. Add `meta.yaml` next to it (template below) with the SHA-256 of that exact PDF: `sha256sum paper.pdf`.
5. Commit only that folder: `git commit -m "paper: <team>/<slug> v<version>"`, then push or open a pull request.

## Layout

```
papers/
  slayerlabs/
    2026-09-26_gollem-v5-report/
      paper.pdf
      meta.yaml
    2026-09-26_data-queue-quarantine/
      paper.pdf
      meta.yaml
  fabryka/
    2026-10-01_example-slug/
      paper.pdf
      meta.yaml
```

A new version of the same paper replaces `paper.pdf` in the same folder and bumps `version` in `meta.yaml`;
the folder name does not change. Git history keeps the old versions.

## `meta.yaml` template

```yaml
title: "Full title of the paper"
type: report              # report | proposal | note | negative-result
status: draft             # draft | reviewed | final
version: 1
date: 2026-09-26          # date of this version
team: slayerlabs
authors:
  - name: First Author
    role: idea, writing
  - name: Second Author
    role: experiments
contact: First Author
pdf_sha256: <full sha256 of paper.pdf>
results: none             # none | preliminary | final
license: not decided      # e.g. CC-BY-4.0 once the authors decide
related: []               # other folders in this repo, e.g. [slayerlabs/2026-09-26_gollem-v5-report]
```

## Rules

1. One paper, one folder, one `meta.yaml`. Keep the folder name stable across versions.
2. `status: reviewed` or `final` only after the authors' review of that exact file (the sha in `meta.yaml`).
3. Say what the paper is. A proposal with no results says `results: none`; intermediate numbers are labelled as such
   inside the PDF.
4. Attribution goes in `authors` with roles. Ideas taken from another paper in this repo go in `related`.
5. **No LaTeX sources.** Source comments often contain local paths, host names and internal references.
6. **No secrets, tokens, private URLs or personal data** in PDFs or metadata. Scan the PDF text before the first
   commit. If a secret or personal data ever lands in the repo, rotate the secret: deleting or replacing the file
   does not remove it from git history.
7. Every paper states its `license` in `meta.yaml`. Until the repository has a `LICENSE` file and a paper states
   otherwise, all rights are reserved by its authors.
8. Never edit another team's folder; open an issue or a pull request instead.

## For AI agents

- Add a paper: create `papers/<team>/YYYY-MM-DD_slug/`, copy the reviewed PDF as `paper.pdf`, write `meta.yaml` with
  the sha of that file, commit as `paper: <team>/<slug> v<version>`.
- Update a paper: replace `paper.pdf`, update `pdf_sha256`, `version`, `date` and `status`; commit as
  `paper: <team>/<slug> v<version>`.
- Check before committing: `sha256sum paper.pdf` equals `pdf_sha256`; the PDF text contains no paths, tokens or
  personal data; only files inside your own paper folder changed.
