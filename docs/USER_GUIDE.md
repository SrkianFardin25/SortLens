# SortLens user guide

This guide takes you from "my photos are in Google Photos" to "my duplicates are in a trash folder I can check, and my solo photos are in their own folder", with an undo at every step.

On Windows type `.\sortlens.cmd <command>` in the SortLens folder. On Linux / macOS type `uv run sortlens <command>` inside `backend/`. This guide writes just `sortlens <command>`.

## 1. Install
See [Install on Windows](../README.md#install-on-windows-one-command) or [Linux / macOS](../README.md#install-on-linux--macos). Then:
```
sortlens doctor
```
`READY` at the end means Python, the data folder and the face models are fine. Fix any `FAIL` line it prints.

## 2. Get your Google Takeout export
1. Open https://takeout.google.com and sign in.
2. Click **Deselect all**, then tick only **Google Photos**.
3. Choose **Export once**, file type **.zip**, and a size (2 GB or 10 GB both work).
4. Google emails you when it is ready. Download **all** parts (`takeout-...-001.zip`, `-002.zip`, ...) into one folder, for example `D:\Takeout`.

You do not need to unzip. SortLens reads the zips directly and never changes them. If you prefer, you can extract them; SortLens reads the extracted `Takeout` folder too.

Keep the zips (or the extracted folder) on the same drive where you want things moved later. SortLens only moves files within one drive.

## 3. Import (reads only)
```
sortlens import-takeout "D:\Takeout\takeout-001.zip" "D:\Takeout\takeout-002.zip"
sortlens import-folder "D:\Photos"          # optional: any laptop folder
sortlens stats
```
- Pass every zip part in one command. Photos and their `.json` sidecars can be in different parts.
- Safe to run again: unchanged files are skipped, so a stopped import resumes.
- Takeout extras are kept: people names, albums, favorite, archived, Google trash, camera GPS, upload time, live-photo pairs, `-edited` copies. `sortlens people` lists the names Google tagged.

## 4. Dates (reads only)
```
sortlens dates
```
Picks one "taken at" date per photo, in this order: Takeout `photoTakenTime`, camera EXIF, a date in the file name (WhatsApp, `IMG_20240101_...`), and the file time (laptop folders only). The zip or extract time is never used as a photo date, because it is only the export date.

## 5. Duplicates (reads only)
```
sortlens dupes                       # prints: List 1 ...
sortlens dupes-show 1 --kind exact   # exact | near | album_copy | edited
```
| Kind | Meaning | Suggested action |
|---|---|---|
| `exact` | Same bytes | Keep one, the rest are extras |
| `near` | Looks the same: resized, re-saved, WhatsApp copy | Keep the biggest, the rest are extras |
| `album_copy` | Takeout puts album photos in two places | Keep the `Photos from YYYY` one |
| `edited` | Google's `-edited` file next to its original | Keep both |

Each group shows one `keep` and its `extra` files. The keep is chosen as: not an album copy, more pixels, a loose file over one inside a zip, a real date, bigger file. `--no-near` is faster (exact only). `--distance 6` sets how alike "near" must be (lower = stricter).

## 6. Solo list: photos where one person is alone (reads only)
Needs face support (installed by default; skipped with `-NoFaces`).
```
sortlens solo "Fardin" --photo "D:\me.jpg"
sortlens solo-show 1 --bucket solo      # solo | group | check
```
- `--photo`: a clear photo with only that person's face.
- First it checks photos Google tagged with that name, then all other photos. `--tagged-only` checks only tagged ones (much faster).
- Lists: `solo` (one face, it is them), `group` (they are in it with others), `check` (tagged but not confirmed, with the reason).
- On a CPU laptop it takes time: roughly 15 minutes per 10,000 photos (estimate; real speed depends on your machine). It resumes if stopped.

## 7. Review, apply, undo (the only step that moves files)
Make a plan. Nothing moves yet:
```
sortlens plan-trash-dupes 1                     # extras of dupes list 1 -> SortLens Trash
sortlens plan-trash-dupes 1 --kinds exact,near  # include look-alikes too
sortlens plan-move-solo 1 --to "Fardin akela"   # solo list 1 -> a folder
sortlens plan-show 1
```
`plan-show` prints every file, where it goes, the `.json` sidecar that goes with it, and a confirm code. Read it. Then:
```
sortlens apply 1 --code <code>
sortlens undo 1 --code <code>      # puts everything back
```
What happens to each file:
- `move`: moved inside the same location. Trash goes to `<location>\SortLens Trash\<same sub-folders>`.
- `mark`: the file is inside a Takeout zip, so it cannot be moved. It only gets a mark in the database.
- `skip`: not moved, with the reason (already there, outside the location, already in trash, changed since the scan).

Rules: nothing is deleted, a name clash gets `a (1).jpg`, every move is written to the undo log first, and you empty `SortLens Trash` yourself when you are sure.

## 8. Live progress and stopping
`sortlens serve` starts the local API on http://127.0.0.1:8765. Long steps can run as jobs with live progress and a stop button for the coming UI. See [API reference](API.md). In the command line, Ctrl+C is safe: finished batches are saved and the next run resumes.

## Settings
Set as environment variables (or in a `.env` file in `backend/`):
| Variable | Default | Meaning |
|---|---|---|
| `SORTLENS_DATA_DIR` | `~/.sortlens` | Database, models and undo log |
| `SORTLENS_BATCH_SIZE` | `50` | Files per saved batch |
| `SORTLENS_HOST` / `SORTLENS_PORT` | `127.0.0.1` / `8765` | API address (keep it local) |

The data folder holds `sortlens.db`, `models/` and `undo_log.jsonl`. Your photos are never copied there. Deleting the data folder only forgets the scans; your photos are not touched.

## Troubleshooting
| Problem | Fix |
|---|---|
| `doctor` says NOT READY | Read the FAIL line; usually run `setup.ps1` again |
| `face models missing` | `sortlens models download` |
| `face support is not installed` | Run setup again without `-NoFaces` |
| PowerShell blocks the script | Use the exact command with `-ExecutionPolicy Bypass` |
| A photo has no date | Takeout had no `photoTakenTime` and the file has no EXIF; it stays without a date rather than a wrong one |
| A plan item says `cross drive` | Source and target are on different drives; keep the photos on one drive |
| `wrong code` | Copy the code exactly from `plan-show`; nothing moved |
| Antivirus warns about the setup | Setup only installs uv, Python packages and two model files; allow it or install by hand (see Linux steps) |

## FAQ
**Does it upload anything?** No. After setup it works offline.
**Can it delete my photos?** No. There is no delete code; a test checks this.
**Will it change my Takeout zips?** Never. Zip files are only read.
**I applied a plan by mistake.** `sortlens undo <plan> --code <code>`.
**Can I use it without Google Takeout?** Yes, `import-folder` works for any folder.
