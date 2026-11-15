# Local API

Start with `sortlens serve`. Address: `http://127.0.0.1:8765` (local only). Interactive docs: `/docs`. All bodies are JSON.

## Info
- `GET /api/health` → `{"status": "ok", ...}`
- `GET /api/stats` → counts of imported files

## Jobs (long steps with live progress)
One job runs at a time.
- `POST /api/jobs` `{"kind": ..., "params": {...}}` → 202 with the job. 409 if a job is running, 422 for bad input.
- `GET /api/jobs`, `GET /api/jobs/{id}`
- `POST /api/jobs/{id}/stop` → stops at the next batch; start the same kind again to resume
- `GET /api/jobs/{id}/events` → Server-Sent Events: `progress` events, then one `end` event with the final job. Keep-alive every 15 s.

| kind | params |
|---|---|
| `import_takeout` | `{"paths": ["D:/Takeout/takeout-001.zip"]}` |
| `import_folder` | `{"path": "D:/Photos"}` |
| `dates` | `{}` |
| `dupes` | `{"near": true, "distance": 6}` (both optional) |
| `solo` | `{"name": "Fardin", "photo": "D:/me.jpg", "tagged_only": false, "threshold": null}` |
| `apply_plan` / `undo_plan` | `{"plan": 1, "code": "1466eb5b"}` (checked before the job starts) |

Job fields: `id, kind, params, status, progress, result, error, started_at, finished_at`.

## Plans (review)
- `POST /api/plans` → 201. Body, one of:
  - `{"kind": "trash", "from_dupes": {"run": 1, "kinds": ["exact"]}}`
  - `{"kind": "move", "dest": "Fardin akela", "from_solo": {"run": 1, "buckets": ["solo"]}}`
  - `{"kind": "trash" | "move", "dest": "...", "media_ids": [1, 2, 3]}`
- `GET /api/plans`, `GET /api/plans/{id}` → plan with every item (`action` move/mark/skip, `src`, `dst`, `sidecar_src`, `sidecar_dst`, `status`, `note`) and its `code`
- `POST /api/plans/{id}/apply` `{"code": "..."}` → 202, runs as an `apply_plan` job
- `POST /api/plans/{id}/undo` `{"code": "..."}` → 202, runs as an `undo_plan` job
