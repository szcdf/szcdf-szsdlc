---
id: WI-0003
title: 'Phase 3: Windows conformance CI'
status: groomed
opened: 2026-11-21
relations:
  parent: EPIC-0001
  depends_on:
    - WI-0002
---

Phase 3 of EPIC-0001 (ADR-0001): the conformance harness that keeps the two
installers from drifting. Starts after Phase 2 (WI-0002).

## Scope

- Add a Windows CI runner that runs `bin/szcdfi.ps1` against the manifest.
- Assert the same result tree that `test/installer` asserts on Linux.
