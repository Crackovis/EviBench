# OPT-HUMAN — `results export-human` optimisation · Implementation report

| Field | Value |
|---|---|
| HEAD SHA (at baseline) | `6a3b83141134f6376ce2cce9751e19de97e548dc` |
| Date | 2026-06-30 |
| Schema | unchanged (no DB migration) |

## What changed

Two cooperating optimisations, neither of which weakens evidence:

1. **Safe parallel storyboard source export.** The storyboard-heavy hot path is
   parallelised through *isolated, module-level* workers. Summaries, metrics,
   manifests and comparability signals stay complete for every selected result;
   the sequential path is the unchanged reference.
2. **Representative storyboards sampled *before* render.** `--storyboards
   representative` now limits the storyboards actually rendered by the source
   export, not just the dashboard gallery. Omitted storyboards are recorded as
   provenance, never as missing evidence.

### Files added

* `imputebench/services/results_interaction/export_engine_worker.py` — module-level
  worker: rebuilds a *fresh* `EvidenceContext` + `default_provider_registry`,
  invokes one planned item, returns a report dict; every failure becomes a
  structured *blocked* dict. Never receives a provider/registry/context/SQLite
  connection/figure (§2.3, §4.4).
* `imputebench/services/results_interaction/human_export/storyboard_sampling_service.py`
  — `StoryboardSamplingService` + `StoryboardSamplingPlan`: deterministic
  stratified subset (`all`/`none`/`representative`), stable
  `sha256(experiment_id + sorted ids + policy_version)` seed, descriptor-first
  strata (`algorithm_id`, `masking_id`, `graph_policy`, `phase`) with an
  id-ordered fallback; `to_provenance()` emits the sampling payload.
* `imputebench/services/results_interaction/human_export/progress_reporter.py` —
  optional parent-process progress sink (no-op default; never affects the
  fingerprint; workers never report directly).

### Files modified

* `export_engine.py` — `execute()` gains `parallel_export`, `export_max_workers`,
  `worker_start_method`, `min_items_for_parallelism`, `parallel_provider_ids`
  (all defaulted → backward compatible). `_invoke_providers()` is policy-aware:
  `_parallel_candidates()` (provider in allow-list **and** `parallel_safe`, count
  ≥ threshold, collision-free `output_subdir`), `_invoke_providers_parallel()`
  (sequential pass for non-candidates + `ProcessPoolExecutor` for storyboard
  items), deterministic merge by **original planned-item index**, conservative
  fall-backs (disabled / 1 worker / below threshold / subdir collision / pool
  failure → sequential).
* `human_export/source_export_service.py` — `export()` forwards the parallel
  kwargs into `engine.execute()` and gains `storyboard_result_ids` /
  `storyboard_policy_payload`; `_apply_storyboard_policy()` produces a **single**
  filtered plan (keeps all non-storyboard items for every result; keeps
  `result_storyboard` only for the selected subset; order preserved; payload
  recorded in `selection_report`). `_configure_source_parallel()` kept for the
  run-fan-out path.
* `human_export/human_evidence_export_service.py` — plans the storyboard sample
  *before* source export, threads `storyboard_result_ids` + omitted set through
  source export, pack build, figures and dashboard; writes
  `provenance/storyboard_sampling.json` (only when sampling materially applied);
  adds a non-fatal omission warning; drives the (no-op default) progress
  reporter; dry-run summary now carries `storyboard_policy`.
* `human_export/storyboard_service.py` — `assemble(..., omitted_result_ids)`
  skips omitted results entirely (neither *blocked* nor *missing*).
* `presentation/human_evidence/dashboard_renderer.py` — `render(...,
  policy_omitted_count)` shows a representative-omission note.
* `cli/results/export_human.py` — `--parallel-source-export` help reworded to
  describe item-level isolated-worker source export.

### Hardened per §2 of the blueprint

* Counts are **measured**, never hardcoded (real `exp_st_v2 = 4062`).
* `parallel_source_export` default stays `False` (opt-in via flag).
* Workers never receive provider/registry/context/SQLite/figure.
* `representative` is an *omission documented in provenance*, not a deletion of
  evidence; summaries/metrics/manifests stay complete for all selected results.
* `KMP_DUPLICATE_LIB_OK` is **not** a normative prerequisite.
* Sequential mode is the reference; parallel reports are merged in original order.

## Determinism & safety

* `min_items_for_parallelism=8`; storyboard `output_subdir` collision check.
* Reports/blocked items merged by original planned-item index → stable manifest.
* Adding `provenance/storyboard_sampling.json` is **guarded** to materially-sampled
  exports, so `all` / unfiltered / ≤cap exports stay byte-identical (existing
  determinism + fixture tests unchanged).

## Tests

| Suite | Result |
|---|---|
| `tests/results_interaction/test_export_engine_parallel_invocation.py` | 12 passed |
| `tests/human_evidence/test_storyboard_sampling_policy.py` | 8 passed |
| `tests/human_evidence/test_source_export_storyboard_filter.py` | 9 passed |
| `tests/human_evidence/test_export_human_parallel_options.py` | 4 passed |
| `tests/human_evidence/performance/test_export_parallel_determinism.py` | 2 passed |
| `tests/human_evidence/test_storyboard_omission_pack.py` | 4 passed |
| `tests/human_evidence/test_progress_reporter.py` | 3 passed |
| **OPT-HUMAN total** | **42 passed** |

Regression: `tests/human_evidence` + `tests/results_interaction` → **785 passed**.
The 28 `tests/cli` failures are **pre-existing** (a `DEPRECATION` banner pollutes
JSON stdout → `JSONDecodeError`); confirmed by `git stash` on a clean tree (same
failures, none in OPT-HUMAN scope).

## Acceptance summary (§12)

* Functional — works with no new flags; `--parallel-source-export` activates
  item-level source-export parallelism for safe storyboard items; non-storyboard
  items exported for all results; `representative`/`none` reduce rendered
  storyboards before source export; staging stays atomic; manifest valid +
  checksummed ✔
* Scientific — no metric value changes; representative policy explicit in
  provenance; dashboard reports omitted count; no raw-UUID leak in public pages
  (verified on a real `exp_st_v2` pack) ✔
* Performance — sequential is the strict reference; `max_workers=0` auto resolves
  to ≤ `min(8, cpu_count)`; no Windows-spawn deadlock; no portable test asserts
  wall-clock < 15 min ✔
* Regression — existing human_evidence / results_interaction / engine tests pass;
  new parallel + sampling tests pass ✔

## Deferred / notes

* Live console progress is wired only as a no-op default reporter (the authoritative
  timing record remains `HumanExportPerformanceTrace`). A `--progress` CLI flag is
  not added (out of scope; the reporter is ready to wire).
* Full-scope `exp_st_v2` (4062) storyboards block with `STMaskBankMissing`
  (legacy banks purged, per MOT12) — a fresh GPU re-run is required for renderable
  ST storyboards; the parallel + sampling machinery is independent of that.
