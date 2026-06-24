# Sheet 15 — Audit Baseline (15-00)

Read-only capture of the surfaces the Human-Readable Evidence Export layer
builds on top of. No existing engine is replaced.

## Commit baseline

- Branch: `main`
- Surfaces observed at revisions `61b9a428` and `bc67986c`.

## Existing technical export engine (reused, never duplicated)

- `imputebench/services/results_interaction/export_engine.py` — `ExportEngine`
  implements the atomic 9-step publication sequence: validate → stage → invoke
  providers → verify → SHA-256 → `manifest.json` → atomic publish → catalog →
  report. Overwrite policies: `fail`, `reuse-identical`, `replace-generated`.
- `imputebench/services/results_interaction/export_planner.py` — `ExportPlanner`
  (`plan_single`, `plan_batch`) categorises items planned / skipped / blocked.
- Source manifest schema: `imputebench.evidence-export/v1`
  (`imputebench/read_models/results_interaction/export.py`).

## SQL-first selection (reused)

- `imputebench/read_models/results_interaction/selection.py` —
  `ResultSelectionQuery`, `ResultSelectionReport`.
- `imputebench/services/results_interaction/selection_service.py` —
  `ResultSelectionService.select(query)`; same semantics as
  `results evidence export-batch`. No directory scan is used to select results.

## Provider sidecars (read, never re-rendered)

- `core_result_exporters.py` writes JSON sidecars:
  `imputebench.result-summary/v1`, `imputebench.artifact-inventory/v1`,
  `imputebench.provenance-manifest/v1`, `imputebench.comparison-ready-signal/v1`.
- `storyboard_exporter.py` writes the four-panel PNG + JSON sidecar
  (`imputebench.result-storyboard/v1`) + Markdown companion; blocks missing /
  ambiguous sources, watermarks mocks, restricts the error panel to evaluated
  support, and exports identity + support + claim limits.

## Comparability + identity

- `imputebench/domain/results/eligibility_policy.py` —
  `evaluate_comparison_eligibility(ResultDescriptor, ...)` controls dataset,
  benchmark contract, mask bank, semantic phase, support fingerprint,
  realization, family, metrics, mock, and DL evidence.
- `imputebench/domain/results/descriptor.py` — `ResultDescriptor.from_result`.
- `imputebench/services/results_interaction/providers/_projections.py` —
  identity / benchmark / runtime / provenance projections + partial human path
  helpers (`compute_human_readable_target_path`).

## Context

- `EvidenceContext` (`evidence_provider_registry.py`) gives memoised,
  metadata-first access to `result(id)`, `run(id)`, `dataset(id)`,
  `algorithm(id)`, `masking(id)`, `results_for_run(id)` — the access path the
  human layer reuses; it never loads arrays.

## Statistics

- No runtime Wilcoxon service exists in the export path; `scipy` is not a core
  dependency. Statistics are therefore an **optional, gated** capability behind
  the `evidence-stats` extra.

## Corrections to the initial brief (never encoded)

- `LinearInterpolation: RMSE 2.4`, `DACPI: RMSE 2.1`, `p = 0.008`,
  "structural issues", "innovation" are **not facts** and must never be
  hard-coded. With five pairs, an exact two-sided Wilcoxon without ties cannot
  produce `p < 0.0625`; the dashboard writes "lower RMSE", not `A > B`.
