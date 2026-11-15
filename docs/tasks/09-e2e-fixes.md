# Task 09: Fixes from the end-to-end check
Status: done
Roadmap line: none (bug fixes found by the full backend run on generated data, 2026-11-12)

## Why
The first end-to-end run (import, people, dates, dupes, solo, plan, apply, undo, API, jobs)
found wrong review lists. No file was ever lost, but a list must be right before the user says yes.

## Fixed
1. `-edited` copies are left out of near-duplicate matching. An original was being marked as an
   extra of its own edit. The pair stays in the `edited` group, where both are kept.
2. Keep choice: after picture size, a loose file (laptop folder or extracted Takeout) is kept over
   the same photo inside a zip. The zip copy then only gets a mark; the laptop file never goes to
   SortLens Trash because of a zip copy. (User said "karo" to this, 2026-11-12.)
3. `extra_bytes` ("space you would free") now counts every extra: exact, near and album copies.
4. `sortlens plan-show` prints the sidecar JSON that moves with each photo (`+ json  src -> dst`).
   Rule: the user sees every file that will move.
5. `sortlens solo` says "models missing, run `sortlens models download`" when only the models are
   missing, and asks to install face support only when OpenCV itself is missing.
6. OpenCV log level is set to errors only, so model loading prints no WARN lines.

## Tests
`test_edit_never_makes_its_original_an_extra`, `test_loose_file_kept_over_zip_copy_and_bytes_counted`,
`test_plan_show_lists_the_json_that_moves_too`, `test_solo_says_models_missing_not_install`;
`test_groups` now expects every extra in `extra_bytes`. 48 tests.

## Left for later (small)
- Empty `SortLens Trash` sub-folders stay after undo (removing them would be a delete; the user
  empties the trash by hand).
- A broken image file is still counted as a photo by the importer; dupes logs it as a hash error.
