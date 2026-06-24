# Sheet 14 — DACPI Baseline Capture (patch 14-00)

Audit freeze captured **before** any DACPI implementation, per Sheet 14 §26 (14-00).
This records the regression oracle state so that DACPI introduction can be proven
non-destructive to the existing benchmark reference (`LinearInterpolation`).

## Repository ref

| Field | Value |
|---|---|
| Local `HEAD` at capture | `61b9a428d12f20bda016473a123a8d6cac043617` |
| Sheet audited ref | `e302b704fcc6f2786019509ab4cd6a2cdcffe164` |
| Branch | `main` |

## Environment

| Tool | Version |
|---|---|
| Python | 3.10.15 |
| NumPy | 2.2.6 |
| pytest | 9.0.2 |

> Note: `pyproject.toml` pins `numpy>=1.24,<2.0`, but the live interpreter resolves
> NumPy `2.2.6`. DACPI is written to the NumPy 2.x API surface available at runtime
> and uses only stable, version-agnostic primitives (`np.isfinite`, `np.corrcoef`,
> `np.nanmedian` is **not** used — see leakage notes). No new dependency is added.

## Current LinearInterpolation manifest (`plugins/linear_interpolation/imputebench_plugin.json`)

```json
{
  "slug": "linear_interpolation",
  "name": "LinearInterpolation",
  "family": "naive",
  "version": "1.0.0",
  "description": "Linear interpolation: fill missing values using linear interpolation between observed points.",
  "entry_class": "LinearInterpolationPlugin",
  "default_config": {},
  "dependencies": [],
  "tags": ["temporal", "interpolation", "baseline"],
  "imputebench_api": "1"
}
```

The built-in `LinearInterpolationImputer`
(`imputebench/algorithms/naive/linear_interpolation.py`) flattens non-temporal axes,
runs Pandas `interpolate(method="linear", limit_direction="both")`, and returns a
same-shape `ImputationOutput`. **DACPI does not modify this file or its plugin
wrapper.** It is the benchmark reference and regression oracle (§1.6).

## Current LinearInterpolation get_info

```python
{"name": "LinearInterpolation", "family": "naive"}
```

The plugin wrapper (`plugins/linear_interpolation/algorithm.py`) returns a richer
dict (family `naive`, `runtime_modes=["classical"]`, `scientific_ready=False`).

## `plugin validate linear_interpolation` output (baseline)

```json
{
  "valid": true,
  "errors": [],
  "warnings": [
    "Optional file missing: requirements.txt",
    "[contract] Legacy manifest detected: contract fields inferred",
    "[contract] runtime_modes inferred from family/default_config"
  ]
}
```

LinearInterpolation has no contract fields (`execution_lifecycle`, `runtime_modes`,
`readiness`, …) → reported `null` by `plugin show`. DACPI, by contrast, declares the
full v2 contract explicitly (§1.5, §19).

## Classical contract test status (baseline)

```text
pytest tests/test_cl2_classical_execution_contract.py -q
16 passed in 7.14s
```

## Temporal benchmark identity fields (baseline)

```text
pytest tests/test_a3_benchmark_identity.py -q
5 passed in 0.37s
```

Benchmark identity is governed by the tuple recorded in the classical runner
(`imputebench/services/execution/runner/classical_execution.py`):
`benchmark_contract_id`, `benchmark_mask_bank_id`, `benchmark_realization_id`,
`evaluation_support_fingerprint`. Metric computation
(`metrics_bridge.compute_metrics`) evaluates **only** at `mask == True` positions
(`diff = x_imputed[mask] - x_true[mask]`), confirming the runtime
`one_means_hidden` / `nonzero == evaluated` convention that DACPI adopts.

## Runtime semantics confirmed during audit

- Canonical evaluation tensor rank is **4**: `metrics_bridge.compute_metrics` unpacks
  `_, rows, cols, pollutants = x_true.shape`. Station-scoped temporal data is
  `(T, 1, 1, P)`.
- Mask convention: `mask` nonzero / `True` = **hidden / evaluated**
  (`classical_execution.py`: `mask_count = np.count_nonzero(mask.astype(bool))`,
  `"evaluated_points": mask_count`).
- Classical runner calls `algorithm.fit(x_masked, mask, config=...)` then
  `algorithm.impute(x_masked, mask)`. Plugins receive the *masked* tensor; hidden
  slots may still contain ground truth or NaN, so DACPI guards **all** reads by the
  observed mask (§14, §27.3).
- `PluginLoaderService._load_module` inserts the plugin directory into `sys.path`,
  so plugin-local absolute imports (`from components import …`) resolve under
  isolated loading (§16.10).

## Patch order (Sheet 14 §26)

14-00 audit freeze (this file) → 14-01 primitives → 14-02 precompute → 14-03
components → 14-04 entry class → 14-05 bundle → 14-06 integration → 14-07 regression.

Do not implement DACPI before this baseline capture. — Captured at 14-00.
