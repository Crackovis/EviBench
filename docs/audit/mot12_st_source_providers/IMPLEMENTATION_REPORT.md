# MOT 12 — ST Source Providers · Implementation report

| Field | Value |
|---|---|
| HEAD SHA (at baseline) | `0f28c1315fb077cfa32d50669124ec492e7f5c3f` |
| Date | 2026-06-29 |
| Agent | Sisyphus (implementation) |
| Schema | unchanged (no migration) |

## What MOT 12 changed

A **domain-aware mask-resolution chain** replaces the single generic resolver, so
spatio-temporal results resolve their exact evaluation mask from graph-aware
`BenchmarkMaskBank`s — and the storyboard renders an honest figure or blocks with a
precise code, never a fabricated one.

### Files created
* `imputebench/services/results_interaction/st_reconstruction_mask_resolver.py` —
  `STBenchmarkMaskBankResolver` + `STMaskBankResolutionAudit`. ST detection
  (multi-field), realization resolution (+ singleton inference warning), dataset /
  phase / realization / graph-identity / support-fingerprint validation, `mask.npy`
  resolution, `one_means_hidden` semantics, and the `STMask*` blocked codes.

### Files modified
* `reconstruction_source_resolver.py` — `ReconstructionSourceResolver` gains
  `st_mask_resolver` + `enable_st_resolver`; the ST resolver is the third link
  (after explicit fields and catalog, before the generic unresolved block). Lazy
  import; non-ST results never query ST banks. Unexpected errors become
  `STMaskResolverError`, never a silent generic message.
* `reconstruction_view_alignment_service.py` — new **`graph_node`** alignment
  mode: projects full-grid `(T,H,W,F)` originals/masks into node space `(T,N,F)`
  by row-major reshape (N = H·W) so the existing rank-3 storyboard path renders.
  Non-row-major / non-resolvable mappings raise `STGraphMaskAlignmentUnsupported`.

### Already satisfied (verified, not re-built)
* `CoreResultExporter.result_summary()` already emits `metrics.node_summary` from
  `result.node_metrics` and registers `result_id` in `source_ids`, **independently
  of the storyboard** — confirmed by test, no second summary path added.

## Shape audit outcome (§7.2)

| Item | Value |
|---|---|
| Original London AQ | `(600, 10, 14, 4)` = (T,H,W,F) |
| ST predictions | `(600, 140, 4)` = (T, N=H·W, F) — **graph-node** |
| `node_index_policy` | `row_major_runtime_grid_v1` → row-major reshape applies |

So MOT 12 implements the row-major `graph_node` alignment (the spec-preferred
"supported" branch of §7.4), validated with synthetic fixtures.

## Field validation (real DB, §15.2)

Sampling one result per ST algorithm from `exp_st`:

```
dcrnn / grin / ignnk / stgcn → "benchmark_mask_bank_id … does not resolve …
                                 [STMaskBankMissing]"
ST-coded blocks: 4   generic "No exact evaluation mask" blocks: 0
```

The legacy 853 ST mask banks were removed in an earlier cleanup, so the exact mask
is **genuinely absent** — these rows now block with the precise `STMaskBankMissing`
code instead of the generic ambiguous message (§0, §2.1). Rendering ST storyboards
end-to-end requires a fresh GPU-backed re-run (`exp_st_v2`, MOT 10 / deferred) that
re-materialises banks + on-disk `mask.npy`; the resolver + graph-node alignment are
unit-validated so that run will render.

## GPU re-run (separate, per §11.1)

MOT 12 adds **no** CUDA code. A fresh scoped ST campaign should be launched from a
CUDA-capable environment, e.g.:

```bash
conda run -n deeplearning python -m imputebench experiment st experiment run \
  --experiment-id exp_st_v2 --tier A --dataset-id london_aq \
  --algorithm stgcn --algorithm dcrnn --algorithm grin --algorithm ignnk
```

then `results export-human --experiment-id exp_st_v2 --phase test --hub …`.

## Tests

| Suite | Result |
|---|---|
| `tests/human_evidence/st_source_providers` (8 modules) | 29 passed |
| `tests/human_evidence` (incl. new ST modules) | 375 passed |
| `tests/results_interaction` | 368 passed |
| `tests/experiment_identity` | 81 passed |
| `tests/st_bridge` | 17 passed |

Coverage: exact mask resolution + every `STMask*` block code; chain insertion +
temporal regression (ST resolver never consulted for non-ST); graph-node
projection + explicit `STGraphMaskAlignmentUnsupported`; end-to-end graph-node
storyboard render; `result_summary` node-summary independence; MOT 08 reader
resolves ST summaries by `source_ids`; export-human ST pack coverage.

Pre-existing, unrelated failures persist outside this change set
(`tests/spatiotemporal/test_st_recipe_materialization.py` `KeyError:
'calibration_scientific'`; duplicate `test_experiment_id_validation.py` basename).

## Acceptance summary (§14)

* Mask resolution — exact `mask.npy`, `one_means_hidden`; dataset/phase/graph/
  fingerprint/realization mismatches each block with a precise code; temporal
  resolution unchanged ✔
* Storyboards — compatible ST graph-node result renders four-panel; non-row-major
  blocks explicitly; never rendered from a train/fallback mask; no generic message
  for valid ST bank rows ✔
* Summaries — `result_summary` from `CoreResultExporter`, independent of the
  storyboard, with `node_summary`; `source_ids` include `result_id`; reader
  resolves ✔
* Export-human — scoped ST selection works; valid summaries resolve; residual
  blockers carry precise ST / missing-data codes, not a provider gap ✔
* Safety — no algorithm/metric/training changes; no CUDA code; no destructive
  cleanup; no fake diagnostic success for missing masks ✔

## Deferred (§17)

Full ST node-level metric table page; ST-specific storyboard visual design beyond
the generic four-panel; graph topology overlay; automatic GPU re-run workflow;
legacy-row re-run/deletion; the optional `st_node_metric_table` evidence item.
