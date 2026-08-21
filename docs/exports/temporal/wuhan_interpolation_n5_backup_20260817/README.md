# Evidence pack

**Evidence grade:** `reportable_with_caveats` | **Claim level:** `bounded_comparison`

> Caption numbering is **provisional** (no chapter set). Pass `--caption-chapter N` to fix chapter numbers.

## Scope

180 result(s) across 9 cohort(s). Dataset: dataset_01. Benchmark contracts: contract_01, contract_02, contract_03, contract_04, contract_05, contract_06, contract_07, contract_08, contract_09. Phase: execute.

## Coverage counts

| Boundary | Count |
|---|---:|
| Database matches | 180 |
| Unique database results | 180 |
| Selected results | 180 |
| Source plan targets | 180 |
| Source manifest targets | 180 |
| Source summaries read | 180 |
| Station-scoped cohorts | 9 |
| Storyboards requested | 180 |
| Storyboards scientific | 30 |
| Storyboards diagnostic | 0 |
| Storyboards blocked | 0 |

## How to open the dashboard

Open [the offline dashboard](evidence_dashboard.html) directly in any browser. It is a single self-contained file and makes no network request.

## Executive summary

- Linear Interpolation obtained the lowest mean RMSE in 5 of 9 reportable recipes.
- These results are specific to the selected dataset snapshot, benchmark contracts, artificial missingness protocols, evaluation supports, algorithm implementations, metric definitions, and execution environment. They do not establish universal algorithm superiority or deployment performance.

## Balanced headline

Status: `available`. Metric: `rmse` (lower is better).

Balanced intersection: 9 of 9 station-scoped cohorts.

| Algorithm | Macro mean | Mean rank | Wins | Ties | Cohorts | Coverage |
|---|---:|---:|---:|---:|---:|---:|
| linear_interpolation | 7.792 | 1.444 | 5 | 0 | 9 | 100% |
| cubic_spline | 8.392 | 1.778 | 4 | 0 | 9 | 100% |
| galpi | 9.166 | 3 | 0 | 0 | 9 | 100% |
| nearest_interpolation | 11.16 | 3.778 | 0 | 0 | 9 | 100% |

## Primary comparison table

[Primary comparison table](tables/comparison_global.md)

*Table 0.1: Primary per-recipe comparison under the selected benchmark contract.*

| Station | Recipe | cubic_spline | galpi | linear_interpolation | nearest_interpolation |
|---|---|---|---|---|---|
| full_grid | MAR 10% | **5.3982 ± 0.2239 (n=5)** | 5.4847 ± 0.2370 (n=5) | 5.3989 ± 0.2364 (n=5) | 8.5242 ± 0.2313 (n=5) |
| full_grid | MAR 30% | 7.4363 ± 0.3525 (n=5) | 8.8595 ± 0.3341 (n=5) | **7.1430 ± 0.3182 (n=5)** | 9.8552 ± 0.2905 (n=5) |
| full_grid | MAR 50% | 19.5414 ± 2.6500 (n=5) | 16.7084 ± 0.1355 (n=5) | **13.3105 ± 0.4410 (n=5)** | 15.4781 ± 0.4316 (n=5) |
| full_grid | MCAR 10% | 5.1084 ± 0.0331 (n=5) | 5.1422 ± 0.0262 (n=5) | **5.1080 ± 0.0297 (n=5)** | 8.2925 ± 0.0420 (n=5) |
| full_grid | MCAR 30% | 6.2541 ± 0.0382 (n=5) | 6.7225 ± 0.0530 (n=5) | **6.0715 ± 0.0312 (n=5)** | 8.8943 ± 0.0244 (n=5) |
| full_grid | MCAR 50% | 8.1425 ± 0.0450 (n=5) | 9.9044 ± 0.0309 (n=5) | **7.6931 ± 0.0209 (n=5)** | 10.2899 ± 0.0215 (n=5) |
| full_grid | MNAR 10% | **6.8065 ± 0.0824 (n=5)** | 7.1017 ± 0.0940 (n=5) | 7.0710 ± 0.1072 (n=5) | 12.1189 ± 0.1277 (n=5) |
| full_grid | MNAR 30% | **7.6809 ± 0.0590 (n=5)** | 8.8985 ± 0.1870 (n=5) | 8.1237 ± 0.1143 (n=5) | 12.7267 ± 0.1047 (n=5) |
| full_grid | MNAR 50% | **9.1625 ± 0.0807 (n=5)** | 13.6703 ± 0.1602 (n=5) | 10.2114 ± 0.0885 (n=5) | 14.2335 ± 0.0918 (n=5) |

