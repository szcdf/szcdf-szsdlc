---
id: ADR-0003
title: Unify the installer into a standalone Python package run via uvx
status: accepted
opened: 2026-11-22
decided: 2026-11-22
relations:
  supersedes: ADR-0002
---

- Deciders: Stephen Zhao
- Scope: this decides ONLY the installer — its language, distribution, and home
  repo. The broader multi-shell architecture (effect-type split, entry / runtime
  layers, phased rollout) is NOT decided here; that direction stays on EPIC-0001
  for later ADRs or spikes.
- Supersedes ADR-0002, which chose a compiled Rust binary. The reasoning for
  moving off a compiled binary is below.

## Context

The installer is provisioning: it reads a manifest and writes files, and does not run inside the target shell.

- ADR-0002 chose a self-contained Rust binary for ONE reason: zero runtime
  dependency on the target.
  - It accepted real costs for that: a load-bearing CI build / release pipeline,
    per-platform binary hosting, and the supply-chain surface of a fetched
    compiled artifact.
- Two facts change that premise.
  - Every target environment can obtain `uv` at install time (network plus
    `winget` on Windows, `curl | sh` elsewhere). Confirmed with the owner.
  - `uv` self-fetches a managed, modern CPython (python-build-standalone). So
    "no interpreter on the target" is solved by uv, not by freezing a binary.
- Owner preference: Python over Rust for anything that is not
  performance-critical.
  - The installer is not performance-critical. It runs rarely, and its logic is
    trivial: `COPY`, `COPYALL`, `PREPENDTEXT`, `APPENDTEXT` from a manifest.
- With uv available, the compiled-binary cost buys nothing that is still needed.

## Options

- A standalone Python package, run via `uvx` (CHOSEN).
  - Stdlib-only; published so `uvx szcdfi@latest` runs it; uv self-fetches CPython.
  - Cost: `uv` must be present on the target (trivial to get); first run
    downloads a managed CPython.
  - Benefit: no CI build matrix, no hosted binaries, no per-triple release; one
    implementation; one manifest; authored in the preferred language.
- One cross-platform Rust binary (ADR-0002) — SUPERSEDED.
  - Its only edge over uv + Python was zero prerequisite on the target. With uv
    universally obtainable that edge does not apply, and the binary carries a
    load-bearing release pipeline and supply-chain surface that the Python path
    avoids.
- A frozen Python binary (PyInstaller / python-build-standalone), curl-able.
  - Rejected: keeps the per-OS CI build matrix of the Rust path, and adds larger
    artifacts, slower cold start, and Windows antivirus false-positives. It pays
    the binary cost without the binary's cleanliness.
- Two installers, one per bootstrap environment (`szcdfi.sh` plus `szcdfi.ps1`).
  - Rejected: duplicated directive logic, the parity-bug class, and the
    manifest-convergence tax.

## Decision

Build ONE installer as a standalone, stdlib-only Python package in its own repository, run via `uvx`.

- Language and shape.
  - Pure-stdlib Python; no third-party runtime dependencies.
  - Reads `.szcdfis`, applies `COPY`, `COPYALL`, `PREPENDTEXT`, `APPENDTEXT`, and
    resolves platform / shell guards uniformly.
  - Correct text handling lives in this one place: UTF-8 without BOM,
    destination-EOL preservation, and the exact SZCDF section-marker format.
- Home: its own repository, separate from szcdf-core.
  - `szcdfi` is the generic engine; it reads any `.szcdfis`. szcdf-core and the
    personal content repos provide the manifests and content.
  - Versioned and released on its own, independent of content.
- Distribution.
  - Run via `uvx szcdfi@latest` — ephemeral and always-latest, the right fit for
    a run-rarely installer; `uv tool install szcdfi` gives a persistent shim.
  - uv provides a managed CPython, so there is no system-Python dependency and no
    version-skew bug class.
  - Published so uvx can resolve it (PyPI, or `git+https` from the repo as an
    interim).
  - No per-platform build, no binary hosting, no checksum-verified fetch.
- The one prerequisite is uv: `winget install astral-sh.uv` (Windows) or
  `curl -LsSf https://astral.sh/uv/install.sh | sh` (Unix / macOS). A tiny
  bootstrap may wrap this.
- The Bash `szcdfi.sh` and the prototype `szcdfi.ps1` are retired.

## Consequences

Written as if the choice turns out wrong — the part a future reader needs.

- `uv` is now a hard prerequisite on every target.
  - A target that cannot obtain uv (air-gapped, no install-time network, no
    package manager) cannot install. ADR-0002's binary covered that case; this
    does not. The constraint was checked with the owner: all targets can get uv.
    A locked-down target appearing later is a new decision (a frozen binary or
    equivalent).
  - First run pays a one-time managed-CPython download (tens of MB).
- A new repository to own.
  - Its own CI, release, versioning, and tracker — more surface than a directory
    in szcdf-core, bought for independent versioning and a clean engine / content
    split.
- The directive engine is net-new Python.
  - Section markers, encoding, and EOL must be covered by tests from the start.
- Banked benefits (the reason to accept the costs).
  - No build matrix, no hosted artifacts, no per-triple release pipeline — the
    load-bearing release pipeline ADR-0002 named as its worst downside is gone.
  - uv self-manages the interpreter: no "system Python missing or too old" class.
  - One implementation: the installer parity-bug class is eliminated by construction.
  - One manifest: no `#@bash`-active Bash upgrade and no `windows.szcdfis`.
  - Authored in the preferred language for non-performance-critical code.
- Prototype artifacts (carried over from ADR-0002).
  - The PowerShell installer `szcdfi.ps1` and `windows.szcdfis` are throwaway.
  - `entry-pwsh.ps1`, `add-claude-md-managed`, `src/core-data/CLAUDE.user.md`, and
    the `@import` stub are entry / provisioning artifacts and carry forward
    independent of the installer.
