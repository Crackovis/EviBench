# SISYPHUS Sheet 17 — Implementation Report

**Station-View Runtime Binding, Classical Benchmark Closure, and Cross-Station Aggregation**

- Baseline HEAD at start: `287486b0c30e5169fea86550b9b71c9ab165a410` ("Sync")
- Workdir: `Interfaces/EviBench/`
- Schema version: `11 → 12` (additive station-view migration `_migrate_to_12`)
- Scientific principle enforced: *a station is a distinct experimental support — never lost in
  orchestration, never disguised as a full grid, never fused without an explicit aggregation policy.*

---

## 1. Root causes and where they were fixed

| Gap (audit) | Root cause | Fix |
|---|---|---|
| Station view lost before the provider | `ExecutionOrchestrator.execute_plan()` loaded the dataset with no `DatasetViewSpec`; the temporal dispatcher never copied the task's station identity onto the run | `_apply_view_identity()` in `temporal_execution_dispatcher.py`; per-run view resolution + per-view cache in `execution_orchestrator.py` |
| DACPI received full grid | view never reached the provider | provider now loaded with the resolved `DatasetViewSpec`; DACPI receives `(T,1,1,P)` |
| Full source shape lost | `DatasetRuntimeProvider.load()` set `full_shape = station shape` | `full_shape` taken from `DatasetView.full_shape` (`(T,R,C,P)`) |
| Benchmark mask shape mismatch | `_materialize_benchmark` compared full-grid mask against the station shape | validate against `full_shape`, then project `mask_full[:, row:row+1, col:col+1, :]` |
| Result re-expansion risk | none enforced | station output asserted `(T_eval,1,1,P)`; re-expansion raises |
| Result missing view identity | `build_classical_result` did not receive view fields | view identity threaded through `classical_task_executor → execute_classical_task → build_classical_result` |
| ARMA unbounded | `p_max=q_max=5`, no per-series/global budget, unbounded refit | `official_bounded` profile, `max_order` grid bound, per-candidate/per-series/global budgets, bounded `fit_arima` |
| Recipe referenced `KNN` | builtin recipe listed an unregistered method | `KNN → NearestInterpolation` in `builtin_recipes.py` |
| Guidance hardcoded DL | contract guidance always `scientific_dl / Deep Learning` | `_resolve_recipe_guidance_class()` derives the class from the recipe |
| ComparisonUnit lacked station | no view identity carried | `ComparisonUnit` + `normalize_result_to_unit` carry view identity and tags |

The provider remains the **single dataset slicing authority** (Sheet 17 §1.1, §19): the classical
executor performs assertions only — **no local slicing was added**.

---

## 2. Canonical view propagation (SVR-01)

```
CLI request → TemporalExperimentTask (station identity)
            → ExperimentRun (copied by execute_official_classical / execute_classical_direct)
            → DatasetViewRuntimeResolver.resolve(run) → DatasetViewSpec
            → DatasetRuntimeProvider.load(view=...) → DatasetRuntimeContext (T,1,1,P)
            → MaskRuntimeMaterializer (full-grid mask projected to station)
            → Result (station identity + effective support fingerprint)
            → ComparisonUnit (station identity + view tags)
```

Coherence guard `ExecutionOrchestrator._assert_view_coherence` blocks any divergence between the
runtime view and the run's persisted view (`SVR-004`). Selector validation emits `SVR-001..005`.

## 3. Shape & full-shape (SVR-02)

`DatasetRuntimeContext.full_shape` is always the full source tensor `(T,R,C,P)` even when `shape` is
`(T,1,1,P)`. Verified by `tests/test_sheet17_station_runtime.py::test_station_context_preserves_full_shape`.

## 4. Mask projection (SVR-03)

The official mask bank stays full-grid; the runtime validates `mask_full.shape == full_shape`,
projects to `(T,1,1,P)`, slices the temporal support, and records
`effective_support_fingerprint = SHA256(source_support + view_fp + slice_start + slice_end + realization)`.
Two stations therefore never share strict benchmark parity (§5.5). The same full-grid bank is reused
for every station — no per-station bank is materialized.

## 5. Station-scoped persistence (SVR-04, §7)

