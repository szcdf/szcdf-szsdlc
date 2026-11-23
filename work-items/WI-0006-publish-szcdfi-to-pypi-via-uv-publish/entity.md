---
id: WI-0006
title: Publish szcdfi to PyPI via uv publish
status: groomed
opened: 2026-11-22
relations:
  parent: EPIC-0001
  informed_by:
    - ADR-0003
  depends_on:
    - WI-0005
---

Publish `szcdfi` to PyPI so `uvx szcdfi@latest` resolves it — ADR-0003's headline
invocation.

- The package and a `release.yml` already exist (WI-0005). The distribution name
  `szcdfi` is claimed (verified free) but NOTHING is published yet.
- BLOCKED on owner ops: configure PyPI Trusted Publishing for the `szcdfi`
  project (or set a `PYPI_API_TOKEN` secret), then push a release tag to trigger
  `uv publish`.
- Running `uv publish` with real credentials is the owner's action, not an
  agent's.

Done = `szcdfi` is on PyPI and `uvx szcdfi@latest` resolves and runs it.
