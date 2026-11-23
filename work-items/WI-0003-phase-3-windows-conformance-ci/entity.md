---
id: WI-0003
title: 'Phase 3: Windows conformance CI'
status: done
opened: 2026-11-21
relations:
  parent: EPIC-0001
---

Cross-platform install and smoke-test CI for the single `szcdfi` Python
installer (ADR-0003), in the szcdfi repo. Reframed twice: first from the original
"conformance harness that keeps two installers in sync" (moot under one
installer), now off the Rust build / release matrix (ADR-0002, superseded) since a
pure-Python package has no per-platform build.

## Scope

- GitHub Actions matrix (Windows, macOS, Linux) installs `uv`, then runs the
  package against a fixture `.szcdfis` and asserts the expected result tree (the
  assertions `test/installer` makes on Linux today).
- Exercise it the way users will: `uvx szcdfi@...` resolving a managed CPython.
- Release: `uv publish` (or equivalent) so `uvx` can resolve the package. No
  binary build, no Releases hosting, no checksum fetch.
