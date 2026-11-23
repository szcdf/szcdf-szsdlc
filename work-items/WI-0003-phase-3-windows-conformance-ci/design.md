## Approach

A pure-Python package has no per-platform build. CI installs `uv`, runs the
installer against a fixture `.szcdfis` on each OS, and asserts the same result
tree that `test/installer` asserts on Linux. Release is `uv publish`.

## Note

Reframed off ADR-0002's Rust build / release matrix (superseded by ADR-0003).
The first actual publish is tracked separately by WI-0006.
