# Results Interaction Architecture

Overview of the Sheet 11 results-interaction layer (v10).

## CLI Domains

ImputeBench CLI is organized into 8 canonical root domains:

```
imputebench
├── data          # Dataset, ingest, masking, calibration
├── methods       # Algorithm, plugin
├── experiment    # Run, st, temporal
├── results       # Result, compare, inspect, evidence, gate, view
├── study         # Study management
├── thesis        # Thesis evidence export
├── admin         # Paths, audit, config, maintenance, env
└── lab           # Interactive lab launcher
```

The `results` domain hosts all result-interaction subcommands:

```
results
├── result        # Result CRUD (list, show, delete, export)
├── compare       # Comparison specs (list, show, delete) + interactive (results, runs, query, table)
├── inspect       # Human/scientific projection of a result, run, or comparison
├── evidence      # Evidence discovery, presets, selective export
│   ├── list      # Read-only evidence inventory
│   ├── preset    # Versioned evidence presets (8 subcommands)
│   ├── export    # Single-target selective export
│   └── export-batch  # Batch export with selection filters
├── gate          # Evidence completeness gate
└── view          # Spatial viewers
```

## Read Models

All read models live in `imputebench.read_models.results_interaction`:

| Module | Schema | Purpose |
|--------|--------|---------|
| `target.py` | — | ResolvedTarget, target type constants |
| `inspection.py` | `imputebench.results-inspection/v1` | InspectionReport, sections, fields, rows |
| `evidence.py` | `imputebench.evidence-inventory/v1` | EvidenceItemDescriptor, EvidenceInventory, 7 states |
| `export.py` | `imputebench.evidence-export/v1` | EvidenceExportPlan, EvidenceExportReport, ExportedFile |
| `preset.py` | `imputebench.evidence-preset/v1` | EvidencePresetDefinition, RegisteredEvidencePreset |

## Service Layer

### Core Services

| Service | Module | Role |
|---------|--------|------|
| `TargetResolver` | `services/results_interaction/target_resolver.py` | Resolve target_id → result/run/comparison |
| `InspectionService` | `services/results_interaction/inspection_service.py` | Build inspection read models |
| `ComparisonService` | `services/results_interaction/comparison_service.py` | Unit normalization, aggregation, ranking |
| `SelectionService` | `services/results_interaction/selection_service.py` | Result selection with filters |
| `CompatibilityService` | `services/comparison` | Benchmark parity evaluation |

### Evidence Pipeline

| Service | Module | Role |
|---------|--------|------|
| `EvidenceInventoryService` | `services/results_interaction/evidence_inventory_service.py` | Build merged evidence inventory |
| `EvidenceProviderRegistry` | `services/results_interaction/evidence_provider_registry.py` | Register and resolve providers |
| `ExportPlanner` | `services/results_interaction/export_planner.py` | Resolve targets/items → EvidenceExportPlan |
| `ExportEngine` | `services/results_interaction/export_engine.py` | 9-step publication sequence |

### Preset System

| Service | Module | Role |
|---------|--------|------|
| `PresetCodec` | `services/results_interaction/preset_codec.py` | Safe YAML/JSON + SHA-256 canonicalizer |
| `EvidencePresetRepository` | `persistence/repositories/evidence_preset_repository.py` | CRUD with optimistic concurrency |
| `PresetRegistryService` | `services/results_interaction/preset_registry_service.py` | List, get, create, clone, update, delete, validate, export |
| `PresetMutationService` | `services/results_interaction/preset_mutation_service.py` | Clone-before-customize, protected built-in enforcement |
| `BuiltinPresetSeedService` | `services/results_interaction/builtin_preset_seed_service.py` | Seed 5 built-in presets |

## Evidence Providers

Seven built-in providers, registered in code (not YAML):

| Provider ID | Items | Scope |
|-------------|-------|-------|
| `core_result` | 9 | result_summary, metric_table, split_metric_table, pollutant_metric_table, node_metric_summary, runtime_breakdown, artifact_inventory, provenance_manifest, comparison_ready_signal |
| `training` | 5 | training_evidence_pack, training_curve_data, training_curve_figure, checkpoint_selection, training_runtime_summary |
| `temporal` | 4 | temporal_evidence_manifest, mask_artifact, prediction_sample, mask_visualization |
| `spatiotemporal` | 10 | graph_characterization, spatial_missingness_characterization, st_algorithm_lifecycle, st_comparison_pack, st_storyboard_pack, st_gate_pack, st_figure_pack, st_gallery, st_scientific_evidence_profile, st_chapter_manifest |
| `comparison` | 7 | comparison_table, ranking_table, comparison_visual_payload, runtime_cohort_table, comparison_storyboard, comparison_gate_verdict, comparison_export_context |
| `storyboard` | 1 | result_storyboard |
| `gate` | 1 | evidence_completeness_gate |

Total: 37 evidence types across 7 providers and 8 categories.

## Export Pipeline

The 9-step publication sequence (§11.4):

1. Validate (target, items, providers)
2. Create staging directory
3. Invoke providers (per-item export calls)
4. Verify expected files exist
5. Compute SHA-256 checksums (64KB chunked reads)
6. Write manifest (`manifest.json` with `imputebench.evidence-export/v1` schema)
7. Atomically publish (staging → output rename)
8. Register exported files in artifact catalog
9. Emit EvidenceExportReport

Failure never corrupts a published target. Staging is cleaned on success, retained on failure only with `--keep-failed-staging`.

## Schema Version History

| Version | Phase | Changes |
|---------|-------|---------|
| v8 | Baseline | Pre-Sheet-11 schema |
| v9 | 11-02 | Recipe lineage columns on runs + results |
| v10 | 11-07 | `evidence_presets` + `evidence_preset_revisions` tables |

## Evidence Item States

| State | Meaning |
|-------|---------|
| `available` | Artifact exists and is verified in catalog |
| `derivable` | Can be generated from persisted data |
| `not_applicable` | Not applicable to this target |
| `blocked` | Prerequisites not met |
| `missing` | Expected but not found |
| `stale` | Artifact exists but source/checksum is stale |
| `unsupported` | No concrete provider registered |

## Presets

Five built-in presets, immutable and clone-only:

| Preset ID | Items | Purpose |
|-----------|-------|---------|
| `quick_inspect` | 5 | Quick result overview |
| `comparison_ready` | 5 | Comparison readiness check |
| `thesis_chapter04` | 8 | Temporal chapter evidence |
| `thesis_chapter06` | 8 | Spatiotemporal chapter evidence |
| `debug_inventory` | 4 | Debug/diagnostic inventory |

Custom presets are created via `results evidence preset clone SOURCE_ID --new-id NEW_ID`, which produces a non-protected, user-owned copy.
