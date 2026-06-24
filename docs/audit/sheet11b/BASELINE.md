# Sheet 11B — Phase 11B-00 Baseline Audit

**Audited HEAD:** `3cbc8db6` (branch `main`)
**Date:** 2026-06-14
**SQLite schema version:** `10` (confirmed: `imputebench/storage/sqlite/schema.py::SCHEMA_VERSION = 10`)

This baseline freezes the executable gap that Sheet 11B closes. It is a
re-audit of the *live* repository, not a transcription of the specification.

---

## 1. Provider capability matrix (before)

`docs/audit/sheet11b/provider_capability_matrix.json` captures the full
descriptor set. Summary:

| Provider | descriptors | overrides `export` |
|---|---:|---|
| core_result | 9 | **no** |
| training | 5 | **no** |
| temporal | 4 | **no** |
| spatiotemporal | 10 | **no** |
| comparison | 7 | **no** |
| storyboard | 1 | **no** |
| gate | 1 | **no** |
| **total** | **37** | **0 / 7** |

No built-in provider overrides `BaseEvidenceProvider.export`. Every provider
therefore inherits the Phase 11-08 stub.

## 2. Phase 11-08 NotImplementedError (reproduced)

```text
core_result      NotImplementedError: Provider 'core_result' export lands in Phase 11-08.
training         NotImplementedError: Provider 'training' export lands in Phase 11-08.
temporal         NotImplementedError: Provider 'temporal' export lands in Phase 11-08.
spatiotemporal   NotImplementedError: Provider 'spatiotemporal' export lands in Phase 11-08.
comparison       NotImplementedError: Provider 'comparison' export lands in Phase 11-08.
storyboard       NotImplementedError: Provider 'storyboard' export lands in Phase 11-08.
gate             NotImplementedError: Provider 'gate' export lands in Phase 11-08.
```

`BaseEvidenceProvider.plan` and `BaseEvidenceProvider.export`
(`imputebench/services/results_interaction/evidence_provider_registry.py:207-215`)
raise `NotImplementedError` for every provider. Discovery advertises
`state=derivable` for `result_summary`, `artifact_inventory`,
`provenance_manifest`, `result_storyboard`, `evidence_completeness_gate`, etc.,
but `ExportEngine._invoke_provider` → `provider.export()` raises and the engine
records each as a blocked item. **Derivable but not executable.**

## 3. Fresh-database preset listing (reproduced)

```text
FRESH DB preset list count: 0
```

`imputebench/cli/results/preset.py::_get_service()` constructs
`EvidencePresetRepository` + `PresetCodec` + `PresetRegistryService` but never
invokes `BuiltinPresetSeedService`. On a fresh database
`results evidence preset list` prints **"No presets found."** even though the
five YAML resources exist under `imputebench/resources/evidence_presets/`
(`quick_inspect`, `comparison_ready`, `thesis_chapter04`, `thesis_chapter06`,
`debug_inventory`). This is a bootstrap/integration failure, not a missing
resource.

## 4. `export-batch` option surface (before)

`imputebench results evidence export-batch` currently exposes:

```text
--run-id            --dataset-id        --algorithm-id
--execution-class   --phase             --what / --preset
--max-targets       --output-dir        --layout
--overwrite-policy  --strictness        --dry-run
--execute           --confirm-token     --format-output
```

Selection is performed by `cli.results.evidence._select_results`, which calls
`ResultService.list(run_id=None)` (full-table hydration) and filters hydrated
`Result` objects in Python. It supports only run / dataset / algorithm /
execution-class / phase. There is **no** recipe-book, revision, tier/profile,
recipe-entry, algorithm-family, mask-family, rate, realization,
benchmark-contract, graph-policy, status, or unlinked control.

`ResultSelectionQuery` already declares `families`, `mask_families`, `rates`
fields, but `ResultSelectionService.select()` does not pass them and
`ResultSelectionQueryService.query_descriptors()` neither accepts them nor
joins `algorithms` / `maskings`. The SQL-first selection path
(`ResultSelectionService`) is **not wired** into `export-batch`.

## 5. Exporter ownership and executable-gap freeze

Five priority run/result evidence items and their owning providers:

| item_id | provider | current formats | required formats |
|---|---|---|---|
| `result_summary` | core_result | markdown, json | markdown, json (+ CSV sidecars) |
| `artifact_inventory` | core_result | markdown, json | markdown, json, csv |
| `provenance_manifest` | core_result | markdown, json | markdown, json |
| `result_storyboard` | storyboard | markdown, json | **png, json, markdown** |
| `evidence_completeness_gate` | gate | json | json, markdown, csv |

`result_storyboard` currently means narrative metadata; Sheet 11B replaces it
with a reconstruction figure via a new
`ResultReconstructionStoryboardService` (keeping the item id).

All other 32 descriptors must either be wired to an existing domain exporter or
honestly marked `unsupported`/`blocked`/`not_applicable` — never left
`derivable` over the inherited stub.

### Exit gate (11B-00)

- [x] active HEAD resolved (`3cbc8db6`)
- [x] schema version verified (`10`)
- [x] current NotImplementedError captured (all 7 providers)
- [x] fresh-database preset list captured (`0`)
- [x] current batch option surface captured
- [x] all 37 descriptors and inherited exporters inventoried
- [x] every derivable descriptor and exporter owner identified
