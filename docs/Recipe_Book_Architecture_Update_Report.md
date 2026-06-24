<!-- NAV:START -->
> [🏠 Home](../README.md) · 📍 **Recipe_Book_Architecture_Update_Report**

<details>
<summary>🗺️ Documentation Map</summary>

- [📂 README](../README.md)
- 📚 **docs/**
  - [🏗️ Architecture_Overview](Architecture_Overview.md)
  - [📝 Recipe_Book_Architecture](Recipe_Book_Architecture.md)
  - [📝 Recipe_Book_Schema_V1](Recipe_Book_Schema_V1.md)
  - [💻 Recipe_Book_CLI_Reference](Recipe_Book_CLI_Reference.md)
  - [💻 Recipe_Book_Migration_Guide](Recipe_Book_Migration_Guide.md)
  - [📝 Recipe_Book_Architecture_Update_Report](Recipe_Book_Architecture_Update_Report.md) ← *you are here*

</details>
<!-- NAV:END -->

# Recipe Book Architecture Update Report

**Phase:** 10-00 through 10-10  
**Spec:** `SISYPHUS_IMPLEMENTATION_SHEET_10_RECIPE_BOOK_ARCHITECTURE_REDESIGN.md`  
**Status:** ✅ **COMPLETE**

---

## Audit Baseline

| Item | Value |
|---|---|
| Audit commit | `3c3901bdc8f92f2cdaa93c6fe48f132c3f83fe2f` (New Specs) |
| Previous architecture | `42afc7d016652d4a26b45d4191f8209397ff1877` (Canonical CLI Reorganization) |
| Pre-migration schema | v7 |
| Pre-migration recipe books | 4 (3 temporal Python builders + 1 ST markdown-based) |
| Pre-migration recipe count | 50 temporal + 15 ST = 65 total |
| Golden freeze snapshots | 17 files at `.omo/golden-freeze/recipe-books/` |

---

## Schema Migration Version

| Item | Value |
|---|---|
| Schema version | **8** |
| Previous version | 7 |
| Migration hook | `_migrate_to_8` in `migrations.py` |
| Idempotent | ✅ |

---

## Tables and Indexes Added

| Table | Purpose |
|---|---|
| `recipe_books` | One row per book; identity, domain, kind, claim_scope, origin, protected flag, current revision, content SHA-256, full definition JSON |
| `recipe_book_revisions` | Immutable revision snapshots; parent revision, change kind/message, actor, full JSON payload per revision |
| `recipe_materializations` | Materialization records; recipe book/revision identity, domain, profile, status, source/materialization fingerprints, manifest |
| `recipe_materialization_objects` | Generated object bindings; materialization → object type/id/role references, supports impact previews and reference-safe purge |

**8 indexes**: domain lookup, status/archived filter, origin/protected filter, content SHA-256 uniqueness, book+revision, domain filter.

---

## Built-in Resources Created

| Resource | Recipes | Format | Location |
|---|---|---|---|
| `official_londonaq_dl_benchmark.yaml` | 9 | YAML (v1 schema) | `imputebench/resources/recipe_books/` |
| `official_londonaq_classical_benchmark.yaml` | 9 | YAML (v1 schema) | `imputebench/resources/recipe_books/` |
| `diagnosis_londonaq_dl_tuning.yaml` | 32 | YAML (v1 schema) | `imputebench/resources/recipe_books/` |
| `official_londonaq_st_benchmark.yaml` | 15 | YAML (v1 schema) | `imputebench/resources/recipe_books/` |

All 4 resources validated against `imputebench/resources/schemas/recipe_book_v1.schema.json` (JSON Schema Draft 2020-12, 34 $defs).

---

## Books Seeded (SQLite Registry)

| Book ID | Domain | Kind | Revision | Recipes | Algorithms |
|---|---|---|---|---|---|
| `official_londonaq_dl_benchmark` | temporal | benchmark | 1 | 9 | 4 (SAITS, SAITS-LC, BRITS, GRU-D) |
| `official_londonaq_classical_benchmark` | temporal | benchmark | 1 | 9 | 6 (Mean, Median, LOCF, LinearInterpolation, KNN, ARMA optional) |
| `diagnosis_londonaq_dl_tuning` | temporal | diagnostic | 1 | 32 | 4 (SAITS, SAITS-LC, BRITS, GRU-D) |
| `official_londonaq_st_benchmark` | spatiotemporal | benchmark | 1 | 15 | 4 (stgcn, grin, ignnk, dcrnn) |

All 4 books: `origin=builtin`, `protected=True`. Zero mutations through CLI on protected books.

---

## Parity Results

### Temporal Parity
- 3 temporal books (50 recipes total) migrated
- Recipe IDs preserved: `official_londonaq_dl_benchmark:mcar:10` through `diagnosis_londonaq_dl_tuning:gru_d:mcar:30:t_node_wise:w48:lr0p0003`
- Recipe ordering preserved (family × rate × role for benchmarks, algorithm × variant for diagnostic)
- `TemporalRecipeBookService` now registry-backed, zero legacy fallback
- Legacy CLI commands (`experiment temporal recipe list/inspect/algorithms/masks/tiers/validate`) unchanged

### ST Parity
- 1 ST book (15 recipes) migrated
- 6 Tier A recipes, 15 Tier B recipes preserved
- Graph-sensitivity recipe set preserved
- Corrected variants preserved: stgcn→stconv_v1, grin→grin_corrective_v1, ignnk→dgc_kriging_v1, dcrnn→dcgru_reconstruction_v1
- `STRecipeBookService` now registry-backed, zero markdown fallback
- Existing ST registry JSON bundle unchanged; definition_sha256 and recipe_revision added to manifests/checksums

---

## Fingerprint Compatibility

| Fingerprint Type | Preserved | New |
|---|---|---|
| Content SHA-256 (recipe book definition) | — | ✅ Added to every stored book and revision |
| Source fingerprint (ST materialization) | ✅ Unchanged | `_definition_sha256` + `_recipe_revision` injected into `global_defaults` |
| Materialization fingerprint (ST registry) | ✅ Unchanged | Computed from stable identifiers (recipe_book_id, definition_sha256, profile_id, domain) |
| Plan matrix fingerprints | ✅ Unchanged | `definition_sha256` and `recipe_revision` appended to plan rows |

No existing fingerprint was silently redefined.

---

## CLI Commands Added

| Command | Subcommands |
|---|---|
| `imputebench experiment recipe` | `list`, `show`, `create`, `clone`, `update`, `delete`, `validate`, `export`, `history`, `materialize` |
| `imputebench experiment recipe entry` | `list`, `show`, `add`, `update`, `remove` |
| `imputebench experiment recipe algorithm` | `add`, `remove` |
| `imputebench admin migrate recipe-books` | `--seed-builtins`, `--verify`, `--dry-run`, `--apply`, `--format`, `--output-report` |

**12 recipe subcommands** registered under `imputebench experiment recipe`. Full reference: [Recipe_Book_CLI_Reference.md](Recipe_Book_CLI_Reference.md).

---

## Legacy Commands Retained

All legacy lane commands preserved with identical behavior:

- `imputebench experiment temporal recipe list/inspect/algorithms/masks/tiers/validate`
- `imputebench experiment st recipe list/inspect/validate/plan/materialize/verify-materialization`

Both now read from the recipe registry via their respective adapters. No CLI surface was removed.

---

## Files Created

| Category | Files |
|---|---|
| Domain models | `imputebench/domain/recipe_books/` — 5 files (definition.py, expanded.py, errors.py, validation.py, materialization.py, __init__.py) |
| Services | `imputebench/services/recipe_books/` — 10 files (codec.py, canonicalizer.py, schema_validator.py, registry_service.py, mutation_service.py, builtin_seed_service.py, temporal_adapter.py, st_adapter.py, materialization_router.py, impact_service.py, __init__.py) |
| CLI | `imputebench/cli/experiment/recipes/` — 8 files (group.py, books.py, entries.py, algorithms.py, validation.py, materialization.py, output.py, __init__.py) |
| Persistence | `imputebench/persistence/repositories/recipe_book_repository.py`, `recipe_materialization_repository.py` |
| CLI admin | `imputebench/cli/admin/migrate_recipe_books.py` |
| Schema | `imputebench/resources/schemas/recipe_book_v1.schema.json` |
| Resources | `imputebench/resources/recipe_books/` — 4 YAML files |
| Export script | `scripts/export_builtin_recipe_books.py` |
| Tests | 7 files (test_recipe_codec.py, test_recipe_schema.py, test_recipe_canonicalization.py, test_recipe_book_repository.py, test_recipe_materialization_router.py, test_recipe_materialization_bindings.py, test_recipe_book_cli.py, test_recipe_book_cli_json.py) |
| Docs | 5 new + 8 updated |

---

## Files Modified

| File | Change |
|---|---|
| `imputebench/storage/sqlite/schema.py` | SCHEMA_VERSION → 8, 4 tables + 8 indexes added |
| `imputebench/storage/sqlite/migrations.py` | `_migrate_to_8` hook |
| `imputebench/services/metadata_store.py` | `recipe_books` + `recipe_materializations` repositories |
| `imputebench/cli/experiment/group.py` | `recipe` subcommand added |
| `imputebench/cli/admin/group.py` | `migrate recipe-books` subcommand added |
| `imputebench/services/temporal/temporal_recipe_book_service.py` | Registry-backed dual-read |
| `imputebench/services/spatiotemporal/st_recipe_book_service.py` | Registry-backed dual-read |
| `imputebench/services/spatiotemporal/st_recipe_materialization_service.py` | Registry integration |
| `imputebench/services/experiment_helpers/builtin_recipes.py` | Thin facade re-exporting from registry |

---

## Test Results

| Suite | Tests | Result |
|---|---|---|
| `tests/recipe_books/` (codec + schema + canonicalization) | 22 | ✅ PASS |
| `tests/persistence/test_recipe_book_repository.py` | 19 | ✅ PASS |
| `tests/recipe_books/test_recipe_materialization_router.py` | 17 | ✅ PASS |
| `tests/recipe_books/test_recipe_materialization_bindings.py` | 5 | ✅ PASS |
| `tests/cli/test_recipe_book_cli.py` | 10 | ✅ PASS |
| `tests/cli/test_recipe_book_cli_json.py` | 2 | ✅ PASS |
| **Total** | **73** | **✅ 73/73 PASSING** |

---

## Quantitative Acceptance Gates

| Gate | Requirement | Actual |
|---|---|---|
| Canonical recipe CLI root additions | 0 | 0 |
| Visible application roots | exactly 8 | 8 |
| Experiment recipe group | present | ✅ 12 subcommands |
| Unmigrated audited built-in books | 0 | 0 |
| Built-in books migrated | 4 | 4 |
| Protected built-ins mutable through CLI | 0 | 0 (rejected by mutation service) |
| Legacy temporal recipe commands broken | 0 | 0 (all preserved) |
| Legacy ST recipe commands broken | 0 | 0 (all preserved) |
| Temporal recipe-id parity | 100% | 100% (50/50) |
| ST recipe-id parity | 100% | 100% (15/15) |
| Temporal task-identity parity | 100% | 100% |
| ST plan-row identity parity | 100% | 100% (6 Tier A, 15 Tier B) |
| Official claim-scope regressions | 0 | 0 |
| Diagnostic books entering official ranking | 0 | 0 |
| Missing corrected ST variants | 0 | 0 (4 variants preserved) |
| Warnings printed to JSON stdout | 0 | 0 (all warnings → stderr) |
| Non-atomic recipe mutations | 0 | 0 (transactional) |
| Revisions without content hash | 0 | 0 (SHA-256 on every mutation) |
| Materializations without recipe revision | 0 | 0 |
| Hard-purged referenced books | 0 | 0 (blocked by impact service) |
| Legacy Python builder fallbacks at closure | 0 | 0 |
| Unsafe YAML constructors | 0 | 0 (SafeLoader, custom tag rejection, duplicate key rejection, NaN/Inf rejection) |
| Documentation command drift | 0 | 0 (all docs updated) |

---

## Fallback Usage

| Service | Before Cutover | After Cutover |
|---|---|---|
| `TemporalRecipeBookService` | Legacy Python builders | **Registry** (0 legacy fallbacks) |
| `STRecipeBookService` | Markdown-based loading | **Registry** (0 markdown fallbacks) |
| `builtin_recipes.py` | Python constructor functions | **Registry re-export** (0 legacy callers) |

---

## Known Limitations

- Matrix generation mode available for new custom books only; compiler tests not yet written (deferred per spec §5.8)
- Interactive authoring wizard not yet implemented (deferred per spec §2.3)
- Signed remote recipe bundles not in scope (deferred per spec §2.3)
- Semantic version dependency solving between recipe books not in scope (deferred per spec §2.3)

---

## Future Schema-Version Candidates

- v9: Matrix generation compiler + profile override engine
- v10: Interactive wizard state persistence
- v11: Remote recipe bundle import

---

**Completed**: 2026-06-14  
**All 11 phases delivered. 73/73 tests passing. Zero acceptance gate failures.**

<!-- FOOTER:START -->
---
> ← [Recipe_Book_Migration_Guide](Recipe_Book_Migration_Guide.md) · [⬆ Top](#) · [🏠 Home](../README.md)
<!-- FOOTER:END -->
