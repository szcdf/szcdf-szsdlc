---
id: WI-0004
title: Split personal profiles, presets and data out of szcdf-core
status: done
opened: 2026-11-21
---

szcdf-core keeps only shared parts: installer, modules, generic presets, the
`default` and `szcdf_tester` profiles, and the files they use.
Machine-specific profiles, presets and data move to a separate personal
package that installs into the same config root after szcdf-core.

- Code: [szcdf/szcdf-core#17](https://github.com/szcdf/szcdf-core/pull/17).
- Scope and reasoning: `design.md`; task list: `plan.md`.
