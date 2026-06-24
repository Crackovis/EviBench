# MOT01 CubicSpline Baseline

Captured for `CUBIC_SPLINE_INTERPOLATION_EviBench_ROBUST.md`.

## Repository

- HEAD SHA: `d17cb8659ea77c058c1336825c24b9ed0d89a51b`
- Implementation root: `Interfaces/EviBench`
- Local policy: no push, no official recipe mutation in this patch

## Runtime

- Python: `3.13.9 | packaged by Anaconda, Inc.`
- SciPy availability: available
- SciPy version: `1.16.3`
- SciPy scope: plugin dependency only; core dependencies were not expanded

## Plugin Inventory

Initial audit observed these plugin bundles before adding `cubic_spline`:

`arma`, `backward_fill`, `brits`, `dacpi`, `dcrnn`, `exponential_smoothing`,
`grin`, `gru`, `gru_d`, `ignnk`, `linear_interpolation`, `locf`, `lstm`,
`mean`, `median`, `moving_average`, `nearest_interpolation`,
`phase10_smoke_plugin`, `rnn`, `saits`, `saits_lc`, `saits_lch`,
`seasonal_naive`, `st_smoke`, `stgcn`.

During verification, another parallel implementation was visible as `galpi`.
This MOT01 patch did not read or modify that plugin.

## LinearInterpolation Behavior

`plugins/linear_interpolation/algorithm.py` wraps
`imputebench.algorithms.naive.linear_interpolation.LinearInterpolationImputer`.
The built-in imputer flattens time-major tensors, runs pandas linear
interpolation on the numeric array, restores the original shape, and returns
`ImputationOutput(metadata={"method": "linear"})`.

MOT01 does not modify LinearInterpolation.

## Existing Classical Recipe Candidates

The active builtin recipe source is:

`imputebench/services/experiment_helpers/builtin_recipes.py`

The classical candidate tuple observed during audit is:

`Mean`, `Median`, `LOCF`, `LinearInterpolation`, `NearestInterpolation`,
plus `ARMA` when available.

No YAML recipe was patched. `CubicSpline` was not added to the official recipe
book in this patch because the fiche marks that integration as optional after
plugin validation and smoke gates, and the recipe file is a likely shared edit
surface for the parallel implementation.

## Baseline Gates

- `python -m imputebench methods plugin validate cubic_spline`: pass after implementation
- `python -m imputebench methods plugin show cubic_spline --strict-validation`: pass after implementation
- `pytest tests/plugins/cubic_spline -q`: pass after implementation
