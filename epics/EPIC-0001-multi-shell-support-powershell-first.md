---
id: EPIC-0001
title: Multi-shell support (PowerShell first)
status: active
opened: 2026-08-02
relations:
  informed_by:
    - ADR-0001
---

Make SZCDF work on Windows-native shells, starting with PowerShell, without
rewriting the framework per shell. Architecture: ADR-0001 (split by effect
type: provisioning centralized in one engine, session shaping generated per
shell).

Delivered in three phases:

- WI-0001 — Phase 1: PowerShell installer and provisioning prototype
  ([szcdf/szcdf-core#15](https://github.com/szcdf/szcdf-core/pull/15)).
- WI-0002 — Phase 2: converge to a single manifest.
- WI-0003 — Phase 3: Windows conformance CI.
