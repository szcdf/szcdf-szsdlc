## Approach

Port the Bash installer (`szcdf-core/bin/szcdfi.sh`) semantics into a
stdlib-only Python engine; centralize text handling in one module; add a guard
parser; package for `uvx`.

## Alternatives rejected

- A compiled binary (ADR-0002, superseded). Needs a per-platform build matrix;
  uv's managed CPython makes it unnecessary.
- Third-party runtime dependencies. Kept stdlib-only so uv resolves a clean,
  dependency-free tool.

## Risks

- Guard semantics were under-specified upstream (see the entity note). The
  generalized token-set model needs owner ratification.
- Text parity bugs (BOM, EOL, END-marker gluing). Covered by regression tests.
