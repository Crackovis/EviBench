# Migration Guide — `imputebench thesis` → Canonical Domains

**Version**: 1.x → 2.0  
**Effective**: Sheet 16 (June 2026)  
**Removal**: ImputeBench 2.0

## Summary

The `imputebench thesis` command group has been deprecated. Its subcommands
have been redistributed across the canonical 7-domain CLI tree:

| Domain | Canonical commands |
|---|---|
| `data` | dataset characterize, masking characterize |
| `methods` | algorithm characterize |
| `results` | export-human, evidence export-batch, gate, compare |
| `experiment` | st evidence |

## Migration Table

| Old command | New command | Differences | Legacy defaults | Removal |
|---|---|---|---|---|
| `imputebench thesis export-human` | `imputebench results export-human` | Same options, same DTOs, canonical provenance | Same | 2.0 |
| `imputebench thesis dataset` | `imputebench data dataset characterize` | Generic guide (no Chapter 04 references), generic output paths | `docs/exports/thesis/chapter04/` → `docs/exports/evidence/datasets/` | 2.0 |
| `imputebench thesis missingness` | `imputebench data masking characterize` | Profile aliases renamed (`thesis_core`→`core`), generic paths | `docs/exports/thesis/chapter04/` → `docs/exports/evidence/missingness/` | 2.0 |
| `imputebench thesis algorithms` | `imputebench methods algorithm characterize` | `configuration_field_count` replaces parameter count claims, generic paths | `docs/exports/thesis/chapter04/` → `docs/exports/evidence/algorithms/` | 2.0 |
| `imputebench thesis training` | `imputebench results evidence export-batch --preset training_evidence` | Preset-driven, same underlying service | Same | 2.0 |
| `imputebench thesis compare` | `imputebench results compare` + `imputebench results evidence export` | Split into ranking + export phases | Same | 2.0 |
| `imputebench thesis gate` | `imputebench results gate` / `imputebench results evidence validate-pack` | Scientific gate vs structural audit | Same | 2.0 |
| `imputebench thesis st` | `imputebench experiment st evidence` | Already canonical; thesis st was a wrapper | Same | 2.0 |
| `imputebench thesis all` | No direct public replacement | Private orchestrator; compose canonical commands | N/A | 2.0 |

## Compatibility Window (1.x)

During the 1.x series:
- `imputebench thesis ...` remains invocable via a **hidden** facade
- Each invocation emits a deprecation warning on stderr
- The warning can be suppressed with `--no-deprecation-warnings` or `IMPUTEBENCH_NO_DEPRECATION_WARNINGS=1`
- The `thesis` group does NOT appear in `--help` or shell completion

## Output Path Changes

New canonical commands use generic output paths:

| Old path | New path |
|---|---|
| `docs/exports/thesis/chapter04/01_dataset_characterization/` | `docs/exports/evidence/datasets/` |
| `docs/exports/thesis/chapter04/02_missingness_characterization/` | `docs/exports/evidence/missingness/` |
| `docs/exports/thesis/chapter04/03_algorithm_lifecycle/` | `docs/exports/evidence/algorithms/` |
| `docs/exports/thesis/chapter04/04_comparisons/` | `docs/exports/evidence/comparisons/` |
| `docs/exports/thesis/chapter06/` | `docs/exports/evidence/st/` |

Legacy facade commands preserve old defaults for backward compatibility.
The manifest includes `legacy_default_paths_used: true` in such cases.

## New Commands Added

| Command | Domain | Purpose |
|---|---|---|
| `imputebench admin db-validate` | admin | Database lineage hygiene audit |
| `imputebench admin migrate result-lineage` | admin | Transactional recipe lineage backfill |
| `imputebench results evidence validate-pack` | results | Structural audit of exported evidence packs |
