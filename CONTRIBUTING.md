# Contributing

Thanks for helping! Bug reports, ideas, docs fixes and code are all welcome. Please follow the [code of conduct](CODE_OF_CONDUCT.md). Security or data-loss problems go privately, see [SECURITY.md](SECURITY.md). By contributing you agree your work is released under the [MIT license](LICENSE).

## Your first pull request, step by step
1. **Pick something.** Look at [issues](https://github.com/SrkianFardin25/SortLens/issues) (labels `good first issue`, `help wanted`) or `docs/tasks/`. Comment on the issue so others know you are on it. For a big change, open an issue first and agree on the idea.
2. **Fork** the repo on GitHub (Fork button), then clone your fork:
   ```bash
   git clone https://github.com/<you>/SortLens.git
   cd SortLens
   git checkout dev
   git checkout -b fix/short-name
   ```
3. **Set up** (see Dev setup below) and make sure tests pass before you change anything.
4. **Make the change**, add or update a test, and update docs if behavior changed.
5. **Run all checks**: `scripts/check.sh` (Windows: `scripts\check.ps1`).
6. **Commit** with a clear message, push to your fork, and open a pull request **into `dev`** (not `main`). Fill the checklist in the PR form.
7. CI runs on Linux and Windows. A maintainer reviews; small fixes are welcome.

## Reporting bugs
Use the Bug report form. Add `sortlens doctor` output and your OS. Never attach private photos; describe them, or make a small test file.

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
