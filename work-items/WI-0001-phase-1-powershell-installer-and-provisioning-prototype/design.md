# WI-0001 design: PowerShell installer and provisioning prototype

Architecture is ADR-0001. This records the Phase 1 slice as described in
szcdf-core PR #15.

## Deliverables

- `bin/szcdfi.ps1` — PowerShell installer.
  - Reads the same `.szcdfis` directives: `COPY`, `COPYALL`, `PREPENDTEXT`, `APPENDTEXT`.
  - Honors `#@<shell>` guards, so one manifest can serve every installer.
  - Section-marker format is byte-identical to `szcdfi.sh`.
- `src/core-entries/entry-pwsh.ps1` — a thin per-shell loader for `env.d/*.ps1`.
- `src/core-presets/add-claude-md-managed.ps1` — a provisioning preset.
  - Writes an `@import` stub for `~/.claude/CLAUDE.md`.
  - Needs no symlink and no administrator rights.
- `src/core-data/CLAUDE.user.md` — the managed Claude Code content.
- `windows.szcdfis` — a curated Windows spec, so Phase 1 does not touch `.szcdfis`.

## Scope and safety

- No Bash file and no `.szcdfis` line is modified, so the Linux install path is unaffected.
- The Bash `COPYALL` steps also copy the new `CLAUDE.user.md` and the `.ps1` preset. Both are harmless on the Bash side.

## Verification

- Verified end-to-end on Windows PowerShell 5.1 in an isolated sandbox home.
  - Install, shell-guard handling, `$PROFILE` entry wiring, provisioning, and idempotency.
  - A pre-seeded CRLF `$PROFILE` with a non-ASCII char keeps its bytes and CRLF endings.
  - The managed section is not duplicated on re-install.

## Adversarial review

- An adversarial multi-agent review ran on the PowerShell code (find across four dimensions, then refute each finding).
- Two real findings were fixed on the branch.
  - Reads now pass `-Encoding UTF8` (5.1 otherwise ANSI-decodes and corrupts non-ASCII content).
  - `PREPENDTEXT` / `APPENDTEXT` preserve the destination file's dominant EOL.
- Two latent parity gaps went to the Phase 2 backlog (WI-0002).
  - New-section gluing when a source lacks a trailing newline (the fix belongs in Bash, not PowerShell).
  - Copy-over-symlink object-type divergence in editable/dev flows.
