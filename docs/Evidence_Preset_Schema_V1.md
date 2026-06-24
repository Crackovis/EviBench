# Evidence Preset Schema v1

Schema ID: `imputebench.evidence-preset/v1`

## Overview

Evidence presets are versioned YAML/JSON files that define a curated set of evidence items for export. Presets select evidence items by ID; they never grant scientific validity.

## Schema Properties

### `schema` (string, required)
Must be `imputebench.evidence-preset/v1`.

### `preset` (object, required)

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique preset identifier (a-z0-9._-, 3-127 chars) |
| `title` | string | Human-readable title (1-240 chars) |
| `description` | string | Detailed description |
| `status` | string | `draft`, `active`, or `deprecated` |
| `target_types` | string[] | Applicable target types: result, run, comparison |
| `domain_scope` | string[] | Domain filter: temporal, spatiotemporal, or empty |
| `tags` | string[] | Freeform tags for filtering |

### `selection` (object, required)

| Field | Type | Description |
|-------|------|-------------|
| `include` | string[] | Evidence item IDs to include |
| `exclude` | string[] | Evidence item IDs to exclude (subtracted from include) |

All item IDs must be registered evidence types (37 total across 7 providers).

### `formats` (object, optional)

Per-item format overrides. Keys are item IDs or `"default"`.

```yaml
formats:
  default: [json, markdown]
  metric_table: [csv, json, markdown]
  training_curve_figure: [png]
```

Supported formats: `json`, `csv`, `markdown`, `png`, `npy`.

### `output` (object, required)

| Field | Type | Description |
|-------|------|-------------|
| `layout` | string | `by-target`, `flat`, `by-type`, or `custom` |
| `path_template` | string | Output path template (see template variables) |
| `write_manifest` | boolean | Whether to write manifest.json |
| `checksum` | string | `sha256` or `none` |

### `policy` (object, required)

| Field | Type | Description |
|-------|------|-------------|
| `strictness` | string | `fail-on-missing`, `warn-on-missing`, or `permissive` |
| `require_comparison_compatibility` | boolean | Require comparison-ready targets |
| `require_gate_profile` | string | Required gate profile: `comparison_ready`, `thesis_ready`, etc. |
| `allow_mock` | boolean | Allow mock/non-scientific results |
| `allow_diagnostic` | boolean | Allow diagnostic-mode data |

## Path Template Variables

Allowed template variables (no arbitrary expressions):

| Variable | Description |
|----------|-------------|
| `{target_id}` | Target identifier |
| `{target_type}` | result, run, or comparison |
| `{dataset_id}` | Dataset identifier |
| `{algorithm_id}` | Algorithm identifier |
| `{comparison_id}` | Comparison spec identifier |
| `{run_id}` | Experiment run identifier |
| `{preset_id}` | Preset identifier |
| `{date_utc}` | UTC date stamp |

## Built-in Presets

Five built-in presets ship with ImputeBench. They are protected (immutable) and must be cloned to customize.

### `quick_inspect`

| Field | Value |
|-------|-------|
| Items | result_summary, metric_table, runtime_breakdown, artifact_inventory, provenance_manifest |
| Target types | result, run |
| Diagnostic | allowed |

### `comparison_ready`

| Field | Value |
|-------|-------|
| Items | metric_table, comparison_ready_signal, artifact_inventory, comparison_table, comparison_gate_verdict |
| Comparison compatibility | required |
| Gate profile | comparison_ready |

### `thesis_chapter04`

Temporal/training/comparison evidence for Chapter 04.

```yaml
preset:
  id: thesis_chapter04
  title: Thesis Chapter 04 evidence
  target_types: [result, comparison]
  domain_scope: [temporal]
selection:
  include:
    - metric_table
    - runtime_breakdown
    - training_evidence_pack
    - training_curve_figure
    - comparison_table
    - comparison_ready_signal
    - comparison_storyboard
    - comparison_gate_verdict
policy:
  strictness: fail-on-missing
  require_comparison_compatibility: true
  require_gate_profile: comparison_ready
  allow_mock: false
```

### `thesis_chapter06`

Spatiotemporal evidence for Chapter 06.

```yaml
preset:
  id: thesis_chapter06
  title: Thesis Chapter 06 evidence
  target_types: [result, run]
  domain_scope: [spatiotemporal]
selection:
  include:
    - graph_characterization
    - spatial_missingness_characterization
    - st_algorithm_lifecycle
    - st_comparison_pack
    - st_storyboard_pack
    - st_gate_pack
    - st_figure_pack
    - st_chapter_manifest
```

### `debug_inventory`

| Field | Value |
|-------|-------|
| Items | artifact_inventory, provenance_manifest, runtime_breakdown, comparison_ready_signal |
| Mock allowed | true |
| Diagnostic | allowed |

## Preset CLI

```
results evidence preset list     [--origin builtin|user|imported] [--format table|json]
results evidence preset show ID  [--revision N] [--format yaml|json|table]
results evidence preset create   --from-file PATH [--dry-run]
results evidence preset clone SRC --new-id NEW [--title TITLE] [--dry-run]
results evidence preset update ID --expected-revision N (--from-file|--patch-file|--set) [--dry-run]
results evidence preset delete ID --expected-revision N [--archive] [--dry-run]
results evidence preset validate (ID|--file PATH) [--target TARGET_ID]
results evidence preset export  ID --output PATH [--revision N] [--format yaml|json]
```

## Protection Rules

- Built-in presets (`origin: builtin`, `protected: true`) are immutable — cannot be updated or deleted
- Custom presets are created via `clone`, producing `origin: user`, `protected: false`
- Optimistic concurrency: mutations require `--expected-revision N`
- Content SHA-256 ensures detection of unintended changes
- Archive by default (delete = archive, not purge)

## Validation

- Stable unique ID
- All include/exclude items must be registered evidence types
- No Python hooks, shell commands, or unsafe YAML constructors
- Portable relative path templates
- Allowlisted template variables only
- Supported provider formats
- Claim scope cannot be upgraded via preset
- Required gate profile must exist
- Target/domain compatibility required
