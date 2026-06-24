# Sheet 11 — Phase 11-00 Re-audit & Golden Freeze

**Status:** complete
**Date:** 2026-06-14
**Audited code state:** `efc6df0351e7bd946209501a60dd910c6064fecb` (`Recipe Book
Architecture Redesign`) — identical to the active worktree at HEAD
`2b2ad9ac` (the only delta since baseline is the Sheet 11 spec file itself,
confirmed by `git diff --name-only efc6df03 HEAD`).
**Live SQLite schema version:** `8` (declared `SCHEMA_VERSION = 8`, live
`schema_version` table = 8).

Golden outputs are frozen under [`docs/audit/sheet11_golden/`](sheet11_golden/)
via the read-only harness
[`scripts/audit/sheet11_golden_freeze.py`](../../scripts/audit/sheet11_golden_freeze.py).
The harness invokes `--help` for every command contract Sheet 11 touches plus a
curated set of read-only `list`/`show` calls. It never runs an exporter, gate,
or mutation.

## 1. Eight visible root domains (acceptance gate)

`imputebench --help` lists exactly eight root domains:

```
admin   data   experiment   lab   methods   results   study   thesis
```

Sheet 11 adds `inspect` and `evidence` and extends `compare` **inside the
existing `results` root**. No ninth root domain is introduced.

## 2. Live data baseline

| table | rows |
|-------|------|
| runs | 178 |
| results | 855 |
| comparison_specs | 0 |
| artifact_records | 964 |

`results.recipe_book_id` column present: **false** — confirms §1.13 of the
sheet (recipe lineage is not yet explicit on Run/Result; Phase 11-02 must add
it). Representative golden ids: run `3d02b53a-294f-4db8-91e1-f0a4d39e68c7`
(classical LinearInterpolation), result
`7dc94779-26f3-4f9c-a760-72380cb2150f`.

## 3. Frozen contracts (each has a `--help` golden + caller note)

### results result (group: `imputebench/cli/results/result.py`)
- `list` — table (ID/Run/Algorithm/Masking/MAE/RMSE/Runtime). **Only** `--run`
  option; **no `--limit`** (freeze harness confirmed `--limit` is rejected).
- `show RESULT_ID` — raw `Result.to_dict()` JSON. **Preserved as raw canonical
  surface**; `results inspect` is the new human/scientific projection.
- `delete`, `bulk-delete` (dry-run + confirm-token), `export-bundle`
  (`BundleExporter`), `spatial-show`, `export-spatial`.
- `export-training-evidence [RESULT_ID] [--run] [--phase] [--algorithm-id]
  [--output-dir]` — single-result and run-batch training packs (pack JSON,
  curve CSV/PNG, checkpoint selection, runtime summary, manifest, cohort pack).
  Reusable helpers (`_export_training_artifacts`, `_checkpoint_payload`,
  `_runtime_payload`, curve writers) live **inline** in the CLI module and must
  move into the training evidence provider in Phase 11-09 while preserving file
  names and default paths (`THESIS_LIFECYCLE_DIR/training_evidence`).

### results compare (group: `imputebench/cli/results/compare.py`)
- `list` / `show COMPARISON_ID` / `delete COMPARISON_ID` — manage persisted
  `ComparisonSpec` via `StudioManagerService`. **Preserved exactly.** This is a
  Click *group*, so interactive comparison is added through new subcommands
  (`results`, `runs`, `query`, `table`) — never an ambiguous
  `results compare <id1> <id2>`.

### experiment run (group: `imputebench/cli/experiment/runs.py`)
- `run show RUN_ID --format table|json [--include-results/--no-include-results]`
  — operational aggregate view. `results inspect RUN_ID` enriches scientific
  interpretation but does **not** replace it. No run-level "best algorithm".

### experiment temporal evidence (`imputebench/cli/experiment/temporal/evidence.py`)
- `export` / `inspect` — directory-oriented temporal pack
  (`TemporalEvidenceExportService`). Authoritative for temporal generation;
  the shared inventory must represent it honestly and not mark
  `prediction_sample`/`mask_visualization` `derivable` without a concrete
  provider (§1.9, §9.4).

### experiment st evidence (`imputebench/cli/experiment/st/evidence.py`)
- `export` / `mirror` / `audit` / `visual-audit` — chapter-grade ST workflow
  (`STScientificEvidenceExportService`, `STExportAuditService`,
  `VisualAuditService`). Authoritative and unchanged; the ST provider wraps
  these, never bypasses the ST scientific gates.

### Supporting machinery confirmed present (reuse targets)
- `services/comparison/benchmark_parity_service.py` —
  `evaluate_shared_benchmark_parity()` returns `shared_benchmark_confirmed`,
  `mixed_contracts`, `mixed_mask_banks`, `mixed_evaluation_support`,
  `not_evaluated`. **The interactive-comparison compatibility lane must surface
  these, not bypass them with an ad-hoc join.**
- `services/comparison/compatibility.py`, `view_model.py` (ComparisonViewModel),
  `studio_manager_service.py`, `spec_builder.py`.
- `ArtifactCatalogService` / `artifact_records` table with checksum, claim
  relevance, repair status, `is_purgeable`.
- `RuntimeTimingCohortService` / `runtime_timing_spans` with native span,
  runtime summary, legacy, backfilled, missing source quality.
- `recipe_materializations` / `recipe_materialization_objects` tables (Sheet 10)
  — the authoritative lineage source for Phase 11-02 backfill.

## 4. Persistence audit (informs Phase 11-02)

- `SCHEMA_VERSION = 8` in `imputebench/storage/sqlite/schema.py`; migrations in
  `imputebench/storage/sqlite/migrations.py` end at `_migrate_to_8`. **Next
  migration is version 9.**
- `results` and `runs` tables carry full benchmark identity columns but **no**
  `recipe_materialization_id / recipe_book_id / recipe_revision /
  recipe_entry_id / recipe_profile_id / recipe_definition_sha256`.
- `ResultPersistenceRow` / `map_result_to_row`
  (`imputebench/persistence/mappers/result_mapper.py`) map explicit columns plus
  a full `payload_json`. New lineage fields need: (a) model fields, (b) mapper
  columns, (c) INSERT column list + row decode, (d) schema columns + indexes,
  (e) migration `_migrate_to_9`.

## 5. Architectural decisions ratified (from the implementation directive)

- Keep exactly eight visible root domains.
- Add `results inspect`; add `results evidence`; **extend** (not replace) the
  existing `results compare` group; preserve `compare list/show/delete`.
- Interactive commands are `compare results|runs|query|table`. No ambiguous
  `results compare <id1> <id2>` parser.
- No separate `results export`; curated export is `results evidence
  export/export-batch`; reproducibility bundles stay `results result
  export-bundle`.
- Retain temporal and ST scientific exporters as domain authorities.

## Exit gate

Every current contract and caller in the mandatory freeze list is represented
by a golden `--help` capture and, where data exists, a read-only output. The
eight-root invariant, schema version, and recipe-lineage gap are all recorded.
**Phase 11-00 is closed.**
