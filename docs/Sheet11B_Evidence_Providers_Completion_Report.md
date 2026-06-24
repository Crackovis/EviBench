# Sheet 11B — Evidence Providers Completion Report

**Audited HEAD:** `3cbc8db6` (branch `main`), SQLite schema **v10**.
**Scope:** the missing last-mile execution layer for Sheet 11's evidence
control plane — executable providers, reconstruction storyboards, built-in
preset seeding, SQL-first campaign export, and benchmark-contract degradation.

---

## 1. Baseline (before)

See `docs/audit/sheet11b/BASELINE.md` and `provider_capability_matrix.json`.

- 7 providers / 37 descriptors; **0/7** overrode `export`.
- Every built-in provider raised `NotImplementedError: ... export lands in Phase 11-08`.
- Fresh-database `results evidence preset list` → **0** presets ("No presets found").
- `export-batch` hydrated the whole `results` table via `ResultService.list(None)`
  and supported only run/dataset/algorithm/execution-class/phase.

## 2. Provider capability matrix (after)

| Provider | items | executable | mechanism |
|---|---:|---:|---|
| core_result | 9 | 9 | full rich exporters (`core_result_exporters.py`) |
| training | 5 | 5 | truthful descriptor exporters (`_descriptor_export.py`) |
| temporal | 4 | 2 | descriptor exporters; `prediction_sample`/`mask_visualization` honestly `unsupported` |
| spatiotemporal | 10 | 10 | descriptor exporters |
| comparison | 7 | 7 | descriptor exporters |
| storyboard | 1 | 1 | reconstruction figure (`storyboard_exporter.py`) |
| gate | 1 | 1 | gate composition (`gate_exporter.py`) |
| **total** | **37** | **35** | — |

`EvidenceProviderRegistry.validate_export_capabilities()` returns `[]`. The two
non-executable items (`prediction_sample`, `mask_visualization`) are never
`derivable` — they are advertised `unsupported`, satisfying *derivable ⟺
executable*.

## 3. Five priority evidence implementations

| Item | Owner | Outputs |
|---|---|---|
| `result_summary` | core_result | `.md`, `.json`, `global_metrics.csv`, `pollutant_metrics.csv`, `runtime_breakdown.csv` (+ run `result_index.csv`) |
| `artifact_inventory` | core_result | `.md`, `.json`, `.csv` |
| `provenance_manifest` | core_result | `.json`, `.md` |
| `result_storyboard` | storyboard | `.png`, `.json`, `.md` (four-panel reconstruction) |
| `evidence_completeness_gate` | gate | `.json`, `.md`, `gate_checks.csv` |

## 4. Storyboard source & support

- predictions resolved through `ResultService.load_prediction_window` (bounded);
  the full-array API is never called (regression-tested).
