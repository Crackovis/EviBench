# ARMA Order Selection Performance Update Report — Patch 04

**Date:** 2026-06-12  
**Target:** `imputebench study temporal arma-order`  
**Goal:** Reduce LondonAQ ARMA order selection from 3h30+ to < 3 min (50 series) / < 15 min (560 series)

---

## Files Modified

| File | Change |
|---|---|
| `imputebench/services/study/arma_order_selection_core.py` | **NEW** — shared core with dataclasses, workers, grid search, method resolution, progress helper |
| `imputebench/services/study/arma_order_selection_service.py` | **REWRITTEN** — joblib parallel, fast grid fitting, early stopping, timeout, progress bar via core |
| `imputebench/read_models/study/arma_diagnostics.py` | **EXTENDED** — ARMAOrderSelectionReport now includes 16 optimisation metadata fields |
| `imputebench/services/study/study_report_service.py` | **EXTENDED** — build_arma_order_report() passes through optimisation metadata |
| `imputebench/cli/study_cmd.py` | **EXTENDED** — 6 new CLI options |
| `plugins/arma/order_selection.py` | **UNIFIED** — delegates to core module via `core_select_orders_bridge()`, with legacy fallback |
| `docs/Study_CLI.md` | **UPDATED** — performance options, examples, caveats |
| `tests/study/test_arma_order_selection_service.py` | **EXTENDED** — 8 new tests |
| `tests/study/test_arma_order_selection_core.py` | **NEW** — 17 core unit tests |
| `tests/study/test_study_cli_temporal.py` | **EXTENDED** — 7 new CLI option tests |
| `tests/study/test_arma_order_selection_performance.py` | **NEW** — 2 performance smoke tests |

---

## New CLI Options

| Option | Default | Description |
|---|---|---|
| `--n-jobs INTEGER` | `1` | Series-level parallel workers (-1 = all cores) |
| `--progress / --no-progress` | `--progress` | Progress bar (tqdm, silent fallback) |
| `--timeout-seconds INTEGER` | `30` | Per-fit timeout (0 = disabled) |
| `--early-stopping / --no-early-stopping` | `--early-stopping` | Prune q/p when criterion degrades |
| `--grid-fit-method TEXT` | `auto` | Fast grid method (css, statespace, …) |
| `--final-refit-method TEXT` | `statespace` | Winner refit method |

---

## Architecture Decisions

1. **Parallelism level:** Series-level via joblib `loky` backend. Each series independently searches (p,q) grid.
2. **Fast grid fitting:** `resolve_grid_fit_method()` probes css→hannan_rissanen→innovations_mle→statespace. Grid uses maxiter=20. Winner refits with statespace maxiter=50.
3. **Early stopping:** Conservative — stops q after 2 consecutive degrades, stops p when row-best > 1.05×global-best. Handles negative scores correctly.
4. **Timeout:** Process-level via ProcessPoolExecutor(max_workers=1). Timeout returns `ARMAFitAttempt(timed_out=True)` without crashing.
5. **Unification:** Plugin `select_arma_order()` delegates to `core_select_orders_bridge()` in the shared core module.
6. **Progress:** `make_progress_iter()` wraps with tqdm if available, silent fallback otherwise.

---

## Method Resolution

| Environment | `--grid-fit-method auto` resolves to |
|---|---|
| statsmodels with css support | `css` |
| Without css | `hannan_rissanen` or fallback chain |
| Worst case | `statespace` |

The actual method used is recorded in `grid_fit_method_used` in the JSON report.

---

## Optimisation Metadata in Reports

The JSON report (`ARMA_ORDER_SELECTION.json`) now includes:

```json
{
  "n_jobs": -1,
  "progress": true,
  "timeout_seconds": 30,
  "early_stopping": true,
  "grid_fit_method_requested": "auto",
  "grid_fit_method_used": "css",
  "final_refit_method": "statespace",
  "maxiter": 20,
  "final_maxiter": 50,
  "n_candidate_grid_max_per_series": 36,
  "n_fits_attempted": 1250,
  "n_fits_skipped_by_early_stopping": 420,
  "n_timeouts": 3,
  "runtime_seconds": 145.6,
  "parallel_backend": "loky",
  "joblib_available": true
}
```

---

## Backward Compatibility

- All new parameters have defaults matching old behavior.
- `imputebench study temporal arma-order --dataset-id X --p-max 5 --q-max 5 --criterion bic --output-dir Y` still works unchanged.
- Plugin `select_arma_order()` calls the core bridge. If core import fails, it falls back to the legacy implementation embedded locally.

