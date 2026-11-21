# WI-0004 design: split personal files out of szcdf-core

## Rule

A file moves if it is tied to specific machines, networks or accounts.
Generic tool setup stays in core, even if only a personal profile uses it
today.

## What moves

| Item | Why it's personal |
|---|---|
| `src/core-profiles/Nordlander__WSL/`, `Sudlander__WSL/` | Activate only on those hostnames; hardcode personal paths and app shortcuts |
| `src/core-presets/add-zhaonetwork1-accessor.sh` | Loads one specific personal SSH key |
| `src/core-data/mail_zhaostephen_com.gitconfig`, `.github.sshconfig` | Personal identity, include path and GitHub key; only the two moved profiles use them |

Everything else stays: all other presets, the `default` and `szcdf_tester`
profiles, `.dircolors-nord`, the `bin/` scripts and vimconfig.

## How two packages coexist

- The personal package has its own `.szcdfis` and installs into the same
  `~/.config/szcdf/{profiles,presets,data}` folders after szcdf-core.
- `COPYALL` copies entry by entry and never clears a folder, so neither
  package removes the other's files. File names must not collide.
- Moved files keep their names, so profile `register` calls are unchanged.

## Order

1. Create the personal package with the moved files and their history.
2. Install it and confirm the profile still loads.
3. Remove the files from szcdf-core (PR #17).
