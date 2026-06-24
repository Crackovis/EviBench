<!-- NAV:START -->
> [🏠 Home](../README.md) · 📍 **Recipe_Book_CLI_Reference**

<details>
<summary>🗺️ Documentation Map</summary>

- [📂 README](../README.md)
- 📚 **docs/**
  - [🏗️ Architecture_Overview](Architecture_Overview.md)
  - [📝 Recipe_Book_Architecture](Recipe_Book_Architecture.md)
  - [📝 Recipe_Book_Schema_V1](Recipe_Book_Schema_V1.md)
  - [💻 Recipe_Book_CLI_Reference](Recipe_Book_CLI_Reference.md) ← *you are here*
  - [💻 Recipe_Book_Migration_Guide](Recipe_Book_Migration_Guide.md)
  - [📝 Recipe_Book_Architecture_Update_Report](Recipe_Book_Architecture_Update_Report.md)
  - [💻 CLI_Reference](CLI_Reference.md)
  - [💻 Temporal_Experiment_CLI_Reference](Temporal_Experiment_CLI_Reference.md)

</details>
<!-- NAV:END -->

# Recipe Book CLI Reference

Canonical path: `imputebench experiment recipe`

This document covers every subcommand, option, and exit code for the recipe book
CLI surface. For an architectural overview, see
[Recipe_Book_Architecture.md](Recipe_Book_Architecture.md).

---

## Command Tree

```
imputebench experiment recipe
├── list            List all recipe books
├── show            Display one recipe book
├── create          Create a new recipe book
├── clone           Clone an existing book
├── update          Update a recipe book
├── delete          Delete a recipe book
├── validate        Validate a YAML/JSON file
├── export          Export a recipe book to YAML or JSON
├── history         Show revision history
├── materialize     Materialize entries from book
├── entry           Manage recipe entries
│   ├── list        List entries in a book
│   ├── show        Show a single entry
│   ├── create      Create a new entry
│   ├── update      Update an entry
│   └── delete      Delete an entry
└── algorithm       Manage algorithm references
    ├── list        List algorithms in a book
    ├── add         Add an algorithm to a book
    └── remove      Remove an algorithm from a book
```

---

## Global Options

All recipe subcommands accept these shared options:

| Option | Description |
|---|---|
| `--format table\|json` | Output format. Default: `table` |
| `--output-dir <path>` | Directory for export/report output |

---

## `list`

List all recipe books in the registry.

```
imputebench experiment recipe list [options]
```

| Option | Description |
|---|---|
| `--domain temporal\|spatiotemporal` | Filter by domain |
| `--kind dl\|classical\|baselines\|spatiotemporal` | Filter by kind |
| `--status active\|archived\|deprecated\|draft` | Filter by status |
| `--claim-scope builtin\|project\|user` | Filter by claim scope |
| `--format table\|json` | Output format |

```bash
imputebench experiment recipe list
imputebench experiment recipe list --domain temporal --kind dl
imputebench experiment recipe list --claim-scope builtin --format json
```

---

## `show`

Display a single recipe book with its current revision, algorithm list, and entry
count.

```
imputebench experiment recipe show <book_id> [options]
```

| Option | Description |
|---|---|
| `--revision <N>` | Show a specific historical revision |
| `--include-entries` | Include materialized entries in output |
| `--include-algorithms` | Include algorithm references (default: true) |
| `--no-include-algorithms` | Exclude algorithm references |
| `--format table\|json\|yaml` | Output format |

```bash
imputebench experiment recipe show official_londonaq_temporal_dl
imputebench experiment recipe show my_book --revision 3 --include-entries
imputebench experiment recipe show my_book --format yaml
```

---

## `create`

Create a new recipe book from a YAML file or inline definition.

```
imputebench experiment recipe create [options]
```

| Option | Description |
|---|---|
| `--file <path>` | Path to YAML or JSON file |
| `--from-book <book_id>` | Clone an existing book (shorthand for clone) |
| `--id <book_id>` | Book identifier (required if not in file) |

```bash
imputebench experiment recipe create --file my_book.yaml
imputebench experiment recipe create --from-book official_londonaq_temporal_dl --id my_copy
```

---

## `clone`

Clone an existing recipe book with a new identifier. Required for customizing
built-in books.

```
imputebench experiment recipe clone <source_book_id> --id <new_book_id> [options]
```

| Option | Description |
|---|---|
| `--id <book_id>` | New book identifier (required) |
| `--claim-scope project\|user` | Ownership claim for the clone. Default: `user` |
| `--status active\|draft` | Initial status. Default: `draft` |

```bash
imputebench experiment recipe clone official_londonaq_temporal_dl --id my_dl_custom
imputebench experiment recipe clone my_book --id my_book_v2 --status draft
```

---

## `update`

Update a recipe book. Requires optimistic concurrency via `--expected-revision`.

```
imputebench experiment recipe update <book_id> [options]
```

| Option | Description |
|---|---|
| `--file <path>` | Path to updated YAML or JSON file |
| `--expected-revision <N>` | Current revision number (required) |
| `--dry-run` | Preview changes without writing |
| `--format table\|json` | Output format for dry-run diff |

```bash
imputebench experiment recipe update my_book --file my_book_v2.yaml --expected-revision 5
imputebench experiment recipe update my_book --file changes.yaml --expected-revision 5 --dry-run --format json
```

Exit codes:
- `0` — update applied
- `1` — validation error
- `2` — revision mismatch (blocked by concurrent modification)

---

## `delete`

Delete a recipe book. Built-in books cannot be deleted.

```
imputebench experiment recipe delete <book_id> [options]
```

| Option | Description |
|---|---|
| `--expected-revision <N>` | Current revision number |
| `--force` | Skip confirmation prompt |
| `--dry-run` | Preview impact without deleting |
| `--cascade` | Also delete entries and algorithm references |

```bash
imputebench experiment recipe delete my_book --expected-revision 3 --force
imputebench experiment recipe delete my_book --dry-run
imputebench experiment recipe delete my_book --cascade --expected-revision 3
```

---

## `validate`

Validate a YAML or JSON file against the `imputebench.recipe-book/v1` schema
without creating or updating any book.

```
imputebench experiment recipe validate <file_path> [options]
```

| Option | Description |
|---|---|
| `--strict` | Apply strict validation (reject unknown fields) |
| `--format table\|json` | Output format |

```bash
imputebench experiment recipe validate my_book.yaml
imputebench experiment recipe validate my_book.yaml --strict --format json
```

---

## `export`

Export a recipe book to YAML or JSON.

```
imputebench experiment recipe export <book_id> [options]
```

| Option | Description |
|---|---|
| `--revision <N>` | Export a specific revision (default: current) |
| `--format yaml\|json` | Export format. Default: `yaml` |
| `--output <path>` | Output file path. If omitted, writes to stdout. |
| `--include-metadata` | Include metadata block |

```bash
imputebench experiment recipe export my_book --format json
imputebench experiment recipe export my_book --revision 2 --output my_book_v2.yaml
```

---

## `history`

Show the revision history for a recipe book.

```
imputebench experiment recipe history <book_id> [options]
```

| Option | Description |
|---|---|
| `--limit <N>` | Maximum revisions to show. Default: 20 |
| `--format table\|json` | Output format |

```bash
imputebench experiment recipe history official_londonaq_temporal_dl
imputebench experiment recipe history my_book --limit 10 --format json
```

Output columns: revision number, timestamp, action (create/update/clone), operator
identifier.

---

## `materialize`

Materialize recipe entries from the current book definition into the entries table.

```
imputebench experiment recipe materialize <book_id> [options]
```

| Option | Description |
|---|---|
| `--expected-revision <N>` | Current revision number |
| `--dry-run` | Preview materialization without writing entries |

```bash
imputebench experiment recipe materialize official_londonaq_temporal_dl
imputebench experiment recipe materialize my_book --expected-revision 5
imputebench experiment recipe materialize my_book --dry-run
```

Materialization is idempotent. The entries table is cleared and re-populated.
Output reports the number of entries created, profiles matched, and any skipped
combinations.

---

## `entry`

Manage individual recipe entries within a book.

### `entry list`

```
imputebench experiment recipe entry list <book_id> [options]
```

| Option | Description |
|---|---|
| `--algorithm-id <id>` | Filter by algorithm |
| `--mask-family <family>` | Filter by masking family |
| `--rate <rate>` | Filter by missingness rate |
| `--format table\|json` | Output format |

```bash
imputebench experiment recipe entry list official_londonaq_temporal_dl
imputebench experiment recipe entry list my_book --algorithm-id brits --mask-family mcar
```

### `entry show`

```
imputebench experiment recipe entry show <book_id> <entry_index> [options]
```

| Option | Description |
|---|---|
| `--format table\|json` | Output format |

```bash
imputebench experiment recipe entry show my_book 0
imputebench experiment recipe entry show my_book 42 --format json
```

### `entry create`

```
imputebench experiment recipe entry create <book_id> [options]
```

| Option | Description |
|---|---|
| `--algorithm-id <id>` | Algorithm identifier (required) |
| `--mask-family <family>` | Masking family (required) |
| `--rate <rate>` | Missingness rate. Must be in `(0, 1)` (required) |
| `--realization <N>` | Realization index. Default: `1` |
| `--expected-revision <N>` | Current revision number |
| `--config JSON` | Per-entry algorithm configuration overrides |

```bash
imputebench experiment recipe entry create my_book \
  --algorithm-id brits --mask-family mcar --rate 0.3 --realization 1 \
  --expected-revision 5
```

### `entry update`

```
imputebench experiment recipe entry update <book_id> <entry_index> [options]
```

| Option | Description |
|---|---|
| `--algorithm-id <id>` | Change algorithm |
| `--mask-family <family>` | Change masking family |
| `--rate <rate>` | Change missingness rate |
| `--realization <N>` | Change realization index |
| `--config JSON` | Update configuration overrides |
| `--expected-revision <N>` | Current revision number |
| `--dry-run` | Preview without writing |

```bash
imputebench experiment recipe entry update my_book 0 --rate 0.5 --expected-revision 5
```

### `entry delete`

```
imputebench experiment recipe entry delete <book_id> <entry_index> [options]
```

| Option | Description |
|---|---|
| `--expected-revision <N>` | Current revision number |
| `--force` | Skip confirmation |

```bash
imputebench experiment recipe entry delete my_book 0 --expected-revision 5 --force
```

---

## `algorithm`

Manage algorithm references within a recipe book.

### `algorithm list`

```
imputebench experiment recipe algorithm list <book_id> [options]
```

| Option | Description |
|---|---|
| `--format table\|json` | Output format |

```bash
imputebench experiment recipe algorithm list official_londonaq_temporal_dl
```

### `algorithm add`

```
imputebench experiment recipe algorithm add <book_id> <algorithm_id> [options]
```

| Option | Description |
|---|---|
| `--config JSON` | Algorithm configuration overrides |
| `--expected-revision <N>` | Current revision number |
| `--dry-run` | Preview without writing |

```bash
imputebench experiment recipe algorithm add my_book brits --expected-revision 5
imputebench experiment recipe algorithm add my_book saits --config '{"epochs": 200}' --expected-revision 5
```

### `algorithm remove`

```
imputebench experiment recipe algorithm remove <book_id> <algorithm_id> [options]
```

| Option | Description |
|---|---|
| `--expected-revision <N>` | Current revision number |
| `--force` | Skip confirmation |
| `--dry-run` | Preview without writing |

```bash
imputebench experiment recipe algorithm remove my_book brits --expected-revision 5 --force
```

---

## Admin Command

```
imputebench admin migrate recipe-books [options]
```

| Option | Description |
|---|---|
| `--seed-builtins` | Seed the four built-in recipe books into the registry |
| `--verify` | Verify seeded books match bundled YAML resources |
| `--dry-run` | Preview without writing |

```bash
imputebench admin migrate recipe-books --seed-builtins
imputebench admin migrate recipe-books --seed-builtins --verify
imputebench admin migrate recipe-books --seed-builtins --dry-run
```

This command is typically run once after upgrading the schema version or on
first-time installation. It is idempotent: running it again with the same built-in
resources has no effect.

---

## Exit Codes

| Code | Meaning |
|---|---|
| `0` | Success |
| `1` | Error (validation failure, missing resource, I/O error) |
| `2` | Blocked (revision mismatch, built-in protection, concurrent conflict) |

---

## JSON Output Format

When `--format json` is specified, command output is written to stdout as a single
JSON object. Warnings and progress messages are written to stderr and do not appear
in the JSON output. This ensures that JSON output is machine-parseable without
filtering.

```bash
# Parseable JSON output
imputebench experiment recipe list --format json | jq '.[] | {id, domain, revision}'

# Warnings go to stderr, not mixed into stdout
imputebench experiment recipe materialize my_book --expected-revision 5 --format json > entries.json
```

<!-- FOOTER:START -->

---

> [← Recipe_Book_Schema_V1](Recipe_Book_Schema_V1.md) · [⬆ Top](#) · [🏠 Home](../README.md) · [Recipe_Book_Migration_Guide →](Recipe_Book_Migration_Guide.md)
<!-- FOOTER:END -->
