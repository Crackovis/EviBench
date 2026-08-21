# Evidence pack — Wuhan interpolation (n=20 realizations)

**Evidence grade:** `reportable_with_caveats` | **Claim level:** `bounded_significance`

> Caption numbering is **provisional** (no chapter set). Pass `--caption-chapter N` to fix chapter numbers.

## Scope

720 result(s) across 9 cohort(s). Dataset: dataset_01. Benchmark contracts: contract_01, contract_02, contract_03, contract_04, contract_05, contract_06, contract_07, contract_08, contract_09. Phase: execute.

## Coverage counts

| Boundary | Count |
|---|---:|
| Database matches | 720 |
| Unique database results | 720 |
| Selected results | 720 |
| Source plan targets | 720 |
| Source manifest targets | 720 |
| Source summaries read | 720 |
| Station-scoped cohorts | 9 |
| Storyboards requested | 720 |
| Storyboards scientific | 30 |
| Storyboards diagnostic | 0 |
| Storyboards blocked | 0 |

## How to open the dashboard

Open [the offline dashboard](evidence_dashboard.html) directly in any browser. It is a single self-contained file and makes no network request.

## Executive summary

- Cubic Spline obtained the lowest mean RMSE in 5 of 9 reportable recipes.
- These results are specific to the selected dataset snapshot, benchmark contracts, artificial missingness protocols, evaluation supports, algorithm implementations, metric definitions, and execution environment. They do not establish universal algorithm superiority or deployment performance.

## Balanced headline

Status: `available`. Metric: `rmse` (lower is better).

Balanced intersection: 9 of 9 station-scoped cohorts.

| Algorithm | Macro mean | Mean rank | Wins | Ties | Cohorts | Coverage |
|---|---:|---:|---:|---:|---:|---:|
| linear_interpolation | 7.791 | 1.556 | 4 | 0 | 9 | 100% |
| cubic_spline | 8.366 | 1.667 | 5 | 0 | 9 | 100% |
| galpi | 9.177 | 3 | 0 | 0 | 9 | 100% |
| nearest_interpolation | 11.14 | 3.778 | 0 | 0 | 9 | 100% |

## Primary comparison table

[Primary comparison table](tables/comparison_global.md)

*Table 0.1: Primary per-recipe comparison under the selected benchmark contract.*

| Station | Recipe | cubic_spline | galpi | linear_interpolation | nearest_interpolation |
|---|---|---|---|---|---|
| full_grid | MAR 10% | **5.3298 ± 0.2304 (n=20)** | 5.4280 ± 0.2457 (n=20) | 5.3422 ± 0.2444 (n=20) | 8.4709 ± 0.2827 (n=20) |
| full_grid | MAR 30% | 7.3327 ± 0.3439 (n=20) | 8.8128 ± 0.3286 (n=20) | **7.0844 ± 0.3251 (n=20)** | 9.7946 ± 0.3088 (n=20) |
| full_grid | MAR 50% | 19.3106 ± 3.1428 (n=20) | 16.7447 ± 0.3687 (n=20) | **13.2249 ± 0.4309 (n=20)** | 15.3717 ± 0.4104 (n=20) |
| full_grid | MCAR 10% | **5.1102 ± 0.0319 (n=20)** | 5.1421 ± 0.0300 (n=20) | 5.1113 ± 0.0317 (n=20) | 8.2913 ± 0.0386 (n=20) |
| full_grid | MCAR 30% | 6.2474 ± 0.0270 (n=20) | 6.7241 ± 0.0405 (n=20) | **6.0708 ± 0.0240 (n=20)** | 8.8899 ± 0.0241 (n=20) |
| full_grid | MCAR 50% | 8.1574 ± 0.0731 (n=20) | 9.9201 ± 0.0521 (n=20) | **7.6956 ± 0.0369 (n=20)** | 10.2994 ± 0.0363 (n=20) |
| full_grid | MNAR 10% | **6.8779 ± 0.1381 (n=20)** | 7.2226 ± 0.1459 (n=20) | 7.1843 ± 0.1506 (n=20) | 12.1923 ± 0.1328 (n=20) |
| full_grid | MNAR 30% | **7.7384 ± 0.0850 (n=20)** | 8.9300 ± 0.1318 (n=20) | 8.1766 ± 0.0835 (n=20) | 12.7449 ± 0.0668 (n=20) |
| full_grid | MNAR 50% | **9.1859 ± 0.1017 (n=20)** | 13.6704 ± 0.2102 (n=20) | 10.2320 ± 0.0943 (n=20) | 14.2285 ± 0.0920 (n=20) |

