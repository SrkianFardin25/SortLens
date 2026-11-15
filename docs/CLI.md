# Command reference

Windows: `.\sortlens.cmd <command>`. Linux / macOS: `uv run sortlens <command>` in `backend/`. `sortlens <command> -h` shows help.

## Setup and info
| Command | What it does |
|---|---|
| `doctor` | Checks Python, data folder (write test), face models. Ends with READY or NOT READY |
| `models download` | Downloads the two face models (~39 MB, SHA-256 checked) into `<data>/models` |
| `stats` | Counts of imported files |
| `people` | Names tagged in Google Photos, with counts |
| `serve` | Starts the local API on 127.0.0.1:8765 |

## Reading (never changes files)
| Command | Options |
|---|---|
| `import-takeout PATH [PATH ...]` | Takeout zip parts and/or extracted Takeout folders |
| `import-folder PATH` | Any folder |
| `dates` | Best taken-at date for every file, resumes |
| `dupes` | `--no-near` exact only · `--distance N` near match bits (default 6) |
| `dupes-show RUN` | `--kind exact\|near\|album_copy\|edited` |
| `solo NAME --photo FILE` | `--tagged-only` · `--threshold X` (default 0.363) |
| `solo-show RUN` | `--bucket solo\|group\|check` |

## Review (plans: nothing moves until `apply`)
| Command | Options |
|---|---|
| `plan-trash-dupes RUN` | `--kinds exact,near,album_copy` (default `exact`) |
| `plan-move-solo RUN --to FOLDER` | `--buckets solo,group,check` (default `solo`); folder must be inside the same location |
| `plan-show PLAN` | Full list, sidecar JSON moves, counts, confirm code |
| `apply PLAN --code CODE` | Does the moves. Wrong code = nothing happens (exit code 2) |
| `undo PLAN --code CODE` | Moves everything back |

Exit codes: `0` ok, `2` bad input or refused.
