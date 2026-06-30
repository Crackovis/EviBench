# MOT 13 — ST Campaign Logging · Baseline audit

| Field | Value |
|---|---|
| HEAD SHA | `877559f3885bf5a5fa6b286bc6230f1a01473adf` |
| Date | 2026-06-30 |
| Files in scope | `st_experiment_orchestrator.py`, `cli/experiment/st/campaign.py` |

## Current event log behaviour

`STExperimentOrchestrator.run()` writes JSONL events **inline** in the execution
loop, with three event types and a **naïve** (timezone-less) timestamp:

```json
{"ts": "2026-06-30T03:12:44.332549", "event": "skipped",   "run_id": "...", "idx": 12, "total": 240}
{"ts": "2026-06-30T03:12:44.332549", "event": "completed", "run_id": "...", "idx": 51, "total": 240}
{"ts": "2026-06-30T03:12:44.332549", "event": "failed",    "run_id": "...", "idx": 51, "total": 240, "error": "..."}
```

Source: each branch does `datetime.utcnow().isoformat()` (no tzinfo) and a direct
`f.write(json.dumps(...))`. There is **no shared writer helper**.

## Gaps (the MOT 13 targets)

* No campaign lifecycle events: **`campaign_started` / `campaign_resumed` /
  `campaign_stopped`** are absent — a standalone JSONL cannot tell whether a
  campaign was started fresh, resumed under `--skip-completed`, or interrupted.
* No **run context** on run-level events: `algorithm`, `variant`, `mask_family`,
  `mask_rate`, `graph_policy`, `phase`, `realization_ids`, epoch fields are
  missing, although `STPlanService._build_run()` already stamps all of them onto
  the `ExperimentRun` (`train_config`, `benchmark_*`, `benchmark_guidance_snapshot`).
* `_execution_progress_detail()` reconstructs `algorithm / variant / mask_family /
  graph_policy / rate / realization_count` but only as a **progress-bar string**,
  not a structured dict reusable for events.
* Timestamps are **naïve UTC** (no `+00:00`), hard to merge across machines.

## CLI (`experiment st … run`) — to preserve

* Emits a `compute_environment` JSONL event (host/GPU/RAM) **before** launching —
  already timezone-aware. MUST stay in the CLI (depends on CLI flags + diagnostic).
* Forwards `--event-log`, `--progress-file`, `--experiment-id`, `--skip-prepare`,
  `--cuda` to the orchestrator; closes the reporter in `finally`.
* The CLI must **not** emit `campaign_stopped` (the orchestrator owns lifecycle).

## progress.json — unchanged

```json
{"completed": 3, "skipped": 50, "failed": 0, "total": 240, "current_idx": 53}
```

Kept byte-for-byte: it is the small machine checkpoint; `events.jsonl` is the audit
trail.
