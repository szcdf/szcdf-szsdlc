## Tasks

- [x] Scaffold the package (`src/` layout, `pyproject.toml`, hatchling, `szcdfi`
      console entry point).
- [x] Port the directive engine (`COPY`, `COPYALL`, `PREPENDTEXT`,
      `APPENDTEXT`), config-root resolution, and path tokens.
- [x] Centralize text handling (UTF-8 no BOM, EOL preservation, marker format,
      END-on-own-line).
- [x] Implement the guard parser (active guard set, platform default, `--guard`).
- [x] Tests: 46 pytest cases, incl. the four parity-bug regressions.
- [x] Create the public repo `szcdf/szcdfi`; push `main`.
- [x] CI green across {ubuntu, macos, windows} × py{3.11, 3.12, 3.13}.
- [x] Claim the PyPI name `szcdfi` (verified free); add the `uv publish` release
      workflow (the publish itself is WI-0006).
