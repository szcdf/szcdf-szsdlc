---
id: ADR-0002
title: Unify the installer into a single cross-platform Rust binary
status: accepted
opened: 2026-11-22
decided: 2026-11-22
---

- Deciders: Stephen Zhao
- Scope: this decides ONLY the installer. The broader multi-shell architecture
  (effect-type split, entry / runtime layers, phased rollout) is NOT decided
  here. That direction is captured separately for later ADRs or spikes.

## Context

The installer is provisioning, and provisioning does not need to run in the target shell.

- The installer reads a manifest and writes files. It does not run inside the shell it configures.
- The Bash framework ships a Bash installer (`bin/szcdfi.sh`); a Windows-native path needs an installer too.
- The obvious path — one installer per bootstrap environment (`szcdfi.sh` plus a PowerShell `szcdfi.ps1`) — was prototyped, and it showed its cost.
  - Two implementations of the same directive logic drift. The prototype's adversarial review found real divergences (ANSI-vs-UTF-8 reads, CRLF normalization, END-marker gluing), each possible only because there are two installers.
  - It also forces manifest-convergence work (teaching the Bash installer that `#@bash` is active) that exists only to keep two parsers in step.
- A hard constraint settles it.
  - There are target environments that need userspace configuration but have no Rust toolchain, and cannot be assumed to have Bash, PowerShell, or Python either.
  - So the installer must run with ZERO runtime dependency on the target.

## Options

- One cross-platform Rust binary (CHOSEN).
  - Cost: a build-and-release pipeline and binary hosting; the directive engine is written once in Rust; fetched binaries need a trust story.
  - Benefit: zero runtime on the target; one implementation (no parity-bug class); one manifest with no extra convergence work.
- One Python installer (stdlib-only).
  - Rejected: needs a Python interpreter, which is absent on fresh Windows and not guaranteed on the toolchain-less targets. It trades one install-time dependency for another.
- Two installers, one per bootstrap environment.
  - Rejected: duplicated directive logic, the parity-bug class, and the manifest-convergence tax.
- One Rust binary, build-from-source only (`cargo build` / `cargo install`).
  - Rejected: requires a Rust toolchain on every target; the toolchain-less environments rule this out as the primary path. Kept only as a FALLBACK.

## Decision

Build ONE self-contained Rust binary as the single installer, distributed as CI-built per-platform binaries that a tiny per-OS bootstrap fetches over HTTPS.

- Engine.
  - Reads `.szcdfis`, applies `COPY`, `COPYALL`, `PREPENDTEXT`, `APPENDTEXT`, and resolves platform / shell guards uniformly.
  - Correct text handling lives in this one place: UTF-8 without BOM, destination-EOL preservation, and the exact SZCDF section-marker format.
- Distribution.
  - CI (GitHub Actions, native runners per OS) builds one binary per target triple on every release.
  - Binaries are published to GitHub Releases.
  - A tiny per-OS bootstrap fetches the matching binary: `install.sh` (curl) on Unix, `install.ps1` (Invoke-WebRequest) on Windows. The bootstrap is the only per-OS file, and it is a few lines.
  - Fetched binaries are verified against a published checksum.
  - `cargo build` / `cargo install` is the fallback for toolchain-equipped or exotic targets.
- Initial target triples.
  - `x86_64-pc-windows-msvc`
  - `x86_64-apple-darwin`, `aarch64-apple-darwin`
  - `x86_64-unknown-linux-gnu`, `aarch64-unknown-linux-gnu`
  - `x86_64-unknown-linux-musl` (static, for minimal environments)
- The Bash `szcdfi.sh` and the prototype `szcdfi.ps1` are retired.

## Consequences

Written as if the choice turns out wrong — the part a future reader needs.

- A release pipeline is now load-bearing.
  - Users fetch a binary instead of "clone and run." If Releases, CI, or the checksum are broken, installation is broken.
  - The bootstrap must handle a missing or mismatched asset gracefully, and offer the `cargo` fallback.
- A compiled artifact adds supply-chain surface.
  - A fetched binary runs with the user's rights. Trust rests on build provenance and the checksum; weaken either and this is worse than a readable shell script.
- The directive engine is net-new Rust.
  - Section markers, encoding, and EOL must be covered by tests from the start.
- Banked benefits (the reason to accept the costs).
  - Zero runtime on the target: works on environments without Bash, PowerShell, Python, or Rust.
  - The installer parity-bug class is eliminated by construction.
  - A single manifest needs no `#@bash`-active Bash upgrade and no `windows.szcdfis`.
  - Adding a shell stays "entry file + manifest line."
- Prototype artifacts.
  - The PowerShell installer `szcdfi.ps1` becomes throwaway.
  - `entry-pwsh.ps1`, `add-claude-md-managed`, `src/core-data/CLAUDE.user.md`, and the `@import` stub are entry / provisioning artifacts and carry forward independent of the installer.