Primary metric: `rmse` (lower is better). Cells show mean ± std (n). `—` = unavailable; `†` = not comparable; **bold** = gated BEST.

## Pollutant sidecar metrics

Pollutant metrics are copied from result sidecars only.

- [Pollutant markdown table](tables/pollutant_breakdown.md)
- [Pollutant CSV table](tables/pollutant_breakdown.csv)
- [Pollutant JSON table](tables/pollutant_breakdown.json)

Rows: 4320.

## Available figures

- Within-cohort rank stability chart: `figures/ranking_chart.png`
- Storyboards scientific/available: 30
- Storyboards diagnostic-only: 0
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r007.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r005.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r017.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r018.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r009.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r011.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r001.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r001.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r015.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r008.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r005.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r014.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r006.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r009.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r013.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r002.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r007.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r010.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r008.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r006.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r004.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r019.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r000.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r016.md) - `available_scientific`
  - [full_grid | MAR 10% | cubic_spline](figures/storyboards/full_grid_mar_10_cubic_spline_execute_r012.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r003.md) - `available_scientific`
  - [full_grid | MAR 10% | galpi](figures/storyboards/full_grid_mar_10_galpi_execute_r004.md) - `available_scientific`

## Multi-dimensional comparison

Relative, versioned multi-dimensional comparison (policy `human_multidimensional_relative_v1`, minimum coverage 0.80). Every score is relative to the balanced cohort intersection (9 of 9 cohorts); missing dimensions stay missing and are never set to 0 or 1.

### Availability and coverage

| Metric | Status | Source quality | Available | Coverage |
|---|---|---|---:|---:|
| accuracy | available | exported_primary_metric | 9/9 | 1.00 |
| speed | available | native_timing_spans | 720/720 | 1.00 |
| stability | available | exported_primary_metric | 36/36 | 1.00 |
| rate_robustness | available | exported_primary_metric | 12/12 | 1.00 |
| mechanism_robustness | available | exported_primary_metric | 12/12 | 1.00 |
| parameter_efficiency | unavailable | explicit_parameter_evidence | 0/720 | 0.00 |
| memory_efficiency | unavailable | not_instrumented | 0/720 | 0.00 |

### Master comparison

Five relative dimension scores (no weighted overall score).

| Algorithm | Accuracy | Speed | Stability | Rate rob. | Mech. rob. | Dim. cov. |
|---|---:|---:|---:|---:|---:|---:|
| Cubic Spline | 0.852 | 0.06148 | 0.1874 | 0.4174 | 0.6576 | 1.00 |
| Galpi | 0.6071 | 0.01292 | 0.2973 | 0.09921 | 0.6858 | 1.00 |
| Linear Interpolation | 0.9603 | 1 | 0.3973 | 0.6322 | 0.6786 | 1.00 |
| Nearest Interpolation | 0.07191 | 0.9057 | 0.9963 | 1 | 0.6825 | 1.00 |

Companion tables: [complexity](tables/framework/complexity.md), [stability](tables/framework/stability.md), [rate robustness](tables/framework/rate_robustness.md), [mechanism robustness](tables/framework/mechanism_robustness.md), [master comparison](tables/framework/master_comparison.md), [availability](tables/framework/availability.md).

### Pareto summary

Accuracy–runtime non-dominated front (lower RMSE, lower runtime): linear_interpolation.

## Statistical tests

Existing paired Wilcoxon signed-rank tests (BH-adjusted) are exposed without recomputation or duplicated interpretation. A non-significant result does not prove equivalence.

