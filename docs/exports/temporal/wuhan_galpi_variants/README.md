# Evidence pack — Wuhan GALPI variants (V1-V6, n=5 realizations)

**Evidence grade:** `reportable_with_caveats` | **Claim level:** `bounded_significance`

> Caption numbering is **provisional** (no chapter set). Pass `--caption-chapter N` to fix chapter numbers.

## Scope

480 result(s) across 3 cohort(s). Dataset: dataset_01. Benchmark contracts: contract_01, contract_02, contract_03. Phase: execute.

## Coverage counts

| Boundary | Count |
|---|---:|
| Database matches | 480 |
| Unique database results | 480 |
| Selected results | 480 |
| Source plan targets | 480 |
| Source manifest targets | 480 |
| Source summaries read | 480 |
| Station-scoped cohorts | 3 |
| Storyboards requested | 480 |
| Storyboards scientific | 30 |
| Storyboards diagnostic | 0 |
| Storyboards blocked | 0 |

## How to open the dashboard

Open [the offline dashboard](evidence_dashboard.html) directly in any browser. It is a single self-contained file and makes no network request.

## Executive summary

- Linear Interpolation obtained the lowest mean RMSE in 3 of 3 reportable recipes.
- These results are specific to the selected dataset snapshot, benchmark contracts, artificial missingness protocols, evaluation supports, algorithm implementations, metric definitions, and execution environment. They do not establish universal algorithm superiority or deployment performance.

## Balanced headline

Status: `available`. Metric: `rmse` (lower is better).

Balanced intersection: 3 of 3 station-scoped cohorts.

| Algorithm | Macro mean | Mean rank | Wins | Ties | Cohorts | Coverage |
|---|---:|---:|---:|---:|---:|---:|
| linear_interpolation | 10.38 | 1 | 3 | 0 | 3 | 100% |
| GALPI_V1 | 12.19 | 2 | 0 | 0 | 3 | 100% |
| GALPI_V5 | 12.19 | 2 | 0 | 0 | 3 | 100% |
| GALPI_V3 | 13.38 | 4 | 0 | 0 | 3 | 100% |
| galpi | 13.45 | 5.333 | 0 | 0 | 3 | 100% |
| GALPI_V2 | 13.41 | 5.667 | 0 | 0 | 3 | 100% |
| GALPI_V6 | 14.84 | 7 | 0 | 0 | 3 | 100% |
| GALPI_V4 | 15.75 | 8 | 0 | 0 | 3 | 100% |

## Primary comparison table

[Primary comparison table](tables/comparison_global.md)

*Table 0.1: Primary per-recipe comparison under the selected benchmark contract.*

| Station | Recipe | GALPI_V1 | GALPI_V2 | GALPI_V3 | GALPI_V4 | GALPI_V5 | GALPI_V6 | galpi | linear_interpolation |
|---|---|---|---|---|---|---|---|---|---|
| full_grid | MAR 50% | 16.0084 ± 0.3820 (n=20) | 16.6395 ± 0.3217 (n=20) | 16.5849 ± 0.3127 (n=20) | 17.7338 ± 0.4096 (n=20) | 16.0084 ± 0.3820 (n=20) | 17.3226 ± 0.3511 (n=20) | 16.7447 ± 0.3687 (n=20) | **13.2249 ± 0.4309 (n=20)** |
| full_grid | MCAR 50% | 8.7194 ± 0.0518 (n=20) | 9.9202 ± 0.0522 (n=20) | 9.9020 ± 0.0524 (n=20) | 11.9896 ± 0.0503 (n=20) | 8.7194 ± 0.0518 (n=20) | 11.1542 ± 0.0527 (n=20) | 9.9201 ± 0.0521 (n=20) | **7.6956 ± 0.0369 (n=20)** |
| full_grid | MNAR 50% | 11.8479 ± 0.2265 (n=20) | 13.6704 ± 0.2102 (n=20) | 13.6668 ± 0.2096 (n=20) | 17.5402 ± 0.1915 (n=20) | 11.8479 ± 0.2265 (n=20) | 16.0468 ± 0.2035 (n=20) | 13.6704 ± 0.2102 (n=20) | **10.2320 ± 0.0943 (n=20)** |

Primary metric: `rmse` (lower is better). Cells show mean ± std (n). `—` = unavailable; `†` = not comparable; **bold** = gated BEST.

## Pollutant sidecar metrics

Pollutant metrics are copied from result sidecars only.

- [Pollutant markdown table](tables/pollutant_breakdown.md)
- [Pollutant CSV table](tables/pollutant_breakdown.csv)
- [Pollutant JSON table](tables/pollutant_breakdown.json)

Rows: 2880.

## Available figures

- Within-cohort rank stability chart: `figures/ranking_chart.png`
- Storyboards scientific/available: 30
- Storyboards diagnostic-only: 0
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r004.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r014.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r001.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r006.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r019.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r015.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r007.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r008.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r012.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r013.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r010.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r009.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r001.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r017.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r004.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r005.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r005.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r011.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r006.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r007.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V2](figures/storyboards/full_grid_mar_50_galpi_v2_execute_r008.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r016.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r018.md) - `available_scientific`
  - [full_grid | MAR 50% | GALPI_V1](figures/storyboards/full_grid_mar_50_galpi_v1_execute_r009.md) - `available_scientific`

## Multi-dimensional comparison

Relative, versioned multi-dimensional comparison (policy `human_multidimensional_relative_v1`, minimum coverage 0.80). Every score is relative to the balanced cohort intersection (3 of 3 cohorts); missing dimensions stay missing and are never set to 0 or 1.

