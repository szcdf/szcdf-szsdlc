---
id: WI-0003
title: 'Phase 3: Windows conformance CI'
status: groomed
opened: 2026-11-21
relations:
  parent: EPIC-0001
---

Cross-platform build, release, and test CI for the single Rust installer
(ADR-0002). Reframed from the original "conformance harness that keeps two
installers in sync," which is moot now that there is one installer.

## Scope

- GitHub Actions matrix (native runners per OS) builds one binary per target
  triple and publishes to Releases with checksums.
- Run the installer on Windows, macOS, and Linux against the manifest and assert
  the expected result tree (the assertions `test/installer` makes on Linux today).
- Smoke-test the per-OS fetch bootstrap (`install.sh` / `install.ps1`).
