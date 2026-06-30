# Chapter 06 — Figure Gallery (`07_figures/`)

Generated: 2026-06-30 01:31 UTC
Total figures: 19 (7 original + 12 extended gallery)
Regenerate: `python -m imputebench st figures generate --clean`

---

## Original figures (root level — `fig_01`–`fig_07`)

Produced by `FigureExportService` as part of `regenerate-chapter06`.

| Figure | File | Description |
|--------|------|-------------|
| fig_01 | `fig_01_adjacency_all_policies.png` | Adjacency matrix visualisation for all graph policies (grid_4n, distance_knn, correlation_train). Shows the spatial connectivity structure under each policy. |
| fig_02 | `fig_02_adjacency_grid4n_grid.png` | Grid-4N adjacency laid out on the physical 10 × 14 sensor grid. Useful for spatial reference in the thesis. |
| fig_03 | `fig_03_node_holdout_spatial_map.png` | Spatial distribution of held-out (hidden) nodes across the benchmark mask banks. Shows which sensors are evaluation targets. |
| fig_04 | `fig_04_mae_by_algorithm.png` | MAE dot plot (mean ± std) per algorithm and graph policy. Aggregate overview across all ST results. |
| fig_05 | `fig_05_rmse_by_algorithm.png` | RMSE dot plot (mean ± std) per algorithm and graph policy. Companion to fig_04. |
| fig_06 | `fig_06_graph_policy_sensitivity.png` | Graph-policy sensitivity (BLOCKED — fixed-support comparison groups unavailable; see sidecar JSON for explanation). |
| fig_07 | `fig_07_within_st_lollipop.png` | Within-ST tier-A ranking lollipop chart. Shows algorithm ranking on the grid_4n_v1 benchmark. |

---

## Extended gallery (section subdirectories — `fig_08`–`fig_19`)

Produced by `FigureGalleryService`.
All performance figures are filtered to **official Phase C runs** (epoch_budget ≥ 50)
and restricted to the **`correlation_train_v1`** graph policy unless noted otherwise.

| Section | Subdirectory | Figures |
|---------|--------------|---------|
| A — Training Dynamics | `A_training_dynamics/` | fig_08–fig_11 |
| B — Performance Distribution | `B_performance_distribution/` | fig_12–fig_14 |
| C — Realization Stability | `C_realization_stability/` | fig_15–fig_16 |
| D — Runtime & Efficiency | `D_runtime_efficiency/` | fig_17–fig_18 |
| E — Scientific Evidence | `E_scientific_evidence/` | fig_19 |

See each subdirectory's `README.md` for figure-level details.

---

## Files in this directory

| File | Description |
|------|-------------|
| `figures_manifest.json` | Output manifest from `FigureExportService` (original figs) |
| `gallery_index.json` | Machine-readable index of all extended gallery figures with captions and file paths |
| `README.md` | This file |

---

*Regenerate all: `python -m imputebench st regenerate-chapter06 --clean --skip-tests --dataset-id london_aq --min-train-epochs 50`*
*Regenerate gallery only: `python -m imputebench st figures generate --clean`*
