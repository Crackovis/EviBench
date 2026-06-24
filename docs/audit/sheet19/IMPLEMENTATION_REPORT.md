# Sheet 19 — Implementation Report

Evidence-Backed Multi-Dimensional Comparison Framework.

## Repository and framework source

| Field | Value |
|---|---|
| Repository `HEAD` | `8171d44c40d92509f944ae6ab66194404fc05f48` |
| Framework source | `docs/.private_docs/reviews/COMPARISON_METRICS_FRAMEWORK.md` |
| Framework source SHA-256 | `b3ed2880d325f8b0fce14a48d1014cdb915e4c0f2d06ad0a6a3c5144b13b5fa4` |
| Human pack schema | `imputebench.human-evidence-pack/v2` (extended, not forked) |
| Framework schema | `imputebench.human-framework-metrics/v1` |
| Framework policy id | `human_multidimensional_relative_v1` |

## Files created

```
imputebench/read_models/framework_metrics.py
imputebench/services/results_interaction/human_export/framework_observation_service.py
imputebench/services/results_interaction/human_export/framework_metric_service.py
imputebench/services/results_interaction/human_export/framework_normalization_policy.py
imputebench/services/results_interaction/human_export/framework_table_service.py
imputebench/presentation/human_evidence/framework_charts.py
imputebench/resources/evidence_policies/algorithm_interpretability_v1.yaml
docs/Human_Evidence_Multidimensional_Framework.md
docs/Human_Evidence_Framework_Schema_V1.md
docs/audit/sheet19/BASELINE.md
docs/audit/sheet19/IMPLEMENTATION_REPORT.md
tests/human_evidence/framework/  (19 test modules + _fw helper)
```

## Files modified

```
imputebench/domain/evidence/human_export.py              (framework request options + validation)
imputebench/read_models/human_evidence_export.py         (pack field + dashboard data + manifest payload)
imputebench/services/results_interaction/human_export/human_evidence_export_service.py
imputebench/services/results_interaction/human_export/manifest_service.py
imputebench/presentation/human_evidence/dashboard_renderer.py
imputebench/presentation/human_evidence/html_template.py
imputebench/presentation/human_evidence/markdown_renderer.py
imputebench/cli/results/export_human.py
tests/human_evidence/_fakes.py                           (runtime + parameter test affordances)
```

The execution runners, primary-metric computation, `Result` rows, benchmark
contracts, mask banks, and `HumanStatisticsService` mathematics were **not**
modified.

## Architecture conformance

| Criterion | Result |
|---|---|
| `FrameworkMetricService` opens SQLite directly | no |
| Source summaries consumed | yes |
| Cohort boundaries preserved (station-scoped) | yes |
| `HumanStatisticsService` reused (Wilcoxon + rank-biserial) | yes |
| Primary metrics unchanged | yes |
| Results mutated | no |

## Scientific correctness

| Criterion | Result |
|---|---|
| Missing synthesised as 0/1 | no |
| Memory score fabricated | no (always `unavailable`) |
| Interpretability used as numeric ranking | no (qualitative policy only) |
| ARMA `p+q+1` fallback | no |
| Heterogeneous raw RMSE pooled | no (intra-cohort normalisation, then macro) |
| Arbitrary spans summed | no (canonical total only) |
| Weighted overall score | no |
| Universal-superiority claim introduced | no |

## Dimension availability (canned 2 algo × 3 mechanism × 2 rate grid)

| Dimension | Status |
|---|---|
| accuracy | available |
| speed | available (native timing) |
| stability | available |
| rate_robustness | available |
| mechanism_robustness | available |
| parameter_efficiency | unavailable (no parameter evidence in fixture) |
| memory_efficiency | unavailable (not instrumented) |

## Chart inventory

* `figures/framework/radar_profile.{png,json,md}`
* `figures/framework/accuracy_runtime_pareto.{png,json,md}`
* `figures/framework/significance/significance_<cohort>.{png,json,md}` (one per cohort)

## Fingerprint

The `FrameworkMetricsBundle.fingerprint_payload()` is folded into
`HumanPackManifest.fingerprint_payload()` under `framework_metrics`. The content
fingerprint therefore changes when the policy id, any dimension value/score, the
availability ledger, or a chart's canonical data changes, and is stable across
repeated generations (verified by `test_framework_integration.test_fingerprint_stable_across_runs`
and `test_framework_manifest.test_fingerprint_stable_for_identical_framework`).
Volatile fields (timestamps, pack id, temporary paths, render duration, data URIs)
are excluded.

## Tests

Focused (Sheet 19 §24):

```
pytest tests/human_evidence/framework -q   → 92 passed
pytest tests/human_evidence -q             → 206 passed (incl. framework)
pytest tests/results_interaction -q        → 368 passed
```

Full regression: the repository carries pre-existing, unrelated collection errors
and test-ordering failures (retired `algorithm_card_service` /
`algorithm_conformance_service` modules; `test_v5/v6/v10` evidence-dashboard
ordering pollution). These reproduce identically on the clean `HEAD` without the
Sheet 19 changes; none of the failing modules import any framework code, and every
Sheet 19 module and its dependents pass. The framework implementation introduces
**no new failures**.

## Known limitations

* The speed dimension requires admissible runtime evidence; under the default
  `allow-summary` gate, legacy `metrics.runtime_s` is descriptive-only and the
  dimension is `unavailable` unless `allow-legacy` is selected.
* Parameter efficiency is published only when results export an explicit
  `parameter_evidence` block; legacy results stay `null`.
* The radar renders all algorithms on one polar plot; true small multiples beyond
  six algorithms are noted in the caption but not yet split into separate panels.

## Deferred metrics (§12)

MAPE/R²/RMSE_peak post-hoc, peak memory, training-data efficiency, R_stress vs a
Mean baseline, any weighted overall score, and interactive Plotly charts remain out
of scope. The dashboard shows `Not available in this evidence pack` and fills no axis
for them.
