---
id: WI-0001
title: 'Phase 1: PowerShell installer and provisioning prototype'
status: executing
opened: 2026-08-02
relations:
  parent: EPIC-0001
---

Phase 1 of EPIC-0001 (ADR-0001): unblock Windows provisioning by adding a
second bootstrap path for PowerShell, without touching the proven Bash flow.

- Code: [szcdf/szcdf-core#15](https://github.com/szcdf/szcdf-core/pull/15)
  (draft), branch `develop/multishell-powershell`.
- Scope and verification: `design.md`; task list: `plan.md`.
