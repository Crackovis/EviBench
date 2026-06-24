# SISYPHUS Sheet 12 — Closure Report

`prepare_all_safe` dataset loading and benchmark binding repair. All six phases
(12-00 … 12-06) implemented and verified on isolated SQLite databases.

## Root cause (confirmed at audited HEAD)

The temporal CLI built `TemporalPreparationService` with an **incomplete**
`OfficialPreparationAutomationService`: `dataset_service`, preparation board,
action service, guided-plan service, artifact catalog and the full
`ExperimentHelperService` were all absent. `_load_dataset_array` therefore
returned `None` before `DatasetService.load_array()` could run, and every
board-derived step then failed with a repeated `Could not build preparation
board.` cascade. The LondonAQ `.dat` reader was never the cause.

## Changes

### 12-01 Shared dataset loading + diagnostics
- `OfficialPreparationAutomationService` gains `dataset_runtime_provider`; a
  `DatasetRuntimeProvider` is auto-built from a real `DatasetService`.
- `_load_dataset_array` now loads through the canonical authority
  (runtime provider → `DatasetService.load_array` → narrow `.data` legacy
  fallback) and raises a structured `DatasetPreparationLoadError`
  (dataset id, resolved id, resolved path, missing pollutant files, cause class)
  instead of the generic `Could not load dataset`.
- Loaded tensor validated once per `create_missing_mask_banks` call.

### 12-02 Canonical preparation composition
- New `imputebench/services/official_preparation_composition.py` with
  `build_official_preparation_services(*, graph=None)` — one UI-free composition
  authority (no Streamlit / no `app` import). Builds datasets + runtime provider,
  mask banks, contracts, guided plans, artifact catalog, the full
  `ExperimentHelperService`, board, action service and the automation.
- `TemporalPreparationService` and `build_temporal_services()` now use it; the
  orchestrator shares the same preparation and dataset runtime authority.
- `app/service_composition/factory.py` delegates both Run Plans and legacy
  bundles to the builder.

### 12-03 Preparation control flow + binding map
- `prepare_all_safe` is dependency-aware and fails fast: a mask-bank failure
  short-circuits (one root error, `failed_step="mask_banks"`, downstream steps
  explicitly skipped); a contract failure blocks plans/comparison shells; a
  board-construction failure is reported once, never as a 3× cascade.
- Final resolver refresh, then a recipe-keyed `PreparedBenchmarkBinding` map is
  emitted (contract, mask bank, realization IDs, eligibility fingerprint, phase
  scopes), built from the persisted `BenchmarkContract` — never from names.
- `build_automation_plan` resolves the dataset alias to the canonical id once.
- Materialize status is `ready` only when every selected recipe has an exact
  binding.

### 12-04 Execution binding propagation
- `TemporalExperimentOrchestrator` blocks official execution when preparation is
  not ready / has errors — never downgrades to ad-hoc masks. Each task receives
  its `PreparedBenchmarkBinding` by `recipe_id`; an official task with no binding
  is blocked, not run.
- Official runs are built at (recipe, algorithm) granularity; one run carries the
  full contract and the runner emits one result per realization.
- `TemporalExecutionDispatcher.execute_official_classical` creates the run,
  attaches benchmark identity + recipe lineage (`masking_authority =
  "benchmark_contract"`), then delegates to `ExperimentRunner.execute`, which
  loads the exact persisted mask realizations. No `Temporal-*` ad-hoc scenario is
  registered for official tasks. The diagnostic ad-hoc path is preserved.

### 12-05 Ledger and resume safety
- Temporal execution identity folds the contract / mask bank / eligibility
  fingerprint into the key for official tasks, so a prior exploratory completion
  is never reused as an official completion under `--skip-completed`.

## Verification (isolated SQLite, temporary LondonAQ dataset)

New tests (all green):
- `tests/temporal/test_temporal_preparation_composition.py` — §7.1
- `tests/temporal/test_prepare_dataset_loader_parity.py` — §7.2/§7.3/§7.4
- `tests/temporal/test_prepare_clean_db_materialization.py` — §7.5/§7.6
  (0→6 banks, 0→6 contracts, 6 bindings, idempotent rerun = 0 new)
- `tests/temporal/test_temporal_official_execution_binding.py` — §7.7/§7.8/§7.11
  (run + result benchmark identity, exact bank mask, block-without-binding,
  diagnostic regression)
- `tests/temporal/test_temporal_execution_identity.py` — §8.5 ledger identity

Regression: full `tests/temporal/` (108) and `tests/results_interaction/` (365)
green; P9.5 automation + phase20/21 green except 4 failures that are identical on
clean HEAD (catalog `index_mask_bank` preference + resolver artifact-health —
unrelated to Sheet 12). Pre-existing unrelated failures elsewhere
(`tests/cli/test_run_crud.py` JSON-decode, `tests/test_s1_*`, masking-protocol
loop schedule) verified to fail identically on clean HEAD.

The live `data/metadata.db` was left untouched (0 contracts / 0 banks).
