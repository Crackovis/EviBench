# MOT 13 — ST Campaign Logging Audit · Implementation report

| Field | Value |
|---|---|
| HEAD SHA (at baseline) | `877559f3885bf5a5fa6b286bc6230f1a01473adf` |
| Date | 2026-06-30 |
| Agent | Sisyphus (implementation) |
| Schema | unchanged (no DB migration) |

## What MOT 13 changed

A standalone `events.jsonl` can now reconstruct the full ST campaign lifecycle
without opening the DB or recomputing the plan: a **central event writer**,
**structured run context**, and **campaign lifecycle events** replace the three
inline JSON dumps.

### Files modified
* `imputebench/services/spatiotemporal/st_experiment_orchestrator.py`
  * `_now_utc_iso()` — timezone-aware ISO 8601 UTC.
  * `_emit_event()` — single central JSONL writer; one valid object per line;
    `ensure_ascii=False`; flush; swallows write errors so logging never crashes a
    run; mirrors the historical top-level `run_id`/`idx`/`total`/`error` under `data`.
  * `_write_progress()` — extracted progress checkpoint writer (schema unchanged).
  * `_completed_results_for_run()` / `_run_has_completed_result()` /
    `_latest_completed_result_for_run()` — resume pre-scan + completed-result lookup.
  * `_run_event_context()` — projects the persisted `ExperimentRun` /
    `train_config` / `benchmark_*` / `benchmark_guidance_snapshot` into a stable
    context dict (algorithm, variant, mask_family, mask_rate, graph_policy,
    graph_id, phase, realization_ids/count/singular, benchmark ids, training_tier,
    run_stage, epochs_requested; + result_id/best_epoch/epochs_completed/device/
    runtime when a completed result exists).
  * Execution loop rewritten: emits `campaign_started` (pre-loop), `campaign_resumed`
    (after the completed-run pre-scan, exact counts), `started` / `skipped` /
    `completed` / `failed` (each with `context`), and `campaign_stopped` in a
    `finally` (reason `completed` / `user_interrupt` / `error`).
* `imputebench/cli/experiment/st/campaign.py` — narrow `KeyboardInterrupt` handler
  for clean UX (prints a message, exits 130). It does **not** emit
  `campaign_stopped` (the orchestrator owns it); `compute_environment` and reporter
  close are preserved; the CLI signature is unchanged.

### Hardened per §2 (corrections to the draft)
* `campaign_stopped` promised only for normal completion / Python exceptions /
  `KeyboardInterrupt` (interpreter alive for `finally`) — **not** for kill -9 /
  power loss.
* Lifecycle emission centralised in the orchestrator, not split with the CLI.
* `campaign_resumed` emitted **after** the pre-scan, so `skipped_count` /
  `resume_idx` are exact, not estimated.
* Timezone-aware timestamps (`…+00:00`), never naïve `utcnow()`.
* `variant` ← `implementation_variant` (fallbacks `variant` / `architecture_variant`
  / `default`); never confused with the raw token.
* `realization_ids` + `realization_count` exposed; singular `realization` only when
  exactly one; epochs split into `epochs_requested` / `epochs_completed` /
  `best_epoch` (no ambiguous single `epoch`).

## Event schema (after)

```json
{"schema":"imputebench.st-campaign-event/v1","event":"completed","ts":"2026-06-30T03:12:44.332549+00:00",
 "data":{"run_id":"…","idx":51,"total":240,"context":{…}},
 "run_id":"…","idx":51,"total":240,"context":{"algorithm":"stgcn","variant":"stconv_v1",
 "mask_family":"mcar","mask_rate":0.3,"graph_policy":"correlation_train_v1","phase":"test",
 "realization":null,"realization_ids":["test_r000","test_r001"],"epochs_requested":200,
 "epochs_completed":200,"best_epoch":137}}
```

Lifecycle: `campaign_started` → `campaign_resumed` (if skip found prior completed) →
`started`/`skipped`/`completed`/`failed`* → `campaign_stopped`.

## progress.json — unchanged

```json
{"completed": 3, "skipped": 50, "failed": 0, "total": 240, "current_idx": 53}
```

## Tests

| Suite | Result |
|---|---|
| `tests/spatiotemporal/test_st_campaign_event_logging.py` | 6 passed |
| `tests/spatiotemporal/test_st_campaign_context_projection.py` | 5 passed |
| `tests/cli/test_st_campaign_logging_cli.py` | 2 passed |
| MOT 13 total | **13 passed** |

Regression: `tests/progress`, `tests/cli`, `tests/spatiotemporal` (excluding the
pre-existing, unrelated `test_st_recipe_materialization.py` `KeyError:
'calibration_scientific'` and the `test_st_plugin_device_contract.py` plugin-import
collection error) — see the verification run.

Coverage: event-writer format + tz-aware ts + backward-compatible top-level fields;
context projection (all required fields, singleton realization, variant fallback,
missing-run tolerance, completed-result fields); skip/resume sequence with exact
counts; per-run failure (`error_class`, loop continues, `reason=completed`);
`KeyboardInterrupt` (`reason=user_interrupt`, re-raised); JSONL validity;
silent no-op when no `--event-log`; CLI flag forwarding + no duplicate
`campaign_stopped`.

## Manual validation

Run §11.1 against a real Tier-B campaign, then §11.2–§11.4 to confirm JSONL
validity, run-context presence, and the final `campaign_stopped` reason. (Deferred
to the candidate's GPU environment; the unit suite covers the logic.)

## Acceptance summary (§12)

* Lifecycle — `campaign_started` once; `campaign_resumed` on prior-completed skips;
  `campaign_stopped` on completion / `KeyboardInterrupt` / fatal exception; not
  promised for kill -9 ✔
* Run context — `started`/`skipped`/`completed`/`failed` all carry context with
  algorithm/variant/mask_family/graph_policy/phase/realization_ids; non-ambiguous
  epoch fields ✔
* Compatibility — one JSON object per line; old top-level `run_id`/`idx`/`total`
  kept; `progress.json` unchanged; CLI signature unchanged; `compute_environment`
  preserved; no DB change ✔
* Robustness — tz-aware timestamps; centralised writer; write failure does not
  crash training; context tolerates missing run/result; tests cover
  skip/resume/interrupt/failure ✔

## Known limitations / deferred

* `campaign_progress` per-run event (§4.8) not implemented — `progress.json`
  remains the per-run checkpoint (MAY, not required).
* `campaign_stopped` cannot be guaranteed for OS-level kill / power loss /
  interpreter crash (documented, by design).
