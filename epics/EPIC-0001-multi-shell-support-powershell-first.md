---
id: EPIC-0001
title: Multi-shell support (PowerShell first)
status: active
opened: 2026-08-02
relations:
  informed_by:
    - ADR-0003
---

Make SZCDF work on Windows-native shells (and other shells) without rewriting
the framework per shell.

- Installer: decided by ADR-0003 (supersedes ADR-0002) — ONE standalone,
  stdlib-only Python package named `szcdfi`, in its own repository, run via
  `uvx`. uv self-fetches a managed CPython, so there is no build matrix and no
  hosted binaries.
- Broader architecture (effect-type split; thin per-shell entries; session
  shaping generated into `env.d`): WORKING DIRECTION for now, to be settled by
  later ADRs or spikes. ADR-0001 proposed it as one big decision; that is being
  stepped back from (rejected) as premature. The high-level phases are kept here
  as direction; the rest is still being hashed out.

Work items:

- WI-0001 — PowerShell installer + provisioning prototype
  ([szcdf/szcdf-core#15](https://github.com/szcdf/szcdf-core/pull/15)). The
  PowerShell installer is superseded by the single Python installer (ADR-0003);
  its entry / provisioning artifacts carry forward.
- WI-0002 — DROPPED: manifest convergence is moot under a single installer.
- WI-0003 — DONE: cross-platform install / smoke-test CI for the Python package.
- WI-0005 — DONE: build the `szcdfi` Python package
  ([szcdf/szcdfi](https://github.com/szcdf/szcdfi), public; CI green across
  3 OS × py 3.11 / 3.12 / 3.13).
- WI-0006 — publish `szcdfi` to PyPI so `uvx` resolves it (blocked on owner PyPI
  Trusted Publishing / token setup).

Open design question: WI-0005 had to GENERALIZE the guard model (the Bash
installer has no guard support; the PS prototype had single-id guards). It now
uses a `#@<guard> <directive>` active-guard-SET with a platform default. That was
a build-time design call, not a port, and awaits owner ratification — a candidate
spike or ADR under the still-open architecture above.
