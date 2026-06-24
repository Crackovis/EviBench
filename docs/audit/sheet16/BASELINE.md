# Sheet 16 Baseline — Pre-Refactoring Snapshot

**Captured**: 2026-06-16
**HEAD SHA**: `9be4701b0da0ba249793609f8e397e4b23d531c3`
**Schema version**: v10 (current)
**CLI version**: pre-2.0 (8-domain canonical tree)

## State

- 8 canonical CLI domains: data, methods, experiment, results, study, **thesis**, admin, lab
- `thesis` is a first-class domain root with 10 subcommands
- CLI help shows `thesis` as a canonical domain
- `LEGACY_MAP` contains `"thesis": "thesis"` (self-mapped)
- `cli/__init__.py` exports thesis symbols
- Recipe lineage columns exist but are nullable
- No `RecipeLineage` domain model in `domain/results/`
- Recipe lineage resolver exists in `services/results_interaction/recipe_lineage_resolver.py`
- No `admin db-validate` command
- No `admin migrate result-lineage` command
- No `display_name_service.py`
- Human evidence export lives at `thesis export-human`

## Count expected post-refactoring

| Metric | Before | After |
|---|---|---|
| Canonical CLI domains | 8 | 7 |
| `thesis` in `--help` | yes | no |
| Recipe lineage audit | manual | automated |
| UUID visible in human export | possible | blocked |
| Standalone results allowed | yes | yes (preserved) |
| Recipe-driven lineage enforced | no | yes (new writes) |
| Backfill idempotent | N/A | yes |
