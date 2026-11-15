# Contributing

## Rules first
Read [`docs/SAFETY.md`](docs/SAFETY.md) and [`AGENTS.md`](AGENTS.md). The short version: nothing is ever deleted, scans only read, files move only through an approved plan with an undo log, and only `app/core/safety.py` may touch user files. Tests use generated data only, never real photos.

## Dev setup
```bash
cd backend
uv sync --locked --extra dev --extra faces
uv run pytest -q
```
Windows: `powershell -ExecutionPolicy Bypass -File scripts\setup.ps1 -Dev`.

## Before a commit
Run every CI check: `scripts/check.sh` (Windows: `scripts\check.ps1`). It runs ruff, ruff format, mypy and pytest. CI runs the same on Linux and a Windows setup job.

## Branches
Work on `dev`. `main` only gets `dev` merged when the owner says so.

## Tasks
Work is planned as one file per task in [`docs/tasks/`](docs/tasks/README.md), using the template there. Write the task file before coding, keep `docs/ROADMAP.md` ticked, and add a test for every rule and bug fix.

## Project layout
```
backend/app/importers   Takeout + folder readers
backend/app/pipeline    dates, dupes, faces, solo, shared steps
backend/app/core        config, safety (moves), plans, jobs, doctor
backend/app/db          SQLite store
backend/app/api         FastAPI routes (jobs, plans)
backend/app/cli.py      the `sortlens` command
scripts/                setup.ps1, check.sh, check.ps1
docs/                   guides, references, task files
```
