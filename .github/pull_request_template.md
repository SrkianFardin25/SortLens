## What and why
Closes #  (issue or `docs/tasks/NN-name.md`)

## What changed
-

## Checklist
- [ ] PR targets `dev`
- [ ] Nothing is ever deleted; scans only read files
- [ ] Any file move goes through a plan and `app/core/safety.py` `move_file` (undo log)
- [ ] Works offline on CPU; batches + resume
- [ ] Missing data stays missing (nothing invented)
- [ ] New behavior has tests
- [ ] `scripts/check.sh` passes
- [ ] `docs/ROADMAP.md` + task file status updated
