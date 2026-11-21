# szcdf-szsdlc

The SDLC tracker for the [szcdf](https://github.com/szcdf) org: ideas, epics,
requirements, spikes, work items, decisions and the roadmap for every szcdf
repo (starting with [szcdf-core](https://github.com/szcdf/szcdf-core)). Code
lives in those repos; planning lives here.

It's managed with [szsdlc](https://pypi.org/project/zhaostephen-szsdlc/).

## Where things are

| Path | What |
|---|---|
| `ideas/`, `epics/`, `requirements/`, `spikes/`, `work-items/`, `decisions/` | Entities, one per file or folder |
| `roadmaps/roadmap.yml` | The roadmap record |
| `views/` | Generated, human-readable views (inbox, roadmap, board, …). Don't edit them by hand. |
| `.szsdlc/config.yml` | Project config, deep-merged over szsdlc's defaults |

## Usage

```sh
pip install zhaostephen-szsdlc==0.2.4
szsdlc capture "an idea"     # into the inbox
szsdlc inbox                 # unrefined ideas
szsdlc next                  # actionable work, in roadmap order
szsdlc sync && szsdlc validate
```

CI runs `szsdlc validate` on every push and pull request. Run
`szsdlc sync` before committing so the generated views stay current.
