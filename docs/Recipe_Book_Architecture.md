<!-- NAV:START -->
> [🏠 Home](../README.md) · 📍 **Recipe_Book_Architecture**

<details>
<summary>🗺️ Documentation Map</summary>

- [📂 README](../README.md)
- 📚 **docs/**
  - [🏗️ Architecture_Overview](Architecture_Overview.md)
  - [🔍 Audit_Guide](Audit_Guide.md)
  - [⚙️ Canonical_Workflow](Canonical_Workflow.md)
  - [💻 CLI_Reference](CLI_Reference.md)
  - [📝 Recipe_Book_Architecture](Recipe_Book_Architecture.md) ← *you are here*
  - [📝 Recipe_Book_Schema_V1](Recipe_Book_Schema_V1.md)
  - [💻 Recipe_Book_CLI_Reference](Recipe_Book_CLI_Reference.md)
  - [💻 Recipe_Book_Migration_Guide](Recipe_Book_Migration_Guide.md)
  - [📝 Recipe_Book_Architecture_Update_Report](Recipe_Book_Architecture_Update_Report.md)
  - [🔧 Experiment_Helpers](Experiment_Helpers.md)
  - [⚙️ Experiment_Runs_Benchmark_Contract_Workflow](Experiment_Runs_Benchmark_Contract_Workflow.md)
  - [⚙️ Experiment_Runs_Classical_Workflow](Experiment_Runs_Classical_Workflow.md)
  - [⚙️ Experiment_Runs_DL_Workflow](Experiment_Runs_DL_Workflow.md)
  - [📖 Introduction](Introduction.md)
  - [💻 Migration_Guide_CLI_First_Temporal](Migration_Guide_CLI_First_Temporal.md)
  - [🏷️ Operational_Labels](Operational_Labels.md)
  - [💻 ST_Experiment_CLI_Philosophy](ST_Experiment_CLI_Philosophy.md)
  - [💻 Study_CLI](Study_CLI.md)
  - [💻 Temporal_Experiment_CLI_Philosophy](Temporal_Experiment_CLI_Philosophy.md)
  - [💻 Temporal_Experiment_CLI_Reference](Temporal_Experiment_CLI_Reference.md)
  - [⚙️ Thesis_Workflow](Thesis_Workflow.md)

</details>
<!-- NAV:END -->

# Recipe Book Architecture

Recipe books are the declarative configuration layer for ImputeBench experiments.
They replace ad-hoc Python builder functions and markdown-based loading with a unified
registry-backed persistence model, a versioned YAML/JSON interchange format, and a
dedicated CLI surface.

---

## Purpose

A recipe book defines a complete experiment blueprint in one document:

- **Dataset reference** — which dataset the book targets
- **Defaults** — shared missingness parameters, masking families, realization counts
- **Algorithm list** — which imputation algorithms are included
- **Generation mode** — explicit entry list vs. combinatorial matrix expansion
- **Profiles** — per-tier or per-masking-group parameter overrides
- **Domain configuration** — temporal parameters (lookback, horizon) or spatiotemporal parameters (graph policy, adjacency config)
- **Materialization policy** — what gets materialized and when

A recipe book does not contain run results, predictions, or runtime metadata. It is
the plan, not the execution.

---

## Shared Control Plane

Recipe books serve both the temporal lane and the spatiotemporal lane through a shared
control plane with domain-specific adapters.

```
                   ┌───────────────────────────┐
                   │   Recipe Book Registry     │
                   │   (SQLite, immutable revs) │
                   └─────────────┬─────────────┘
                                 │
                   ┌─────────────┴─────────────┐
                   │    Recipe Book Service     │
                   │   (validate, materialize,  │
                   │    clone, revision mgmt)   │
                   └─────────────┬─────────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │                                     │
   ┌──────────┴──────────┐             ┌────────────┴────────────┐
   │  Temporal Adapter   │             │  Spatiotemporal Adapter │
   │  (lookback, horizon,│             │  (graph policy, adj,    │
   │   masking families) │             │   spatial masking)      │
   └─────────────────────┘             └─────────────────────────┘
```

- The **registry layer** stores books as immutable revisions with monotonically
  increasing version numbers.
- The **service layer** handles validation, materialization routing, cloning, and
  revision history.
- **Domain adapters** translate the generic recipe book schema into domain-specific
  experiment plans used by `imputebench experiment temporal` and
  `imputebench experiment st`.

---

## Registry-Backed Persistence

Recipe books live in SQLite with four tables.

| Table | Purpose |
|---|---|
| `recipe_books` | One row per book; stores id, domain, kind, status, claim scope, tags, current revision |
| `recipe_book_revisions` | Immutable revision snapshots; stores the full YAML payload per version |
| `recipe_book_entries` | Individual recipe entries (one per algorithm + masking combo after expansion) |
| `recipe_book_algorithms` | Algorithm references scoped to a book (algorithm id, config overrides) |

Key properties:

- Every mutation to a book creates a new revision row. Previous revisions are never
  overwritten.
- The `--expected-revision` flag enables optimistic concurrency: a command aborts if
  the current revision in the database differs from the one the caller last read.
- Entries are materialized from the book definition and cached in the entries table
  for fast query access by the temporal and ST lanes.

---

## Safe YAML/JSON Interchange

The canonical interchange format is YAML with the schema identifier
`imputebench.recipe-book/v1`. JSON is accepted as a secondary format.

Schema location: `imputebench/resources/schemas/recipe_book_v1.schema.json`
(JSON Schema Draft 2020-12).

Safe YAML rules enforced at parse time:

- No custom YAML tags (no `!Tag` directives)
- No duplicate mapping keys
- String values limited to 64 KiB
- Document size limited to 1 MiB before expansion

These rules prevent code-execution vectors and accidental corruption through
malformed YAML constructs.

See [Recipe_Book_Schema_V1.md](Recipe_Book_Schema_V1.md) for the full field reference
and example.

---

## Built-in Protected Resources

Four recipe books ship as built-in YAML resources under
`imputebench/resources/recipe_books/`. They are seeded into the registry on first run
via `imputebench admin migrate recipe-books --seed-builtins`.

| Book ID | Domain | Kind | Description |
|---|---|---|---|
| `official_londonaq_temporal_dl` | temporal | dl | Temporal DL benchmark (BRITS, SAITS, SAITS-LC, GRU-D) |
| `official_londonaq_temporal_classical` | temporal | classical | Temporal classical baselines (mean, median, KNN, MICE, etc.) |
| `official_londonaq_temporal_baselines` | temporal | baselines | Naive/statistical baselines (LOCF, NOCB, linear interpolation) |
| `official_londonaq_st` | spatiotemporal | spatiotemporal | Spatiotemporal benchmark (ST-GCN, ST-GDN, etc.) |

Built-in books are protected: they cannot be directly modified or deleted.
To customize a built-in book, clone it first.

---

## CLI Surface

The canonical CLI path is `imputebench experiment recipe`.

```
imputebench experiment recipe
├── list          List all recipe books
├── show          Display one recipe book
├── create        Create a new recipe book from a YAML file or inline definition
├── clone         Clone an existing book (required for customizing built-ins)
├── update        Update a recipe book (requires --expected-revision)
├── delete        Delete a recipe book (refused for built-ins)
├── validate      Validate a YAML/JSON file against the schema
├── export        Export a recipe book to YAML or JSON
├── history       Show revision history for a book
├── materialize   Materialize entries from a book definition
├── entry         Manage individual recipe entries (CRUD)
└── algorithm     Manage algorithm references within a book (add/remove)
```

Full command reference: [Recipe_Book_CLI_Reference.md](Recipe_Book_CLI_Reference.md).

Admin seeding command:

```
imputebench admin migrate recipe-books
    --seed-builtins    Seed the four built-in books into the registry
    --verify           Verify seeded books against YAML resources
    --dry-run          Preview without writing
```

---

## Domain Materialization Router

When `materialize` is invoked, the service inspects the book's `domain` field and
routes materialization to the correct domain adapter.

| Domain | Adapter | Output |
|---|---|---|
| `temporal` | TemporalMaterializer | Temporal experiment plan entries |
| `spatiotemporal` | STMaterializer | ST experiment plan entries |

The materialization process:

1. Reads the book's current revision from the registry.
2. Resolves algorithm references against the algorithm registry.
3. Expands the generation matrix (if matrix mode) into explicit entries.
4. Applies profile overrides to each entry.
5. Writes materialized entries to `recipe_book_entries`.
6. Returns a materialization summary: entry count, profile matches, skipped entries.

Materialization is idempotent: re-running it with the same revision produces the
same entries. The entries table is cleared and re-populated on each materialization.

---

## Optimistic Concurrency

Every mutating command (`update`, `delete`, `materialize`, `entry create`, `entry update`,
`entry delete`, `algorithm add`, `algorithm remove`) accepts the flag:

```
--expected-revision <N>
```

Before applying the mutation, the service checks that the book's current revision
equals `<N>`. If it does not, the command exits with code `2` (blocked) and prints
the actual current revision number.

This pattern prevents lost-update conflicts when multiple operators or automated
pipelines modify the same book concurrently.

---

## References

| Document | Covers |
|---|---|
| [Recipe_Book_Schema_V1.md](Recipe_Book_Schema_V1.md) | Full schema field reference and YAML example |
| [Recipe_Book_CLI_Reference.md](Recipe_Book_CLI_Reference.md) | Complete CLI command reference with examples |
| [Recipe_Book_Migration_Guide.md](Recipe_Book_Migration_Guide.md) | Migration from Python builders to registry |
| [Recipe_Book_Architecture_Update_Report.md](Recipe_Book_Architecture_Update_Report.md) | Closure report for the architecture redesign |

<!-- FOOTER:START -->

---

> [← README](../README.md) · [⬆ Top](#) · [🏠 Home](../README.md) · [Recipe_Book_Schema_V1 →](Recipe_Book_Schema_V1.md)
<!-- FOOTER:END -->
