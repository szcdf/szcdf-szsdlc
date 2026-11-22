---
id: WI-0002
title: 'Phase 2: converge to a single manifest'
status: dropped
opened: 2026-11-21
relations:
  parent: EPIC-0001
  depends_on:
    - WI-0001
---

DROPPED (ADR-0002): a single installer makes manifest convergence unnecessary —
one parser reads one manifest, so there is no `#@bash`-active Bash upgrade and no
`windows.szcdfis` to retire. The parity backlog below also dissolves, because
there is only one implementation. Kept below for the record.

---

Phase 2 of EPIC-0001. Starts after Phase 1 (WI-0001) lands.

## Scope

- Upgrade `bin/szcdfi.sh` to treat `#@bash` as active.
- Retire `windows.szcdfis` in favor of guarded lines in `.szcdfis`.
- Formalize `kind` / `supports` metadata on presets.
- Move session presets to the generator model that emits `env.d/*`.

## Parity backlog (from ADR-0001 and the Phase 1 adversarial review)

- Fix `szcdfi.sh` new-section insert so a source without a trailing newline
  doesn't glue the END marker onto its last line. Do not replicate the bug in
  `szcdfi.ps1`. Not reachable today (every referenced source ends in LF).
- Add explicit symlink-type detection to `Copy-One` in `szcdfi.ps1`, so copy
  over a symlink destination matches `szcdfi.sh` (which replaces it with a
  regular file). Dev-only path on Windows.