Primary metric: `rmse` (lower is better). Cells show mean ± std (n). `—` = unavailable; `†` = not comparable; **bold** = gated BEST.

## Pollutant sidecar metrics

Pollutant metrics are copied from result sidecars only.

- [Pollutant markdown table](tables/pollutant_breakdown.md)
- [Pollutant CSV table](tables/pollutant_breakdown.csv)
- [Pollutant JSON table](tables/pollutant_breakdown.json)

Rows: 1080.

## Available figures

- Within-cohort rank stability chart: `figures/ranking_chart.png`
- Storyboards scientific/available: 30
- Storyboards diagnostic-only: 0
  - [full_grid | MAR 10% | linear_interpolation](figures/storyboards/full_grid_mar_10_linear_interpolation_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 30% | galpi](figures/storyboards/full_grid_mar_30_galpi_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 10% | linear_interpolation](figures/storyboards/full_grid_mar_10_linear_interpolation_execute_r004.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 10% | linear_interpolation](figures/storyboards/full_grid_mar_10_linear_interpolation_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 30% | galpi](figures/storyboards/full_grid_mar_30_galpi_execute_r004.md) - `available_scientific`
  - [full_grid | MAR 30% | cubic_spline](figures/storyboards/full_grid_mar_30_cubic_spline_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 30% | cubic_spline](figures/storyboards/full_grid_mar_30_cubic_spline_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 10% | linear_interpolation](figures/storyboards/full_grid_mar_10_linear_interpolation_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 30% | galpi](figures/storyboards/full_grid_mar_30_galpi_execute_r001.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r001.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 30% | cubic_spline](figures/storyboards/full_grid_mar_30_cubic_spline_execute_r004.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r004.md) - `available_scientific`
  - [full_grid | MAR 10% | nearest_interpolation](figures/storyboards/full_grid_mar_10_nearest_interpolation_execute_r004.md) - `available_scientific`
  - [full_grid | MAR 30% | cubic_spline](figures/storyboards/full_grid_mar_30_cubic_spline_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 10% | linear_interpolation](figures/storyboards/full_grid_mar_10_linear_interpolation_execute_r001.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r004.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 10% | nearest_interpolation](figures/storyboards/full_grid_mar_10_nearest_interpolation_execute_r001.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r001.md) - `available_scientific`
  - [full_grid | MAR 10% | nearest_interpolation](figures/storyboards/full_grid_mar_10_nearest_interpolation_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 30% | galpi](figures/storyboards/full_grid_mar_30_galpi_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 10% | nearest_interpolation](figures/storyboards/full_grid_mar_10_nearest_interpolation_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 30% | galpi](figures/storyboards/full_grid_mar_30_galpi_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 10% | nearest_interpolation](figures/storyboards/full_grid_mar_10_nearest_interpolation_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 30% | cubic_spline](figures/storyboards/full_grid_mar_30_cubic_spline_execute_r001.md) - `available_scientific`

## Multi-dimensional comparison

Relative, versioned multi-dimensional comparison (policy `human_multidimensional_relative_v1`, minimum coverage 0.80). Every score is relative to the balanced cohort intersection (9 of 9 cohorts); missing dimensions stay missing and are never set to 0 or 1.

### Availability and coverage

| Metric | Status | Source quality | Available | Coverage |
|---|---|---|---:|---:|
| accuracy | available | exported_primary_metric | 9/9 | 1.00 |
| speed | available | native_timing_spans | 180/180 | 1.00 |
| stability | available | exported_primary_metric | 36/36 | 1.00 |
| rate_robustness | available | exported_primary_metric | 12/12 | 1.00 |
| mechanism_robustness | available | exported_primary_metric | 12/12 | 1.00 |
| parameter_efficiency | unavailable | explicit_parameter_evidence | 0/180 | 0.00 |
| memory_efficiency | unavailable | not_instrumented | 0/180 | 0.00 |

### Master comparison

Five relative dimension scores (no weighted overall score).

