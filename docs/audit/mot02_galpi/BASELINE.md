# MOT02-00 — GALPI Baseline

Captured before implementing the GALPI plugin (spec
`Interfaces/EviBench/specs/GALPI_ADAPTIVE_LOCAL_POLYNOMIAL_EviBench_ROBUST.md`).

## HEAD

- Repo HEAD SHA at baseline: `d17cb8659ea77c058c1336825c24b9ed0d89a51b`
- Branch: `master`

## Environment

- `numpy` version: `2.2.6`
- `RankWarning` location on this numpy: `numpy.exceptions.RankWarning`
  (numpy>=2.0 moved it off the top-level namespace; the plugin imports it with a
  fallback shim).

## Plugin inventory (pre-existing, relevant subset)

`arma`, `backward_fill`, `dacpi`, `exponential_smoothing`, `linear_interpolation`,
`locf`, `mean`, `median`, `moving_average`, `nearest_interpolation`, `seasonal_naive`,
plus DL/ST plugins. No `galpi` bundle existed at baseline. (`cubic_spline` is the
sibling MOT 01 plugin being implemented in parallel; out of scope for MOT 02.)

## Candidate methods in the builtin classical recipe

From `imputebench/services/experiment_helpers/builtin_recipes.py` (`_classical_recipe`):

```
["Mean", "Median", "LOCF", "LinearInterpolation", "NearestInterpolation"]
+ (["ARMA"] if _ARMA_AVAILABLE else [])
```

`DACPI` — an already-implemented **experimental** classical plugin by the same author —
is deliberately **not** present in this recipe. This sets the repo convention: an
experimental plugin stays out of the official builtin recipe until it reaches benchmark
readiness. GALPI follows that convention (see the implementation report, §Recipe decision).

## LinearInterpolation behavior (reference peer)

`imputebench.algorithms.naive.linear_interpolation.LinearInterpolationImputer` flattens
time-major, fills via `pandas.DataFrame.interpolate(method="linear",
limit_direction="both")` — i.e. it interpolates over **NaN** positions and forward/backward
fills the edges. It does not consult the mask. GALPI, by contrast, derives the hidden
support from the **mask** and uses an explicit linear-anchor formula for short gaps, so the
short-gap test compares GALPI against the boundary-anchor linear formula rather than against
this class directly (spec §13.6).

## Reference contracts used

- `imputebench.algorithms.base.ImputationOutput` (`X_imputed`, `runtime_seconds`,
  `metadata: dict[str, float|int|str|bool]`).
- `imputebench.algorithms.utils.flatten_time_major` / `restore_time_major`.
- `imputebench.services.plugin_loader_service.PluginLoaderService` (validate/load/register/
  discover; strict manifest contract via `validate_manifest_contract`).
- `imputebench.domain.algorithms.plugin_manifest.build_manifest_validation` (strict mode
  requires the six contract fields, all present in the GALPI manifest).