| Cohort | Metric | A | B | n | p raw | p adj | effect | status | claim |
|---|---|---|---|---:|---:|---:|---:|---|---|
| cohort_full_grid_mar_10_4ebde8e8f3 | rmse | cubic_spline | galpi | 20 | 1.907e-06 | 2.289e-06 | -1 | computed | yes |
| cohort_full_grid_mar_10_4ebde8e8f3 | rmse | cubic_spline | linear_interpolation | 20 | 0.114 | 0.114 | -0.4095 | computed | no |
| cohort_full_grid_mar_10_4ebde8e8f3 | rmse | cubic_spline | nearest_interpolation | 20 | 1.907e-06 | 2.289e-06 | -1 | computed | yes |
| cohort_full_grid_mar_10_4ebde8e8f3 | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 2.289e-06 | 1 | computed | yes |
| cohort_full_grid_mar_10_4ebde8e8f3 | rmse | galpi | nearest_interpolation | 20 | 1.907e-06 | 2.289e-06 | -1 | computed | yes |
| cohort_full_grid_mar_10_4ebde8e8f3 | rmse | linear_interpolation | nearest_interpolation | 20 | 1.907e-06 | 2.289e-06 | -1 | computed | yes |
| cohort_full_grid_mar_30_18214cc37c | rmse | cubic_spline | galpi | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mar_30_18214cc37c | rmse | cubic_spline | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | 1 | computed | yes |
| cohort_full_grid_mar_30_18214cc37c | rmse | cubic_spline | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mar_30_18214cc37c | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | 1 | computed | yes |
| cohort_full_grid_mar_30_18214cc37c | rmse | galpi | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mar_30_18214cc37c | rmse | linear_interpolation | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mar_50_82c12bcdb8 | rmse | cubic_spline | galpi | 20 | 0.00639 | 0.00639 | 0.6762 | computed | yes |
| cohort_full_grid_mar_50_82c12bcdb8 | rmse | cubic_spline | linear_interpolation | 20 | 1.907e-06 | 2.289e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_82c12bcdb8 | rmse | cubic_spline | nearest_interpolation | 20 | 1.907e-06 | 2.289e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_82c12bcdb8 | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 2.289e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_82c12bcdb8 | rmse | galpi | nearest_interpolation | 20 | 1.907e-06 | 2.289e-06 | 1 | computed | yes |
| cohort_full_grid_mar_50_82c12bcdb8 | rmse | linear_interpolation | nearest_interpolation | 20 | 1.907e-06 | 2.289e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_10_beafdd3d3a | rmse | cubic_spline | galpi | 20 | 6.294e-05 | 7.553e-05 | -0.9143 | computed | yes |
| cohort_full_grid_mcar_10_beafdd3d3a | rmse | cubic_spline | linear_interpolation | 20 | 0.8124 | 0.8124 | -0.06667 | computed | no |
| cohort_full_grid_mcar_10_beafdd3d3a | rmse | cubic_spline | nearest_interpolation | 20 | 1.907e-06 | 2.861e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_10_beafdd3d3a | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 2.861e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_10_beafdd3d3a | rmse | galpi | nearest_interpolation | 20 | 1.907e-06 | 2.861e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_10_beafdd3d3a | rmse | linear_interpolation | nearest_interpolation | 20 | 1.907e-06 | 2.861e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_30_380e398fad | rmse | cubic_spline | galpi | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_30_380e398fad | rmse | cubic_spline | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_30_380e398fad | rmse | cubic_spline | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_30_380e398fad | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_30_380e398fad | rmse | galpi | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_30_380e398fad | rmse | linear_interpolation | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_a8afad4cc5 | rmse | cubic_spline | galpi | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_a8afad4cc5 | rmse | cubic_spline | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_a8afad4cc5 | rmse | cubic_spline | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_a8afad4cc5 | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | 1 | computed | yes |
| cohort_full_grid_mcar_50_a8afad4cc5 | rmse | galpi | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mcar_50_a8afad4cc5 | rmse | linear_interpolation | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_10_2859d20c68 | rmse | cubic_spline | galpi | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_10_2859d20c68 | rmse | cubic_spline | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_10_2859d20c68 | rmse | cubic_spline | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_10_2859d20c68 | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_10_2859d20c68 | rmse | galpi | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_10_2859d20c68 | rmse | linear_interpolation | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_30_ec0a509ea8 | rmse | cubic_spline | galpi | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_30_ec0a509ea8 | rmse | cubic_spline | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_30_ec0a509ea8 | rmse | cubic_spline | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_30_ec0a509ea8 | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_30_ec0a509ea8 | rmse | galpi | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_30_ec0a509ea8 | rmse | linear_interpolation | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_7ff69a9216 | rmse | cubic_spline | galpi | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_7ff69a9216 | rmse | cubic_spline | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_7ff69a9216 | rmse | cubic_spline | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_7ff69a9216 | rmse | galpi | linear_interpolation | 20 | 1.907e-06 | 1.907e-06 | 1 | computed | yes |
| cohort_full_grid_mnar_50_7ff69a9216 | rmse | galpi | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |
| cohort_full_grid_mnar_50_7ff69a9216 | rmse | linear_interpolation | nearest_interpolation | 20 | 1.907e-06 | 1.907e-06 | -1 | computed | yes |

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
