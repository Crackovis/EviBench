# MOT02-09 — GALPI Implementation Report

Implementation of the robust GALPI contract from
`Interfaces/EviBench/specs/GALPI_ADAPTIVE_LOCAL_POLYNOMIAL_EviBench_ROBUST.md`.
Local implementation only — no push, no recipe reseed.

## Files changed / added

### Plugin bundle — `plugins/galpi/`

| File | Role |
|---|---|
| `__init__.py` | empty package marker (loader requirement) |
| `algorithm.py` | `GALPIPlugin` entry class: shape validation, flatten/restore, per-gap orchestration, timing, observed restoration, scalar metadata |
| `galpi_config.py` | `DEFAULT_CONFIG`, `GALPIConfig`, `validate_and_build_config`, config fingerprint |
| `galpi_components.py` | window/degree schedule, scaled-time polynomial fit/predict, RankWarning handling, linear-anchor fill, fallback chain, bounds policy |
| `galpi_gaps.py` | `Gap`, `iter_hidden_gaps`, prev/next observed-index maps, nearest, max/long-gap helpers |
| `galpi_state.py` | `GALPIState`, `build_galpi_state` (observed-only train stats) |
| `galpi_errors.py` | `GALPIError` hierarchy |
| `imputebench_plugin.json` | extended v2 manifest (experimental, single_shot, fit_impute) |
| `requirements.txt` | `numpy` only |
| `README.md` | full contract README incl. literature-vs-evidence separation |

### Tests — `tests/plugins/galpi/`

`__init__.py`, `helpers.py`, and 14 test modules: `test_galpi_config`,
`test_galpi_gap_detection`, `test_galpi_degree_window`, `test_galpi_algorithm_contract`,
`test_galpi_mask_semantics`, `test_galpi_leakage`, `test_galpi_short_gap_linear_anchor`,
`test_galpi_local_polynomial`, `test_galpi_rank_warning_fallback`, `test_galpi_fallbacks`,
`test_galpi_edges`, `test_galpi_metadata`, `test_galpi_manifest`,
`test_galpi_plugin_loader`, `test_galpi_temporal_integration`.

### Docs

`docs/audit/mot02_galpi/BASELINE.md`, this report.

No existing source files were modified. The builtin recipe was **not** patched (see
*Recipe decision*).

## Deliberate deviation from the spec's literal file layout

The spec §7 names the internal modules `config.py`, `components.py`, `gaps.py`, `state.py`,
`errors.py`; §8 states the skeleton is "normative for behavior, not necessarily for exact
file layout". The internal modules are instead `galpi_`-prefixed.

**Why:** the plugin loader puts each bundle directory on `sys.path` and imports submodules by
bare name, caching them in `sys.modules`. DACPI already ships generic `components`/`state`/
`gaps`/`errors` modules. Two bundles sharing those names collide in one Python process (e.g.
a full `pytest tests/plugins` run, or any flow that loads several plugins): the second
bundle's `from components import …` would resolve to the first bundle's module. ARMA avoids
this with uniquely named submodules; GALPI follows that proven pattern. Verified:
`pytest tests/plugins -q` (galpi + dacpi + arma together) → **332 passed**, no collision.

## Tests run

| Command | Result |
|---|---|
| `pytest tests/plugins/galpi -q` | **109 passed** |
| `pytest tests/plugins -q` (galpi + dacpi + arma + manifest validation) | **332 passed** |
| `pytest tests/test_cl2_classical_execution_contract.py -q` | **16 passed** (no classical regression) |

## Plugin validation output

```
$ python -m imputebench methods plugin validate galpi
{ "valid": true, "errors": [], "warnings": [] }

$ python -m imputebench methods plugin show galpi --strict-validation --format json
slug: galpi  name: GALPI  family: classical  entry_class: GALPIPlugin
validation.valid: True  validation.strict: True  validation.errors: []
readiness: experimental, benchmark_ready False, comparison_ready False, scientific_ready False
```

## Recipe decision (MOT02-07)

**Not applied.** Recipe integration is explicitly optional (spec §12.2) and gated behind
plugin readiness. Three reasons to defer:

1. **Repo convention.** The sibling experimental classical plugin **DACPI** is *not* in the
   builtin classical recipe. Experimental plugins stay out until benchmark-ready.
2. **Readiness.** GALPI is `readiness=experimental`, `benchmark_ready=false`,
   `comparison_ready=false`. Adding it to the official recipe would imply a readiness it has
   not earned.
3. **Cross-dependency.** The spec's §12.2 patch adds `CubicSpline` *and* `GALPI` together.
   `CubicSpline` is the parallel MOT 01 plugin; adding a not-yet-resolvable candidate method
   would risk breaking `test_candidate_method_registry_validation`.

The patch is small and reversible. When GALPI reaches benchmark readiness, add `"GALPI"` to
`candidate_methods` in `_classical_recipe()` (`builtin_recipes.py`) and reseed with
`imputebench admin migrate recipe-books --seed-builtins --apply --verify`. `"GALPI"` already
matches the manifest `name` and the registry-resolved method.

## Same-support smoke (MOT02-08)

The full `imputebench results export-human` smoke requires a seeded results database
(registered algorithms + an executed run) and is left for the benchmark phase. The
test-level same-support smoke is covered by `test_galpi_temporal_integration.py`:
GALPI and `LinearInterpolation` run on an identical NaN-defined support, produce the same
prediction shape, GALPI preserves all observed values and fills every hidden slot, and
GALPI's hidden predictions are invariant to the numeric content of hidden cells
(leakage-safe).

## Known caveats

- Least-squares polynomials can overshoot on sparse/noisy/long-gap support; mitigated by the
  degree cap (`max_degree=3`), support/identifiability caps, RankWarning→fallback, and the
  optional `bounds_policy`.
- Window and degree adaptivity are heuristics; they must be evaluated per dataset and mask
  family.
- GALPI is univariate per flattened column: no cross-pollutant or spatial reconstruction.
- `LinearInterpolation` consults values (NaN positions), GALPI consults the mask. Short-gap
  equivalence is asserted against the boundary-anchor linear formula, not that class.

## Claim boundary

GALPI is a **proposed candidate** adaptive imputation method. No performance claim is made.
The manifest/`get_info`/README carry no RMSE target and no "beats Linear" statement; the
cited literature is marked as motivation, explicitly **not** EviBench evidence. Any
comparative claim requires same-support benchmark evidence under a fixed dataset snapshot,
mask bank/realization, recipe revision, phase and metric contract; adaptive-gap claims
additionally require the gap-stratified report (buckets 1-3 / 4-15 / >15).
