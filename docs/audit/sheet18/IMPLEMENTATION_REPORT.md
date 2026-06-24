# Sheet 18 Implementation Report

## Repository SHA

- Branch: `main`
- Baseline SHA recorded at implementation start: `a6bebe3171a3b512824bbf9fe029744eccc9200a`

## Baseline reproduction

No official LondonAQ baseline pack was generated before modifications in this workspace turn. Baseline counts are marked `not_captured` in the `BEFORE` artifacts rather than inferred or fabricated.

## Selection ledger before/after

- Before: `SELECTION_LEDGER_BEFORE.json` is `not_captured`.
- After: human packs write `provenance/selection_ledger.json`, manifest `coverage_ledger`, dashboard `coverage_ledger`, and README coverage counts.
- Selection now reads the full metadata-only descriptor set, dedupes by result id, accepts exact caps, and marks overflow explicitly.
- Source export rejects target count mismatches and truncation warnings.

## Root causes confirmed/rejected

Confirmed and remediated:

- Silent cap ambiguity at selection/source boundary.
- Public names lacking station identity.
- Recipe labels falling back to non-public mechanism/rate placeholders.
- Station storyboards comparing station predictions to full-grid originals/masks.
- Dashboard first screen missing coverage/headline context.
- Pollutant rows needing sidecar provenance.

Rejected as remedies:

- Disabling fingerprint support.
- Merging stations.
- Increasing only `--limit`.
- Replacing a legacy mechanism placeholder in HTML.
- Ignoring storyboard blocks.
- Recomputing pollutant metrics from hidden tensors.
- Duplicating Wilcoxon computation.

## Station alignment proof

Implemented `ReconstructionViewAlignmentService` to resolve dataset view mode, station id/grid coordinates, support fingerprint, and source/effective shapes. Station mode projects the full-grid original and hidden mask onto the exact `(row, col)` station. Missing station coordinates produce a prediction-only diagnostic instead of fabricated original/error panels.

Validation: `tests/results_interaction/test_reconstruction_view_alignment_service.py`.

## Temporal offset proof

The alignment service carries `support_slice_start` and `support_slice_end`, slices original/mask windows with those offsets, and writes local/absolute windows in storyboard payloads and sidecars.

Validation: `test_station_projection_uses_exact_cell_and_temporal_offsets`.

## Identity resolution

Recipe display identity resolves family/rate from recipe lineage and summary payloads, then exposes public labels such as `MCAR 30%`. Aliases include station prefix, recipe family/rate, algorithm, phase, and realization. UUIDs remain in provenance mappings only.

## Public naming audit

`rg` static audit found zero legacy mechanism-placeholder occurrences in `imputebench`, `tests/human_evidence`, and `tests/results_interaction`. Dashboard and README renderers still redact UUID-like strings before publication. The dashboard JSON island omits blocked object IDs and replaces missing/invalid source ID lists with counts; detailed IDs remain in provenance.

## Cohort and coverage policy

Cohort keys now include dataset view, station identity, dataset snapshot/view fingerprint, and recipe entry identity. Strict comparability remains station-scoped. The headline aggregate uses a balanced intersection across compatible station-scoped cohorts and exposes coverage/warnings.

## Dashboard structure

The offline dashboard now includes:

- Evidence overview cards.
- Balanced headline above detailed tables.
- Station/family/rate/algorithm/status/pollutant filters.
- Visible count, reset, and no-match state.
- Pollutant sidecar table.
- Storyboard diagnostic/scientific badges.
- Public diagnostic table.

CSP/offline escaping and no-network behavior are preserved.

## Pollutant evidence

Pollutant metrics are copied from `metrics.pollutant` sidecar data through `PollutantTableService`. No pollutant metric is reconstructed from tensors.

Validation: `tests/human_evidence/test_pollutant_table_service.py`.

## Statistics status

The existing paired Wilcoxon engine is reused. Dashboard/README expose existing test outputs, BH-adjusted p-values, status, method, claim flag, and caveats. No duplicate Wilcoxon path was added.

## README audit

README now includes coverage counts, balanced headline, pollutant sidecar links, storyboard counts by scientific/diagnostic/blocked status, statistics caveats, interpretation boundary, and provenance links.

## Offline/security validation

Dashboard renderer tests cover CSP, no external refs, no fetch, no `innerHTML`, JSON island escaping, XSS title escaping, and UUID redaction.

## Focused tests

- `pytest tests/human_evidence -q` -> 114 passed.
- `pytest tests/results_interaction/test_reconstruction_view_alignment_service.py tests/results_interaction/test_result_selection_service.py tests/human_evidence/test_source_export_service.py tests/human_evidence/test_pollutant_table_service.py -q` -> 15 passed.

## Full regression

- `pytest tests/human_evidence tests/results_interaction -q` -> 482 passed, 81 warnings.
- `pytest tests/test_sheet17_guidance_and_persistence.py tests/test_sheet17_cross_station.py tests/test_sheet17_station_runtime.py -q` -> 37 passed.
- `python -m compileall imputebench tests/human_evidence tests/results_interaction` -> passed.
- `pytest -q` was attempted and stopped during collection on pre-existing missing modules:
  `tests.cli`, `plugins.stgcn`, `scripts.audit_algorithm_design_inventory`,
  `imputebench.services.algorithm_conformance_service`,
  `imputebench.services.algorithm_card_service`, and
  `imputebench.services.algorithm_execution_truth_service`.

## Known blockers

Official before/after LondonAQ pack generation was not executed in this turn, so campaign-specific counts such as 528 selected/planned/source summaries are not claimed here.

The repository-wide `pytest -q` collection blockers listed above are outside the Sheet 18 human evidence export surface.

## Deferred work

Run the official export command from the spec in an environment with the full database/artifact store, then replace `not_captured` baseline artifacts with measured counts.