| Algorithm | Accuracy | Speed | Stability | Rate rob. | Mech. rob. | Dim. cov. |
|---|---:|---:|---:|---:|---:|---:|
| Cubic Spline | 0.8504 | 0.05565 | 0.2997 | 0.4187 | 0.6605 | 1.00 |
| Galpi | 0.6128 | 0 | 0.4068 | 0.106 | 0.6908 | 1.00 |
| Linear Interpolation | 0.9617 | 1 | 0.4315 | 0.6312 | 0.6826 | 1.00 |
| Nearest Interpolation | 0.07246 | 0.8979 | 0.9782 | 1 | 0.6826 | 1.00 |

Companion tables: [complexity](tables/framework/complexity.md), [stability](tables/framework/stability.md), [rate robustness](tables/framework/rate_robustness.md), [mechanism robustness](tables/framework/mechanism_robustness.md), [master comparison](tables/framework/master_comparison.md), [availability](tables/framework/availability.md).

### Pareto summary

Accuracy–runtime non-dominated front (lower RMSE, lower runtime): linear_interpolation.

## Statistical tests

Existing paired Wilcoxon signed-rank tests (BH-adjusted) are exposed without recomputation or duplicated interpretation. A non-significant result does not prove equivalence.

| Cohort | Metric | A | B | n | p raw | p adj | effect | status | claim |
|---|---|---|---|---:|---:|---:|---:|---|---|
| cohort_full_grid_mar_10_ccda6baa83 | rmse | cubic_spline | galpi | 5 | 0.0625 | 0.075 | -1 | computed | no |
| cohort_full_grid_mar_10_ccda6baa83 | rmse | cubic_spline | linear_interpolation | 5 | 1 | 1 | -0.06667 | computed | no |
| cohort_full_grid_mar_10_ccda6baa83 | rmse | cubic_spline | nearest_interpolation | 5 | 0.0625 | 0.075 | -1 | computed | no |
| cohort_full_grid_mar_10_ccda6baa83 | rmse | galpi | linear_interpolation | 5 | 0.0625 | 0.075 | 1 | computed | no |
| cohort_full_grid_mar_10_ccda6baa83 | rmse | galpi | nearest_interpolation | 5 | 0.0625 | 0.075 | -1 | computed | no |
| cohort_full_grid_mar_10_ccda6baa83 | rmse | linear_interpolation | nearest_interpolation | 5 | 0.0625 | 0.075 | -1 | computed | no |
| cohort_full_grid_mar_30_eda88b3952 | rmse | cubic_spline | galpi | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mar_30_eda88b3952 | rmse | cubic_spline | linear_interpolation | 5 | 0.0625 | 0.0625 | 1 | computed | no |
| cohort_full_grid_mar_30_eda88b3952 | rmse | cubic_spline | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mar_30_eda88b3952 | rmse | galpi | linear_interpolation | 5 | 0.0625 | 0.0625 | 1 | computed | no |
| cohort_full_grid_mar_30_eda88b3952 | rmse | galpi | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mar_30_eda88b3952 | rmse | linear_interpolation | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mar_50_8348a0efd0 | rmse | cubic_spline | galpi | 5 | 0.125 | 0.125 | 0.8667 | computed | no |
| cohort_full_grid_mar_50_8348a0efd0 | rmse | cubic_spline | linear_interpolation | 5 | 0.0625 | 0.075 | 1 | computed | no |
| cohort_full_grid_mar_50_8348a0efd0 | rmse | cubic_spline | nearest_interpolation | 5 | 0.0625 | 0.075 | 1 | computed | no |
| cohort_full_grid_mar_50_8348a0efd0 | rmse | galpi | linear_interpolation | 5 | 0.0625 | 0.075 | 1 | computed | no |
| cohort_full_grid_mar_50_8348a0efd0 | rmse | galpi | nearest_interpolation | 5 | 0.0625 | 0.075 | 1 | computed | no |
| cohort_full_grid_mar_50_8348a0efd0 | rmse | linear_interpolation | nearest_interpolation | 5 | 0.0625 | 0.075 | -1 | computed | no |
| cohort_full_grid_mcar_10_8426867839 | rmse | cubic_spline | galpi | 5 | 0.0625 | 0.075 | -1 | computed | no |
| cohort_full_grid_mcar_10_8426867839 | rmse | cubic_spline | linear_interpolation | 5 | 1 | 1 | 0.06667 | computed | no |
| cohort_full_grid_mcar_10_8426867839 | rmse | cubic_spline | nearest_interpolation | 5 | 0.0625 | 0.075 | -1 | computed | no |
| cohort_full_grid_mcar_10_8426867839 | rmse | galpi | linear_interpolation | 5 | 0.0625 | 0.075 | 1 | computed | no |
| cohort_full_grid_mcar_10_8426867839 | rmse | galpi | nearest_interpolation | 5 | 0.0625 | 0.075 | -1 | computed | no |
| cohort_full_grid_mcar_10_8426867839 | rmse | linear_interpolation | nearest_interpolation | 5 | 0.0625 | 0.075 | -1 | computed | no |
| cohort_full_grid_mcar_30_bcfb4e1979 | rmse | cubic_spline | galpi | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mcar_30_bcfb4e1979 | rmse | cubic_spline | linear_interpolation | 5 | 0.0625 | 0.0625 | 1 | computed | no |
| cohort_full_grid_mcar_30_bcfb4e1979 | rmse | cubic_spline | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mcar_30_bcfb4e1979 | rmse | galpi | linear_interpolation | 5 | 0.0625 | 0.0625 | 1 | computed | no |
| cohort_full_grid_mcar_30_bcfb4e1979 | rmse | galpi | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mcar_30_bcfb4e1979 | rmse | linear_interpolation | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mcar_50_88b8f7d7f7 | rmse | cubic_spline | galpi | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mcar_50_88b8f7d7f7 | rmse | cubic_spline | linear_interpolation | 5 | 0.0625 | 0.0625 | 1 | computed | no |
| cohort_full_grid_mcar_50_88b8f7d7f7 | rmse | cubic_spline | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mcar_50_88b8f7d7f7 | rmse | galpi | linear_interpolation | 5 | 0.0625 | 0.0625 | 1 | computed | no |
| cohort_full_grid_mcar_50_88b8f7d7f7 | rmse | galpi | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mcar_50_88b8f7d7f7 | rmse | linear_interpolation | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_10_7617a7d65e | rmse | cubic_spline | galpi | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_10_7617a7d65e | rmse | cubic_spline | linear_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_10_7617a7d65e | rmse | cubic_spline | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_10_7617a7d65e | rmse | galpi | linear_interpolation | 5 | 0.0625 | 0.0625 | 1 | computed | no |
| cohort_full_grid_mnar_10_7617a7d65e | rmse | galpi | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_10_7617a7d65e | rmse | linear_interpolation | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_30_a4eaab2521 | rmse | cubic_spline | galpi | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_30_a4eaab2521 | rmse | cubic_spline | linear_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_30_a4eaab2521 | rmse | cubic_spline | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_30_a4eaab2521 | rmse | galpi | linear_interpolation | 5 | 0.0625 | 0.0625 | 1 | computed | no |
| cohort_full_grid_mnar_30_a4eaab2521 | rmse | galpi | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_30_a4eaab2521 | rmse | linear_interpolation | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_50_7bda064a97 | rmse | cubic_spline | galpi | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_50_7bda064a97 | rmse | cubic_spline | linear_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_50_7bda064a97 | rmse | cubic_spline | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_50_7bda064a97 | rmse | galpi | linear_interpolation | 5 | 0.0625 | 0.0625 | 1 | computed | no |
| cohort_full_grid_mnar_50_7bda064a97 | rmse | galpi | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |
| cohort_full_grid_mnar_50_7bda064a97 | rmse | linear_interpolation | nearest_interpolation | 5 | 0.0625 | 0.0625 | -1 | computed | no |

Full detail: [significance summary](stats/significance_summary.md).

## Blocked or unavailable evidence

None - every selected item assembled cleanly.

## Interpretation boundary

These results are specific to the selected dataset snapshot, benchmark contracts, artificial missingness protocols, evaluation supports, algorithm implementations, metric definitions, and execution environment. They do not establish universal algorithm superiority or deployment performance.

## Provenance

- [Provenance manifest](provenance/manifest.json)
- [Source export manifest](provenance/source_manifest.json)
- [Selection ledger](provenance/selection_ledger.json)
- [Alias to identity map](provenance/id_map.json)
- [Checksums](provenance/checksums.sha256)
