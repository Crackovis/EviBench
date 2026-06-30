# OPT-HUMAN — Baseline & real-count audit

| Field | Value |
|---|---|
| HEAD SHA (at baseline) | `6a3b83141134f6376ce2cce9751e19de97e548dc` |
| Branch | `main` (submodule `Interfaces/EviBench`) |
| Date | 2026-06-30 |
| OS | Windows-10-10.0.26200-SP0 |
| Python | 3.10.15 |
| Logical CPUs | 20 |
| Agent | implementation (online) |

## 1. Measured result counts — *no number is hardcoded*

The draft assumed `exp_st_v2 = 4062` while the audited v0.3 thesis structure
mentions `exp_st_v2 (620)`. The blueprint requires the **real** count, measured
from the live selection, not an estimate. Measured from `data/metadata.db`:

| experiment_id | results (DB) | execution class |
|---|---:|---|
| `exp_st_v2` | **4062** | spatiotemporal |
| `exp2` | 900 | classical |
| `exp1` | 900 | classical |
| `<null>` (legacy) | 1 | — |

By execution class: `classical = 1800`, `spatiotemporal = 4063`.

> The real `exp_st_v2` scope is **4062** results, not 620. The published pack
> records the live `selected_result_count` in `provenance/selection.json`; no
> public text asserts 4062 (or 620) unless the published pack actually contains
> that many results.

## 2. Planned source items per result

`HumanSourceExportService.SOURCE_ITEMS` plans up to **7** items per result:

```
result_summary, metric_table, runtime_breakdown, result_storyboard,
provenance_manifest, comparison_ready_signal, artifact_inventory
```

So for `N` selected results the technical source plan holds up to `N × 7`
planned items (e.g. `exp_st_v2` full ≈ `4062 × 7 = 28 434`). The dominant cost
is `result_storyboard` (it loads prediction artefacts, resolves the evaluation
support, renders Matplotlib, then writes PNG + JSON + Markdown).

## 3. Pre-patch behaviour (confirmed by reading the code)

* `ExportEngine.execute()` is atomic (`stage → invoke → verify → checksum →
  manifest → publish`) but `_invoke_providers()` is strictly sequential.
* `HumanExportParallelPolicy.source_export_parallel` defaults to `False`; the CLI
  already exposes `--parallel-source-export` but it only toggled the *provider's*
  internal run-fan-out, which never fires for batch **result-id** exports
  (`_export_one` path), so source export stayed sequential.
* `--storyboards representative` only trimmed the dashboard gallery; a storyboard
  PNG was still rendered for **every** selected result.

## 4. Real dry-run validation (post-patch, non-destructive)

`results export-human --experiment-id exp2 --phase execute --algorithm-id galpi
--storyboards representative --max-dashboard-images 5 --dry-run` (225 eligible):

```json
{"mode":"representative","eligible_count":225,"selected_count":5,
 "omitted_count":220,"max_storyboards":5,
 "strata":["algorithm_id","masking_id","graph_policy","phase"],
 "seed":"sha256:f24cae9d…"}
```

Representative now limits **rendered** storyboards (5) before the source export,
not only the dashboard gallery. Strata are resolved descriptor-first from the
live selection candidates.

## 5. Real published-pack validation (post-patch)

`results export-human --experiment-id exp_st_v2 --algorithm-id stgcn
--graph-policy correlation_train_v1 --storyboards representative
--max-dashboard-images 12 --parallel-source-export --max-workers 4`
(published to a scratch dir, **not** `docs/.private_docs`):

| Metric | Value |
|---|---|
| selected_result_count | 105 |
| storyboard eligible / selected / omitted | 105 / 12 / 93 |
| `provenance/storyboard_sampling.json` | present + checksummed |
| exported_file_count | 77 |
| blocked_item_count | 100 (ST mask banks purged → `STMaskBankMissing`, expected per MOT12) |
| dashboard raw-UUID leak | **none** |
| dashboard policy note | present |
| wall time | ~20 s (parallel pool, 4 workers, Windows spawn, no deadlock) |

Storyboard items (12 ≥ `min_items_for_parallelism=8`) were dispatched to the
isolated process pool and completed cleanly; the 6 non-storyboard items per
result remained sequential.
