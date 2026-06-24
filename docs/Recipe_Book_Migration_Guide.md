<!-- NAV:START -->
> [🏠 Home](../README.md) · 📍 **Recipe_Book_Migration_Guide**

<details>
<summary>🗺️ Documentation Map</summary>

- [📂 README](../README.md)
- 📚 **docs/**
  - [🏗️ Architecture_Overview](Architecture_Overview.md)
  - [📝 Recipe_Book_Architecture](Recipe_Book_Architecture.md)
  - [📝 Recipe_Book_Schema_V1](Recipe_Book_Schema_V1.md)
  - [💻 Recipe_Book_CLI_Reference](Recipe_Book_CLI_Reference.md)
  - [💻 Recipe_Book_Migration_Guide](Recipe_Book_Migration_Guide.md) ← *you are here*
  - [📝 Recipe_Book_Architecture_Update_Report](Recipe_Book_Architecture_Update_Report.md)
  - [💻 CLI_Reference](CLI_Reference.md)
  - [💻 Migration_Guide_CLI_First_Temporal](Migration_Guide_CLI_First_Temporal.md)
  - [💻 Temporal_Experiment_CLI_Reference](Temporal_Experiment_CLI_Reference.md)

</details>
<!-- NAV:END -->

# Recipe Book Migration Guide

This document describes the migration from the previous experiment configuration
system (Python builder functions and markdown-based ST loading) to the new
registry-backed recipe book architecture.

---

## What Changed

| Before | After |
|---|---|
| Python builder functions in `builtin_recipes.py` | Declarative YAML resources under `imputebench/resources/recipe_books/` |
| Markdown-based ST recipe loading | Unified `imputebench.recipe-book/v1` schema |
| Experiment configs as ephemeral Python objects | Immutable SQLite revisions with version history |
| No interoperability format | YAML/JSON interchange with schema validation |
| Ad-hoc CLI per lane | Shared CLI: `imputebench experiment recipe` |
| No concurrency control | Optimistic concurrency via `--expected-revision` |

---

## Migrated Built-in Books

Four built-in recipe books were migrated from Python builders to YAML resources.

| Book ID | Source (Before) | Resource Path (After) |
|---|---|---|
| `official_londonaq_temporal_dl` | `builtin_recipes.py: build_londonaq_temporal_dl()` | `resources/recipe_books/official_londonaq_temporal_dl.yaml` |
| `official_londonaq_temporal_classical` | `builtin_recipes.py: build_londonaq_temporal_classical()` | `resources/recipe_books/official_londonaq_temporal_classical.yaml` |
| `official_londonaq_temporal_baselines` | `builtin_recipes.py: build_londonaq_temporal_baselines()` | `resources/recipe_books/official_londonaq_temporal_baselines.yaml` |
| `official_londonaq_st` | `markdown` → Python loader | `resources/recipe_books/official_londonaq_st.yaml` |

Each YAML resource follows the `imputebench.recipe-book/v1` schema and is validated
at build time against `recipe_book_v1.schema.json`.

---

## Schema Migration v7 to v8

The recipe book migration coincides with the database schema migration from version
7 to version 8.

| Schema Version | Tables Added |
|---|---|
| v7 | N/A (pre-recipe-book schema) |
| v8 | `recipe_books`, `recipe_book_revisions`, `recipe_book_entries`, `recipe_book_algorithms` |

The migration adds four tables and eight indexes for efficient lookup by book ID,
revision number, domain, kind, status, and algorithm reference.

Run the migration:

```bash
imputebench admin migrate recipe-books --seed-builtins
```

This command:
1. Creates the v8 tables and indexes if they don't exist.
2. Seeds the four built-in YAML resources into the registry.
3. Assigns revision `1` to each seeded book.
4. Sets `claim_scope: builtin` to prevent direct modification.

---

## Dual-Read Transition Period

During the transition period, both the new registry and the legacy loading paths
are available. The resolution order is:

```
1. Recipe Book Registry (SQLite)     ← canonical path
2. Package YAML resources            ← fallback (built-ins not yet seeded)
3. Legacy Python builders / markdown ← last-resort fallback
```

This dual-read approach ensures that existing workflows continue to function while
operators migrate to the new system. The temporal lane's `--recipe-book` flag and
the ST lane's materialization commands resolve against this chain automatically.

### Detecting Which Path Was Used

When a recipe book is resolved from a legacy fallback, a deprecation warning is
emitted to stderr:

```
[WARNING] Recipe book 'official_londonaq_temporal_dl' resolved from legacy builder.
          Run 'imputebench admin migrate recipe-books --seed-builtins' to migrate.
```

A counter tracks legacy fallback invocations. The goal is zero fallback invocations
before cutover.

---

## Cutover: Zero Legacy Fallback

The cutover is complete when:

1. All four built-in books are seeded in the registry.
2. The `imputebench admin migrate recipe-books --verify` command reports zero drift.
3. No legacy fallback invocations occur in any test suite or production workflow.
4. All temporal and ST lane commands resolve recipe books exclusively from the registry.

At this point, the legacy builder functions and markdown loaders can be deprecated
and scheduled for removal in a future release.

---

## Protected Built-ins: Clone to Customize

Built-in books have `claim_scope: builtin`. They cannot be modified or deleted
directly. To customize a built-in book:

```bash
# 1. Clone the built-in book
imputebench experiment recipe clone official_londonaq_temporal_dl --id my_custom_dl

# 2. Inspect the cloned book
imputebench experiment recipe show my_custom_dl

# 3. Export the clone to a YAML file for editing
imputebench experiment recipe export my_custom_dl --output my_custom_dl.yaml

# 4. Edit my_custom_dl.yaml as needed

# 5. Update the book with your changes
imputebench experiment recipe update my_custom_dl --file my_custom_dl.yaml --expected-revision 1

# 6. Materialize entries
imputebench experiment recipe materialize my_custom_dl --expected-revision 2
```

This workflow preserves the original built-in as an unmodified reference while
allowing full customization of the clone.

---

## Backward Compatibility

### Temporal Lane

All existing temporal lane commands remain unchanged. The `--recipe-book` flag
continues to accept book identifiers. Under the hood, the resolution chain
(registry → YAML resource → legacy builder) is transparent to the user.

```bash
# Still works as before
imputebench experiment temporal experiment run --recipe-book official_londonaq_temporal_dl --tier smoke
imputebench experiment temporal prepare materialize --recipe-book official_londonaq_temporal_dl
```

### Spatiotemporal Lane

All existing ST lane commands remain unchanged. The materialization path
automatically resolves recipe books through the new registry.

```bash
# Still works as before
imputebench experiment st experiment run --tier smoke --dataset-id london_aq
```

---

## Verification Checklist

After migration, verify with these commands:

```bash
# 1. Confirm all four built-in books are seeded
imputebench experiment recipe list --claim-scope builtin
# Expected: 4 books

# 2. Verify built-in books match YAML resources
imputebench admin migrate recipe-books --seed-builtins --verify
# Expected: exit code 0, "All 4 built-in books match bundled resources"

# 3. Show a built-in book's revision history
imputebench experiment recipe history official_londonaq_temporal_dl
# Expected: at least revision 1 with "seed" action

# 4. Validate a YAML export round-trips correctly
imputebench experiment recipe export official_londonaq_temporal_dl --output /tmp/test.yaml
imputebench experiment recipe validate /tmp/test.yaml
# Expected: exit code 0, validation passed

# 5. Run the test suite
pytest tests/ -k recipe_book
# Expected: 73 tests passing, 0 legacy fallback warnings
```

<!-- FOOTER:START -->

---

> [← Recipe_Book_CLI_Reference](Recipe_Book_CLI_Reference.md) · [⬆ Top](#) · [🏠 Home](../README.md) · [Recipe_Book_Architecture_Update_Report →](Recipe_Book_Architecture_Update_Report.md)
<!-- FOOTER:END -->