`runs` and `results` gained `dataset_snapshot_fingerprint, dataset_view_mode, dataset_view_fingerprint,
station_id, station_grid_row, station_grid_col` (schema v12). Migration `_migrate_to_12` is additive,
backfills from `payload_json`, and never invents a station. Indexes enable SQL-first station selection.

## 6. ARMA bounded budget (ARMA-BUDGET-01, §8)

Profiles `scientific_full` (unbounded reference) and `official_bounded` (`p,q∈{0,1,2}`, `p+q≤4`,
per-candidate / per-series / global timeouts, bounded final refit) are defined in `plugins/arma/algorithm.py`.
Budget diagnostics (`search_profile, candidate_count, fit_attempt_count, timeout_count,
series_budget_exhausted_count, global_budget_exhausted, fit_elapsed_seconds, fallback_count,
selected_orders`) are emitted. A timeout produces a declared fallback, never a fit success.

- Benchmark: `scripts/benchmark_arma_station.py` → `docs/audit/sheet17/ARMA_PERFORMANCE_REPORT.json`
- Tests: `benchmarks/arma/test_arma_station_budget.py` (injectable clock + fake workers; one `slow` real fit)

## 7. Recipe & guidance (RECIPE-METHOD-01, PREP-GUIDANCE-01)

- `KNN` references: **0**; `NearestInterpolation` references: **9** (one per classical recipe).
- `validate_candidate_methods` resolves by canonical id or display name; an unresolved mandatory method
  is `RECIPE-METHOD-001`, an absent optional dependency (ARMA) is a warning.
- Contract guidance class is derived from the recipe (`classical` for the classical book), not hardcoded DL.
- `PlanEligibilityDiagnostic` + `PREP-001..012` provide structured blockers (`read_models/plan_eligibility.py`).

> Operational note: the canonical builtin recipe revision must be re-seeded into the metadata registry
> (`imputebench admin migrate recipe-books --seed-builtins --apply --verify`) so the persisted book
> reflects the `NearestInterpolation` source fix; the source-of-truth and the legacy builders are already corrected.

## 8. Tier B & cardinality (§10, §13)

- Tier A (core, rates 0.10/0.30): **6 recipes** → 24 tasks/station → 120 results/station → **72 tasks / 360 results** over three stations.
- Tier B (stress, rate 0.50): **3 recipes**, seeds MCAR=1729 / MAR=2729 / MNAR=3729, 5 realizations each → 12 tasks/station → 60 results/station.

Verified by `tests/test_sheet17_station_runtime.py::test_tier_cardinalities_match_spec`.

## 9. Cross-station aggregation (XST-01, §12)

`StationAggregationService` builds a `CrossStationAggregate` (macro mean/median MAE & RMSE, win count
with tie tolerance, rank mean/std, coverage matrix) and an SSE-weighted pooled RMSE guarded by
evaluated-point counts. Missing policies: `intersection` (default), `block`, `descriptive-union`
(ranking not reportable). The CLI `imputebench results compare merge-stations` persists a
`cross_station_aggregate` `ComparisonSpec` — **zero new `Result` rows** — with explicit claim boundary.

## 10. Tests

New suites (all green):

- `tests/test_sheet17_station_runtime.py` — 17 tests (resolver, full_shape, mask projection, coherence, result identity, no re-expansion, cardinality)
- `tests/test_sheet17_cross_station.py` — 10 tests (macro, pooled guard, ties, policies, provenance, CLI zero-Result)
- `tests/test_sheet17_guidance_and_persistence.py` — 10 tests (KNN absence, candidate validation, class-correct guidance, schema columns, migration backfill)
- `benchmarks/arma/test_arma_station_budget.py` — 7 fast + 1 `slow`

Pre-existing, unrelated failures (present on baseline `287486b0`, not introduced here): the
`algorithm_card_service` / `algorithm_execution_truth_service` collection errors, `test_phase15b_wizard`,
and `test_official_preparation_dl_lifecycle_propagation::...[0.1-1]`.

## 11. Constraints honored

- No local slicing in the classical executor (assertions only).
- No `KMP_DUPLICATE_LIB_OK` mutation by code, tests, or gates.
- No Streamlit import in the new services.
- No physical fusion of `Result` rows; no pooled RMSE without SSE + counts.
