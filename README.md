# SortLens

Local-first, offline photo organizer. Point it at a **Google Takeout** export or any **laptop folder**. It sorts your photos by date, finds duplicates, and lists photos where one person is alone. Everything runs on your own computer, on the CPU, with no internet after setup.

> **Safety promise:** SortLens never deletes anything. Scans only read your files and write lists to a local database. A file moves only after SortLens shows you the full list and you confirm it with a code. "Delete" means "move into a `SortLens Trash` folder" that only you can empty. Every move can be undone.

## Status
v1 backend: done and checked end to end on generated test data (48 tests, CI on Linux and Windows).
Not done yet: the local web UI, and a test on a real Takeout export. See [`docs/ROADMAP.md`](docs/ROADMAP.md).
Today you use SortLens from the command line (`sortlens ...`) or its local API.

## What it does
| Feature | Command | Moves files? |
|---|---|---|
| Read a Google Takeout export (zips or extracted folder) with all its metadata | `import-takeout` | No |
| Read any laptop folder | `import-folder` | No |
| Best "taken at" date for every photo | `dates` | No |
| Duplicates: exact, look-alike, album copies, `-edited` pairs | `dupes` | No |
| Photos where one person is alone (from one reference photo) | `solo` | No |
| Live progress, stop and resume | local API + SSE | No |
| Review: move duplicates to `SortLens Trash`, move a solo list to a folder, undo | `plan-*`, `apply`, `undo` | Only after your yes |

## Runs on
Windows 11 (main target: a 12th-gen i3 laptop, no GPU). Linux works (CI tests it). macOS should work but is untested.
Python 3.12. About 40 MB of face models are downloaded once.

## Install on Windows (one command)
Download or clone this repo, open PowerShell in its folder, then:
```powershell
powershell -ExecutionPolicy Bypass -File scripts\setup.ps1
.\sortlens.cmd doctor        # READY means all good
```
Options: `-DataDir "D:\SortLens Data"` (where the database lives), `-NoFaces` (skip the solo feature), `-Dev` (test tools).
Setup installs uv and Python 3.12 if missing, installs pinned versions from `backend/uv.lock`, downloads the face models and runs `doctor`. Safe to run again. It never touches your photos.

## Install on Linux / macOS
```bash
cd backend
uv sync --locked --extra faces      # needs uv: https://docs.astral.sh/uv/
uv run sortlens models download
uv run sortlens doctor
```

## First run in 5 steps
```powershell
.\sortlens.cmd import-takeout "D:\Takeout\takeout-001.zip" "D:\Takeout\takeout-002.zip"
.\sortlens.cmd dates
.\sortlens.cmd dupes                    # prints a run number, e.g. 1
.\sortlens.cmd plan-trash-dupes 1       # a plan: nothing moves yet
.\sortlens.cmd plan-show 1              # read the full list and the confirm code
```
Only if the list is right: `.\sortlens.cmd apply 1 --code <code>`. Changed your mind: `.\sortlens.cmd undo 1 --code <code>`.
(On Linux / macOS use `uv run sortlens` instead of `.\sortlens.cmd`.)

The full walk-through, from exporting your Google Photos to undoing a move, is in the **[User guide](docs/USER_GUIDE.md)**.

## Documentation
| Doc | For |
|---|---|
| [User guide](docs/USER_GUIDE.md) | Step by step use, settings, troubleshooting, FAQ |
| [Command reference](docs/CLI.md) | Every command and option |
| [API reference](docs/API.md) | The local HTTP API and live progress (for the coming UI or your own scripts) |
| [Safety](docs/SAFETY.md) | The no-delete rules and how the code enforces them |
| [Google Takeout notes](docs/TAKEOUT.md) | How Takeout exports are laid out and how SortLens reads them |
| [Architecture](docs/ARCHITECTURE.md) | How the pieces fit, database tables, each pipeline step |
| [Roadmap](docs/ROADMAP.md) | Done and next |
| [Contributing](CONTRIBUTING.md) | Dev setup, checks, branches, task files |
| [`docs/tasks/`](docs/tasks/README.md) | The work queue, one file per task |
| [`AGENTS.md`](AGENTS.md) | Rules for coding agents (Claude Code, Cursor) |

## Privacy
No account, no cloud, no telemetry. The API listens on `127.0.0.1` only. Your photos are never copied; the database stores paths, dates, hashes and face numbers (embeddings) in your data folder.

## Contributing
Contributions are welcome: bug reports, ideas, docs and code. Start with [CONTRIBUTING.md](CONTRIBUTING.md). Please follow the [code of conduct](CODE_OF_CONDUCT.md), and report security or data-loss problems privately ([SECURITY.md](SECURITY.md)).

## License
[MIT](LICENSE) © 2026 Shaikh Fardin
