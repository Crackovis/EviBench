# Result Reconstruction Storyboard Contract (Sheet 11B §9)

The `result_storyboard` item is a deterministic four-panel reconstruction figure
generated from persisted truth — never a guessed array, inferred mask, or
favourable interval.

## Outputs

`result_storyboard.png`, `result_storyboard.json` (schema
`imputebench.result-storyboard/v1`), `result_storyboard.md`.

## Panels

```
A. Original observed truth        B. Artificial evaluation support (binary)
C. Imputed reconstruction         D. |original − imputed| on hidden support
```

- A and C share one numeric scale (robust **1st/99th percentile** of their
  shared finite values).
- B is binary: 1 = hidden/scored, 0 = not evaluated.
- D is restricted to evaluation support; non-support cells are transparent and
  excluded from statistics. Scale is 0 → 99th-percentile error (non-negative).

## Source resolution

| Source | Path |
|---|---|
| predictions | `ResultService.load_prediction_window(result_id, start, stop)` — bounded; the full-array API is forbidden |
| original | `DatasetService.load_array(dataset_id)`, sliced to the window |
| mask | `ReconstructionSourceResolver.resolve_mask` |
| error | `abs(original − imputed)` on evaluation support only |

## Mask resolution order (`ReconstructionSourceResolver`)

1. phase-aware persisted evaluation-mask field
   (`test_mask_artifact_path` for test; `benchmark_validation_mask_artifact_path`
   / `selection_mask_artifact_path` for validate; then `mask_artifact_path`);
2. the exact catalog mask record linked to the result (exactly one);
3. otherwise **blocked** (missing or ambiguous — never the newest file).

## Mask semantics

The persisted evaluation-mask fields are, by construction, *hidden* masks
consumed verbatim by the metric engine (`hidden = mask.astype(bool)`), so their
convention is the documented `one_means_hidden` — read from the field's role,
**not inferred from array density**. Normalisation:

```
true_means_hidden / one_means_hidden   -> support = mask.astype(bool)
true_means_observed / one_means_observed -> support = ~mask.astype(bool)
unknown                                -> blocked
```

## Deterministic window

```
max_timesteps = 96
pollutant     = first registered pollutant with finite evaluated support
start         = first support index with >= 1 evaluated point
stop          = min(start + max_timesteps, T)
```

For `[T,R,C,P]` the rendered view is `[T_window, R×C]` (row-major); for `[T,N,F]`
it is `[T_window, N]`; for `[T,F]` a single feature plane. The transform is
recorded in the sidecar.

## Blocking behaviour

| Situation | State / message |
|---|---|
| no prediction artifact | `missing` |
| original unresolvable | `blocked` |
| mask missing / ambiguous | `blocked` (candidate ids listed) |
| unknown mask semantics | `blocked` |
| shape mismatch | `blocked` (all shapes listed) |

A run target exports one storyboard for a single eligible result, an index plus
one storyboard per result for several in the same phase, and **blocks** when
eligible results span multiple phases without explicit selection.
