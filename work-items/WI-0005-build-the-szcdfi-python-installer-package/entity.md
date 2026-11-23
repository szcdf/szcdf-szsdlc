---
id: WI-0005
title: Build the szcdfi Python installer package
status: done
opened: 2026-11-22
relations:
  parent: EPIC-0001
  informed_by:
    - ADR-0003
---

Build `szcdfi`, the single cross-platform installer decided by ADR-0003: a
standalone, stdlib-only Python package in its own public repo, run via `uvx`.

- Repo: [szcdf/szcdfi](https://github.com/szcdf/szcdfi) (public). Local:
  `E:\Projects\Dev\szcdfi`.
- Delivered: `src/` layout, hatchling backend, no runtime deps. The engine ports
  the Bash directive semantics (`COPY`, `COPYALL`, `PREPENDTEXT`, `APPENDTEXT`),
  config-root resolution, and `$CONFIG_ROOT` / `$HOME` / `$VAR` path tokens.
- Text rules centralized: UTF-8 without BOM (reads via `utf-8-sig`, never emits
  one), destination-EOL preserved, the exact `SZCDF_GENERATED_TEXT` marker, and
  the END marker always on its own line (the Bash gluing bug is impossible by
  construction).
- 46 pytest tests, incl. regression tests for the four parity bugs. CI green:
  {ubuntu, macos, windows} × py{3.11, 3.12, 3.13}.
- PyPI distribution name claimed as `szcdfi` (plain; verified free). Not yet
  published — that is WI-0006.

NOTE — a design call, not a port: the guard model was UNDER-SPECIFIED upstream.
The Bash installer has no guard support (it treats `#@bash …` as a plain
comment); only the PowerShell prototype had guards, as a single shell id. It was
generalized here to a `#@<guard> <directive>` model over an active guard SET with
a platform-derived default (`--guard` override). This warrants owner review and
possibly a spike or ADR under EPIC-0001's still-open architecture. Hand-over
notes: `szcdfi-build-notes.md`.
