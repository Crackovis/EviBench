# Evidence Discovery and Selective Export

`imputebench results evidence` — discover, select, and export curated evidence.

## Evidence Discovery

### `results evidence list TARGET_ID`

Read-only discovery. Never writes files, runs exporters, or loads prediction arrays.

```
imputebench results evidence list TARGET_ID
  --target-type auto|result|run|comparison
  --category CATEGORY     # Filter by category
  --state STATE           # Filter by item state
  --provider PROVIDER     # Filter by provider id
  --include-not-applicable
  --wide                  # Show provider and full paths
  --format auto|rich|plain|table|json
```

Seven evidence item states:

| State | Meaning |
|-------|---------|
| `available` | Artifact verified in catalog |
| `derivable` | Can be generated from persisted data |
| `not_applicable` | Not relevant to this target |
| `blocked` | Prerequisites not met |
| `missing` | Expected but not found |
| `stale` | Exists but source/checksum outdated |
| `unsupported` | No concrete provider registered |

Eight evidence categories: `summary`, `tables`, `runtime`, `manifests`, `figures`, `gates`, `training`, `provenance`.

JSON output uses `imputebench.evidence-inventory/v1` schema.

## Selective Export

### `results evidence export TARGET_ID`

Export evidence for a single target.

```
imputebench results evidence export TARGET_ID
  --target-type auto|result|run|comparison
  (--what ITEM,... | --preset PRESET_ID)    # Mutually exclusive
  --format FORMAT ...                        # Evidence formats (csv, json, png, etc.)
  --output-dir PATH
  --overwrite-policy fail|reuse-identical|replace-generated
  --strictness fail-on-missing|skip-unavailable
  --dry-run                                  # Plan without executing
  --format-output auto|rich|plain|table|json # Terminal rendering
```

Exactly one of `--what` or `--preset` is required.

### `results evidence export-batch`

Batch export across multiple targets with selection filters.

```
imputebench results evidence export-batch
  --run-id RUN_ID
  --dataset-id DATASET_ID
  --algorithm-id ALGORITHM_ID
  --execution-class classical|scientific_dl|spatiotemporal
  --phase PHASE
  (--what ITEM,... | --preset PRESET_ID)
  --max-targets N (default 100)
  --output-dir PATH
  --layout by-target|by-item|flat
  --overwrite-policy fail|reuse-identical|replace-generated
  --strictness fail-on-missing|skip-unavailable
  --dry-run
  --execute                      # Required to proceed past plan
  --confirm-token TOKEN          # Must match plan token
  --format-output ...
```

Dry-run returns the plan with a confirmation token. Execute requires both `--execute` and the matching `--confirm-token`.

## Export Pipeline

1. **Validate** — target existence, item/provider resolution
2. **Resolve** — preset items, format negotiation
3. **Build plan** — `EvidenceExportPlan` with resolved/skipped/blocked items
4. **Stage** — temp directory under output parent
5. **Invoke providers** — per-item export calls
6. **Verify** — all expected files exist
7. **Checksum** — SHA-256 per file (64KB chunked reads)
8. **Manifest** — `manifest.json` with `imputebench.evidence-export/v1` schema
9. **Publish** — atomic staging → output rename

Failure never corrupts a published target.

## Overwrite Policy

| Policy | Behavior |
|--------|----------|
| `fail` | Error if output directory exists |
| `reuse-identical` | Compare manifest SHA-256; skip if identical |
| `replace-generated` | Remove old output, publish new |

## Strictness

| Mode | Behavior |
|------|----------|
| `fail-on-missing` | Abort if any requested item is unavailable |
| `skip-unavailable` | Skip unavailable items, export the rest |

## Manifest

Every export produces a `manifest.json`:

```json
{
  "schema": "imputebench.evidence-export/v1",
  "export_id": "abc123...",
  "generated_at": "2026-06-14T...",
  "targets": [...],
  "selection": {"preset_id": "...", "requested_items": [...]},
  "preset": {"preset_id": "...", "preset_revision": 1},
  "items": [
    {
      "item_id": "metric_table",
      "provider_id": "core_result",
      "provider_version": "1",
      "status": "exported",
      "files": [{"path": "...", "sha256": "...", "size_bytes": 123}],
      "source_ids": [],
      "warnings": []
    }
  ],
  "scientific_scope": "...",
  "gate_status": "...",
  "claim_limits": [],
  "warnings": [],
  "blocked_items": []
}
```

Exported files are registered in the artifact catalog with `object_type = "evidence_export"`.

## Selective Guarantee

Only requested item IDs, mandatory manifest files, and provider-required sidecars are written. The exporter never copies an entire evidence tree unless a provider item explicitly represents that pack.

## Examples

```bash
# Discover evidence for a result
imputebench results evidence list 7dc94779

# Plan an export using a built-in preset
imputebench results evidence export 7dc94779 --preset quick_inspect --dry-run

# Execute the export
imputebench results evidence export 7dc94779 --preset quick_inspect --output-dir exports/my-export

# Export specific items with JSON format
imputebench results evidence export 7dc94779 --what metric_table,runtime_breakdown --format json

# Batch export all test-phase results
imputebench results evidence export-batch --phase test --what metric_table --dry-run
```
