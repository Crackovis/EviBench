# Sheet 14 — DACPI Implementation Report

DACPI (Diurnal-Aware Cross-Pollutant Interpolation) implemented as a deterministic,
observed-support-only EviBench plugin per `specs/SISYPHUS_IMPLEMENTATION_SHEET_14_DACPI_COMPLETE.md`.

The completeness chain targeted by this work:

```
the code matches the formal algorithm        -> §4–§13 implemented verbatim
the formal algorithm matches the tests       -> §21 manual cases + §22 plan covered
the tests match EviBench runtime semantics    -> rank-4 (T,1,1,P), 1==hidden, observed-only metrics
the runtime output preserves benchmark support-> observed values bit-for-bit, hidden-only writes
the benchmark evidence bounds every claim     -> readiness=experimental, no perf claim in code/docs
```

## Repository ref

| Field | Value |
|---|---|
| Local `HEAD` at start | `61b9a428d12f20bda016473a123a8d6cac043617` |
| Sheet audited ref | `e302b704fcc6f2786019509ab4cd6a2cdcffe164` |
| Branch | `main` |
| Python / NumPy / pytest | 3.10.15 / 2.2.6 / 9.0.2 |

## Files created

Plugin bundle (`plugins/dacpi/`):

```
__init__.py              errors.py          state.py        components.py
algorithm.py (DACPIImputer entry class)     gaps.py         weights.py
precompute.py            imputebench_plugin.json
requirements.txt (numpy) README.md
```

Tests (`tests/plugins/dacpi/`):

```
__init__.py  helpers.py
test_dacpi_weights.py              test_dacpi_gaps.py
test_dacpi_precompute.py           test_dacpi_components.py
test_dacpi_algorithm_contract.py   test_dacpi_edge_cases.py
test_dacpi_leakage.py              test_dacpi_determinism.py
test_dacpi_manifest.py             test_dacpi_plugin_loader.py
test_dacpi_temporal_integration.py
```

Audit / evidence (`docs/audit/sheet14/`):

```
BASELINE.md              ABLATION_PROFILES.json    IMPLEMENTATION_REPORT.md (this file)
```

## Files modified

None. The benchmark reference `LinearInterpolation` (built-in and plugin wrapper) is
untouched, as required (§1.6). No existing source, test, or manifest was edited.

## Architecture notes

- **Pure helper modules, thin orchestrator.** Math lives in `gaps.py`, `weights.py`,
  `precompute.py`, `components.py`, `state.py`; `algorithm.py` owns only shape
  normalization, config orchestration, timing, observed-value restoration, and scalar
  metadata aggregation (§16.2).
- **Leakage boundary.** Every test-time read is guarded by the observed-&-finite
  context mask; predictions are written to a separate buffer and never re-read as
  context, so early imputations cannot contaminate later ones (§14, §27.3–§27.4). Fit
  statistics use observed training support only; the `fit_fingerprint` is a SHA-256
  over the canonical fitted state and is invariant to hidden-slot contents.
- **Scale-normalized cross transfer** is default-on; the affine-invariance property
  test confirms `y = a·x + b` transforms target predictions consistently (§23).
- **Transactional fit.** State is assigned only after `build_dacpi_state` succeeds; a
  failing config/fit leaves any prior fitted state unchanged (§17.2).
- **Plugin-local absolute imports** (`from components import …`) so the isolated
  `PluginLoaderService` loader resolves them with the plugin dir on `sys.path`
  (§16.10). Module names (`components`, `gaps`, …) are unique across all plugins.

## Test commands and counts

```
pytest tests/plugins/dacpi -q
138 passed in ~4.0s
```

Per file:

| File | Passed |
|---|---:|
| test_dacpi_weights.py | 27 |
| test_dacpi_gaps.py | 14 |
| test_dacpi_precompute.py | 15 |
| test_dacpi_components.py | 24 |
| test_dacpi_algorithm_contract.py | 17 |
| test_dacpi_edge_cases.py | 13 |
| test_dacpi_leakage.py | 4 |
| test_dacpi_determinism.py | 5 |
| test_dacpi_manifest.py | 9 |
| test_dacpi_plugin_loader.py | 6 |
| test_dacpi_temporal_integration.py | 4 |
| **Total** | **138** |

Manual verification cases A–F (§21) are covered end-to-end (A, E, F in
`test_dacpi_edge_cases.py`; B, C at component level in `test_dacpi_components.py`;
D periodic in `test_dacpi_edge_cases.py`).

### Regression commands

