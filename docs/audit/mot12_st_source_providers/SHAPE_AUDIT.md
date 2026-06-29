# MOT 12 — ST Shape Audit (§7.2)

Captured from representative `exp_st` results on the local DB (2026-06-29).

## Original dataset

| Item | Value |
|---|---|
| London AQ array shape | `[600, 10, 14, 4]` = (T, H, W, F) |
| Nodes if row-major flattened | N = H·W = 10·14 = **140** |
| Pollutants (F) | 4 |

## ST prediction artifacts

Every ST algorithm (stgcn / dcrnn / grin / ignnk) writes predictions in
**graph-node space**:

| Artifact | Shape | Note |
|---|---|---|
| `predictions_path` (`X_imputed.npy`) | `[600, 140, 4]` | (T, N, F) |
| `prediction_artifact_path` (`.npz` key `x_imputed_graph`) | `[600, 140, 4]` | (T, N, F) |

So ST predictions are **rank-3 (T, N, F)**, not full-grid rank-4. This is the
graph-node case of §7.4.

## Node identity

| Field | Value |
|---|---|
| `node_index_policy` | `row_major_runtime_grid_v1` → **row-major** |
| `node_mapping_fingerprint` | present (`sha256:…`) |
| `graph_id` / `graph_policy` | present (e.g. `grid_4n_v1`) |
| `dataset_view_mode` | `full_grid` |
| `runtime_summary_payload.effective_output_shape` | absent (not persisted) |

## Mask artifacts

ST `BenchmarkMaskBank` realizations are written as:

```
<artifact_dir>/<realization_id>/mask.npy        # full-grid hidden mask (T,H,W,F)
<artifact_dir>/<realization_id>/mask_graph.npy  # node-space (T,N,F)
<artifact_dir>/<realization_id>/mask_metadata.json
```

For the **legacy 853** the banks are unresolved (removed in cleanup), so no
on-disk mask exists today — they block with `STMaskBankMissing` after MOT 12.

## Alignment decision

* N = H·W = 140 and `node_index_policy` is **row-major** ⇒ the row-major
  reshape path of §7.5 applies: full-grid `(T,H,W,F)` projects to `(T,N,F)` by
  C-order reshape (node `n = h·W + w`).
* MOT 12 therefore implements a **`graph_node`** alignment mode that projects the
  original (and a full-grid mask) into `(T,N,F)` so the storyboard's existing
  rank-3 path renders a normal four-panel figure against the `(T,N,F)` prediction.
* When a result is ST but the node mapping is **not** row-major-resolvable
  (policy not row-major, or N ≠ H·W), alignment raises
  **`STGraphMaskAlignmentUnsupported`** and the storyboard blocks explicitly —
  never a misleading figure (§7.4, §14.2).