- original resolved through `DatasetService.load_array`, sliced to the window.
- mask resolved by `ReconstructionSourceResolver` in documented priority order
  (phase-aware persisted evaluation-mask field → catalog mask record → block);
  semantics `one_means_hidden` read from the field role, never inferred from
  array density (matches the metric engine's `hidden = mask.astype(bool)`).
- error = `abs(original − imputed)` only on evaluation support; non-support is
  excluded (NaN/transparent). Original and imputed share one robust 1/99
  percentile scale; the error panel uses a 0–99th-percentile non-negative scale.
- deterministic window = first support index with ≥1 evaluated point.
- missing predictions → `missing`; missing/ambiguous mask, unknown semantics, or
  shape mismatch → `blocked` (structured, never a fabricated figure).

## 5. Preset seeding & revisions

- `build_preset_registry_service(ensure_builtins=True)` seeds the five canonical
  built-ins idempotently. Fresh DB → 5 presets; second call → no duplicate
  revisions (all stay revision 1).
- aliases `thesis_ch04 → thesis_chapter04`, `debug_full → debug_inventory`
  resolved before registry lookup; no duplicate rows.
- `thesis_chapter06` preserved.
- `debug_inventory` uses `all_derivable`, expanded by
  `EvidenceInventoryService.resolve_tokens`; the literal `all` is rejected.

## 6. SQL-first campaign export

- `export-batch` routes through `ResultSelectionService`; the hydrated
  `_select_results` path is removed (`hasattr(ev, "_select_results")` is False).
- `result_selection_queries.py` joins `algorithms`/`maskings`, projects
  `algorithm_family`/`mask_family`/`mask_rate`, and filters on
  `a.family`, `m.type`, `ABS(m.rate-?)<=1e-9`, `r.actual_execution_class`.
- `--tier` maps to `recipe_profile_id` (conflict-guarded against
  `--recipe-profile`); `--families`/`--rates` are deprecated aliases for
  `--mask-family`/`--rate` with guidance.
- The full selection report is recorded in the export manifest under
  `selection.selection`.

## 7. Benchmark-contract degradation

- canonical `EXPLORATORY ONLY` warning (verbatim) emitted whenever contract /
  mask-bank / realization / support-fingerprint identity is absent.
- `comparison_ready_signal` exports `status=exploratory_only`,
  `ranking_allowed=false`, `best_highlight_allowed=false`,
  `thesis_grade_candidate=false`, and the four `missing_fields`.
- no contract is ever inferred from mask/recipe/dataset/name similarity.
- the export manifest carries `scientific_scope=exploratory_only` and explicit
  `claim_limits` when any target lacks a contract.

## 8. Targeted tests added

`tests/results_interaction/`:
`test_provider_export_capabilities.py`, `test_result_storyboard_exporter.py`,
`test_builtin_preset_bootstrap.py`,
`test_missing_benchmark_contract_degradation.py`,
`test_evidence_batch_selection.py`, `test_sheet11b_export_integration.py`.

## 9. Regression

- `tests/results_interaction` — **365 passed**.
- `tests/test_comparison_descriptor_queries.py`, `tests/recipe_books`,
  `tests/temporal` — **148 passed**.
- `tests/test_legacy_evidence_command_guards.py`,
  `tests/test_cli_evidence_gate_lifecycle_parity.py`,
  `tests/test_cli_dl_lifecycle_evidence_export.py` — **26 passed**.
- Pre-existing, unrelated collection errors (missing modules) remain:
  `tests/spatiotemporal/test_st_plugin_device_contract.py`,
  `tests/test_ad0_algorithm_inventory_cli.py`, the SAITS-family bridge tests —
  these reference modules not present at HEAD and are outside Sheet 11B scope.

## 10. Acceptance gate matrix

| Gate | Result |
|---|---|
| built-in provider NotImplementedError paths | 0 |
| derivable descriptors without executable exporter | 0 |
| advertised formats without writer | 0 |
| priority evidence items exported | 5/5 |
| storyboard figures without sidecar | 0 |
| storyboard errors outside evaluation support | 0 |
| storyboard prediction full-array loads | 0 |
| built-in presets visible on fresh DB | 5 |
| duplicate built-in preset rows | 0 |
| requested preset aliases unresolved | 0 |
| literal ambiguous `all` accepted | 0 |
| batch exports using full-table hydration | 0 |
| family/rate filters ignored | 0 |
| batch manifests without selection report | 0 |
| missing contracts granting ranking/BEST/thesis | 0 |
| exports without manifest / SHA-256 / source id | 0 |
| legacy command regressions | 0 |

## 11. Known unsupported evidence types

`prediction_sample`, `mask_visualization` (temporal) remain honestly
`unsupported`. Non-priority training/temporal/ST/comparison items are exported
as truthful descriptor evidence (JSON + Markdown) pointing at the canonical
domain renderers (`experiment temporal evidence`, `experiment st evidence`,
`results compare`), with explicit claim boundaries.