```
pytest tests/test_cl2_classical_execution_contract.py -q     16 passed   (unchanged from baseline)
pytest tests/test_a3_benchmark_identity.py -q                 5 passed    (unchanged from baseline)
pytest tests/plugins/test_plugin_manifest_validation.py
      tests/plugins/test_plugin_scaffold_manifest_v2.py
      tests/temporal -q                                       116 passed
pytest tests/test_cl1_classical_recipe_book.py
      tests/test_classical_benchmark_test_support_parity.py -q  passed
```

DACPI introduces no modification to expected LinearInterpolation outputs (§22.11).

## Known skipped / pre-existing failing tests (NOT caused by DACPI)

1. `tests/plugins/arma/test_arma_plugin_contract.py::test_arma_preserves_observed_values`
   — **pre-existing**. Fails in isolation with no DACPI on the path; the ARMA test
   builds its mask with `True==observed` while the ARMA imputer treats `True==hidden`.
   Surfaces only because `statsmodels` is installed. No ARMA file was touched by this
   work (`git status` shows no ARMA changes).
2. `tests/test_syntax_all_modules.py::test_all_modules_compile` — **pre-existing,
   environment-dependent**. This whole-repo import-resolution test already fails at
   baseline here because the optional `xarray` (`data-io` extra) is not installed
   (`imputebench/services/data_contract/adapters/netcdf_adapter.py` imports it at top
   level) and because plugin-local absolute imports (`arma_backend`, `_st_common`, …)
   only resolve once a plugin dir is on `sys.path` during full collection. DACPI
   follows the identical established ARMA/ST pattern; when DACPI tests are collected,
   all DACPI module references resolve and zero `dacpi` entries remain. DACPI does not
   change this test's verdict in this environment.

## Plugin validate output

```
$ imputebench plugin validate dacpi
{ "valid": true, "errors": [], "warnings": [] }
```

## Strict manifest output

```
$ imputebench plugin show dacpi --strict-validation --format json   (excerpt)
validation: { valid: true, strict: true, errors: [], warnings: [] }
contract:   { execution_lifecycle: single_shot, runtime_modes: [fit_impute],
              readiness: experimental, benchmark_ready: false,
              comparison_ready: false, scientific_ready: false,
              supports_checkpoint: false, supports_training_history: false,
              supports_prediction_artifact: true, required_artifacts: [] }
```

`build_manifest_validation(manifest, strict=True)` returns `valid=True, errors=()`
(asserted in `test_dacpi_manifest.py::test_strict_manifest_contract_passes`).

## Benchmark recipe identity / mask-bank identity

No benchmark was executed (Sheet 14 §28: "RMSE improvement required for code
acceptance: no"). The ablation **profiles** are persisted as explicit configurations
in `docs/audit/sheet14/ABLATION_PROFILES.json` (DACPI-L, DACPI-LP, DACPI-LC,
DACPI-FULL, DACPI-NOCLIP), realized through `enabled_components` and `bounds_policy`
— benchmark profiles, not alternate plugin code paths (§25.3). Each profile is
verified to validate against the config contract. Patches 14-08 (controlled benchmark
recipe over a shared mask bank) and 14-09 (evidence export: global / per-pollutant /
gap-bucket metrics, availability, clipping, fallback, same-support audit) are the
separate, evidence-gated phase and remain pending; no README claim is promoted before
those artifacts exist.

## Same-support audit result

Not yet run (requires 14-08/14-09 execution). The same-support invariants that MUST
hold before any comparative ranking are enumerated in `ABLATION_PROFILES.json`
(`same_support_invariants`) and Sheet 14 §25.1. The runtime metric boundary
(`metrics_bridge.compute_metrics`) was confirmed to evaluate only `mask==True`
positions, so same-support comparison is well-defined once a shared mask bank binds
all baselines and profiles.

## Readiness retained or promoted

**Retained: `experimental`.** `benchmark_ready=false`, `comparison_ready=false`,
`scientific_ready=false`. Per §26 (14-10), passing unit tests authorizes at most
`smoke_ready`; promotion is a separate decision and is not performed by this
implementation.

## Remaining caveats

- Default `periodic_lag_steps=60` is an **hourly proxy** at one-minute resolution, not
  a full 24-hour diurnal cycle.
- The periodic and cross mechanisms are **hypotheses**; their value requires
  same-support, gap-stratified, ablated benchmark evidence (14-08/14-09).
- Empirical-bound clipping can suppress legitimate unseen extremes (`DACPI-NOCLIP`
  ablation provided).
- DACPI v1 is station-scoped temporal only: rejects full-grid `R≠1`/`C≠1`, no graph
  adjacency, no forecasting beyond the observed window.
- No novelty, superiority, state-of-the-art, or deployment-readiness claim appears in
  code or docs (§2.2, §25.6).