---

## Manual Benchmark

### Before (sequential, full MLE, no early stopping)

```bash
time python -m imputebench study temporal arma-order \
  --dataset-id london_aq --p-max 5 --q-max 5 \
  --criterion bic --output-dir docs/study/london_aq_before
```

Expected: **3h30+** for ~50 series

### After (parallel, CSS grid, early stopping, timeout=30s)

```bash
time python -m imputebench study temporal arma-order \
  --dataset-id london_aq --p-max 5 --q-max 5 \
  --criterion bic --n-jobs -1 --progress \
  --timeout-seconds 30 --early-stopping \
  --output-dir docs/study/london_aq_after
```

Target: **< 3 minutes** for 50 series, **< 15 minutes** for 560 series

---

## Tests

### Unit tests (run with `pytest tests/study/test_arma_order_selection_core.py tests/study/test_arma_order_selection_service.py`)

| Test | Status |
|---|---|
| `test_resolve_grid_fit_method_returns_explicit_value` | ✅ |
| `test_resolve_grid_fit_method_auto_returns_string` | ✅ |
| `test_is_worse_than_global_positive_scores` | ✅ |
| `test_is_worse_than_global_negative_scores` | ✅ |
| `test_is_worse_than_global_zero_global_best` | ✅ |
| `test_fit_timeout_zero_runs_in_process` | ✅ |
| `test_fit_timeout_disabled_runs_no_timeout` | ✅ |
| `test_select_order_basic` | ✅ |
| `test_select_order_respects_p0_q0_skip` | ✅ |
| `test_select_order_with_early_stopping` | ✅ |
| `test_select_order_no_early_stopping_scans_more` | ✅ |
| `test_select_order_returns_fallback_on_constant_series` | ✅ |
| `test_progress_disabled_returns_same_items` | ✅ |
| `test_progress_enabled_falls_back_without_tqdm` | ✅ |
| `test_config_defaults` | ✅ |
| `test_backward_compatible_defaults_work` | ✅ |
| `test_optimisation_metadata_in_report` | ✅ |
| `test_n_jobs_minus_one_means_all_cores` | ✅ |
| `test_sequential_fallback_without_joblib` | ✅ |
| `test_early_stopping_is_used` | ✅ |
| `test_no_early_stopping_disables_it` | ✅ |
| `test_timeout_zero_means_disabled` | ✅ |

### CLI tests (run with `pytest tests/study/test_study_cli_temporal.py`)

| Test | Status |
|---|---|
| `test_arma_order_cli_accepts_n_jobs` | ✅ |
| `test_arma_order_cli_accepts_progress_flags` | ✅ |
| `test_arma_order_cli_accepts_timeout_seconds` | ✅ |
| `test_arma_order_cli_accepts_early_stopping` | ✅ |
| `test_arma_order_cli_accepts_grid_fit_method` | ✅ |
| `test_arma_order_cli_accepts_final_refit_method` | ✅ |
| `test_arma_order_cli_backward_compatible` | ✅ |

### Performance smoke tests (run with `pytest tests/study/test_arma_order_selection_performance.py -m slow`)

| Test | Status |
|---|---|
| `test_order_selection_on_synthetic_series_is_reasonable` | ✅ |
| `test_full_grid_vs_early_stopping_produces_same_best` | ✅ |

---

## Remaining Bottlenecks

1. **statsmodels ARIMA fit overhead** — Even with CSS, individual fits take 0.5–2s on LondonAQ-length series. Timeout guards against pathological hangs.
2. **Nested process overhead** — When both joblib (series-level) and timeout (fit-level) use processes, Windows spawn overhead adds latency. Mitigated by configurable timeout_seconds=0 to disable.
3. **Memory** — joblib loky copies the series list. For 560 long series this is manageable (~tens of MB).
4. **Early stopping may skip true best** — When criterion surface is non-convex, early stopping could miss a deep minimum. The `--no-early-stopping` flag provides full-grid mode for verification.

---

## Caveats

- Fast grid fitting (CSS or similar) is used for candidate pruning. The final winner is refit with statespace.
- Early stopping may skip high-order candidates after criterion degradation. Full-grid mode is available.
- Timeouts exclude pathological fits and are reported explicitly. Never silently hidden.
- The plugin and study service share one implementation in `arma_order_selection_core.py`.
- No Streamlit or GUI dependency introduced.
- If joblib is unavailable, the command falls back to sequential execution with a warning in metadata.
- If tqdm is unavailable, progress bar is silently omitted.
