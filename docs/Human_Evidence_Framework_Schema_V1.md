# Human Evidence Multi-Dimensional Framework — Schema v1

Schema id: `imputebench.human-framework-metrics/v1`
Normalisation policy id: `human_multidimensional_relative_v1`

The framework is a **post-hoc** projection of the already-exported evidence pack.
It adds a relative, versioned, multi-dimensional comparison without mutating any
result, recomputing any primary metric, or opening the database.

## Bundle

`FrameworkMetricsBundle` is carried on `HumanEvidencePack.framework_metrics` and
its `fingerprint_payload()` is folded into the pack content fingerprint.

| Field | Meaning |
|---|---|
| `schema` | `imputebench.human-framework-metrics/v1` |
| `policy_id` | `human_multidimensional_relative_v1` |
| `min_coverage` | coverage threshold below which a dimension is blocked |
| `availability` | one `MetricAvailability` per metric id |
| `complexity_profiles` | per-algorithm runtime + parameter + interpretability |
| `stability_profiles` | per algorithm × station-scoped cohort × recipe CV |
| `rate_robustness_profiles` | per station × mechanism × algorithm degradation |
| `mechanism_robustness_profiles` | per station × rate × algorithm sensitivity |
| `global_profiles` | per-algorithm relative dimension scores (no weighted total) |
| `charts` | radar, Pareto, and per-cohort significance heatmap models |
| `balanced_cohort_count` / `total_cohort_count` | balanced intersection size |
| `warnings` | non-blocking diagnostics |

## Dimensions

| Dimension id | Source | Direction | Normalisation |
|---|---|---|---|
| `accuracy` | exported RMSE | lower better | intra-cohort min-max, macro over balanced |
| `speed` | canonical runtime total (admissible quality) | lower better | intra-cohort **log** min-max, macro over balanced |
| `stability` | RMSE coefficient of variation (ddof=1) | lower better | intra-cohort min-max, macro |
| `rate_robustness` | relative degradation 10→50 | lower better | per station × mechanism, macro |
| `mechanism_robustness` | `1 − relative_range(MCAR,MAR,MNAR)` | higher better | self-normalised, macro |
| `parameter_efficiency` | explicit parameter evidence only | lower count better | log min-max (only if ≥2 algorithms expose evidence) |
| `memory_efficiency` | not instrumented | — | always `unavailable` (never `1.0`) |

## Invariants

* A missing value is `None` — never a synthesised `0` or `1`.
* Every score is **relative** to its normalisation population and carries the
  `policy_id`.
* `coverage = available_count / expected_count`; no literal `540`.
* The radar draws only real axes and is blocked below three available axes.
* The Pareto front is the lower/lower non-dominated set over the balanced
  intersection.
* Significance heatmaps reuse the existing Wilcoxon + matched-pairs
  rank-biserial outputs (never Cohen's d).
* There is no weighted overall score.
* The fingerprint changes when policy, values, scores, availability, or sidecars
  change; it excludes timestamps, pack id, temporary paths, and render duration.

## Availability statuses

`available` · `partial` · `unavailable` · `blocked`, derived from coverage
against `min_coverage` (default `0.80`).