### Availability and coverage

| Metric | Status | Source quality | Available | Coverage |
|---|---|---|---:|---:|
| accuracy | available | exported_primary_metric | 3/3 | 1.00 |
| speed | available | native_timing_spans | 480/480 | 1.00 |
| stability | available | exported_primary_metric | 24/24 | 1.00 |
| rate_robustness | unavailable | exported_primary_metric | 0/24 | 0.00 |
| mechanism_robustness | available | exported_primary_metric | 8/8 | 1.00 |
| parameter_efficiency | unavailable | explicit_parameter_evidence | 0/480 | 0.00 |
| memory_efficiency | unavailable | not_instrumented | 0/480 | 0.00 |

### Master comparison

Five relative dimension scores (no weighted overall score).

| Algorithm | Accuracy | Speed | Stability | Rate rob. | Mech. rob. | Dim. cov. |
|---|---:|---:|---:|---:|---:|---:|
| Galpi V1 | 0.641 | 0.1971 | 0.2118 | - | 0.5447 | 0.80 |
| Galpi V2 | 0.4181 | 0.1092 | 0.5769 | - | 0.5962 | 0.80 |
| Galpi V3 | 0.4237 | 0 | 0.5854 | - | 0.5971 | 0.80 |
| Galpi V4 | 0 | 0.117 | 0.8396 | - | 0.6761 | 0.80 |
| Galpi V5 | 0.641 | 0.1977 | 0.2118 | - | 0.5447 | 0.80 |
| Galpi V6 | 0.1634 | 0.1357 | 0.7468 | - | 0.6439 | 0.80 |
| Galpi | 0.4103 | 0.1096 | 0.513 | - | 0.5924 | 0.80 |
| Linear Interpolation | 1 | 1 | 0.5525 | - | 0.5819 | 0.80 |

Companion tables: [complexity](tables/framework/complexity.md), [stability](tables/framework/stability.md), [rate robustness](tables/framework/rate_robustness.md), [mechanism robustness](tables/framework/mechanism_robustness.md), [master comparison](tables/framework/master_comparison.md), [availability](tables/framework/availability.md).

### Pareto summary

Accuracy–runtime non-dominated front (lower RMSE, lower runtime): linear_interpolation.

## Statistical tests

Existing paired Wilcoxon signed-rank tests (BH-adjusted) are exposed without recomputation or duplicated interpretation. A non-significant result does not prove equivalence.

| Cohort | Metric | A | B | n | p raw | p adj | effect | status | claim |
|---|---|---|---|---:|---:|---:|---:|---|---|
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V1 | GALPI_V2 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V1 | GALPI_V3 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V1 | GALPI_V4 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V1 | GALPI_V5 | 20 | 1 | 1 | 0 | identical | no |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V1 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V1 | galpi | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V1 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V2 | GALPI_V3 | 20 | 3.624e-05 | 3.903e-05 | 0.9333 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V2 | GALPI_V4 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V2 | GALPI_V5 | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V2 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V2 | galpi | 20 | 0.03623 | 0.03758 | -0.5333 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V2 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V3 | GALPI_V4 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V3 | GALPI_V5 | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V3 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V3 | galpi | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V3 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V4 | GALPI_V5 | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V4 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V4 | galpi | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V4 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V5 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V5 | galpi | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V5 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V6 | galpi | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | GALPI_V6 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_99b62f1a5b | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V1 | GALPI_V2 | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V1 | GALPI_V3 | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V1 | GALPI_V4 | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V1 | GALPI_V5 | 20 | 1 | 1 | 0 | identical | no |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V1 | GALPI_V6 | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V1 | galpi | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V1 | linear_interpolation | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V2 | GALPI_V3 | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V2 | GALPI_V4 | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V2 | GALPI_V5 | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V2 | GALPI_V6 | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V2 | galpi | 20 | 0.3683 | 0.3819 | 0.2381 | computed | no |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V2 | linear_interpolation | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V3 | GALPI_V4 | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V3 | GALPI_V5 | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V3 | GALPI_V6 | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V3 | galpi | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V3 | linear_interpolation | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V4 | GALPI_V5 | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V4 | GALPI_V6 | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V4 | galpi | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V4 | linear_interpolation | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V5 | GALPI_V6 | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V5 | galpi | 20 | 1.907e-06 | 2.054e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V5 | linear_interpolation | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V6 | galpi | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | GALPI_V6 | linear_interpolation | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_2ab1b5f626 | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 2.054e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V1 | GALPI_V2 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V1 | GALPI_V3 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V1 | GALPI_V4 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V1 | GALPI_V5 | 20 | 1 | 1 | 0 | identical | no |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V1 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V1 | galpi | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V1 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V2 | GALPI_V3 | 20 | 0.0001049 | 0.000113 | 0.8952 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V2 | GALPI_V4 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V2 | GALPI_V5 | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V2 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V2 | galpi | 20 | 1 | 1 | 0.04762 | computed | no |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V2 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V3 | GALPI_V4 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V3 | GALPI_V5 | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V3 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V3 | galpi | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V3 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V4 | GALPI_V5 | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V4 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V4 | galpi | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V4 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V5 | GALPI_V6 | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V5 | galpi | 20 | 1.907e-06 | 2.136e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V5 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V6 | galpi | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | GALPI_V6 | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_b783aa72f7 | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 2.136e-06 | 1 | computed | yes |

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
