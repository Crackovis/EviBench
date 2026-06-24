# Results Interaction & Curated Evidence CLI — Update Report (Sheet 11)

**Branch:** `sheet11-evidence-providers`
**Audit baseline:** `efc6df0351e7bd946209501a60dd910c6064fecb`, SQLite schema v8.
**Status as of 2026-06-14:** Phases 11-00 → 11-10 complete and committed. This report
is the final closure deliverable (§23).

## Schema migration

- `SCHEMA_VERSION` 8 → **9** → **10** (`imputebench/storage/sqlite/schema.py`).
- **v9** (Phase 11-02): nullable lineage columns on `runs` and `results`:
  `recipe_materialization_id, recipe_book_id, recipe_revision, recipe_entry_id,
  recipe_profile_id, recipe_definition_sha256` with batch-filter indexes.
- **v10** (Phase 11-07): `evidence_presets` table (id, title, status, origin,
  protected, archived, schema_id, current_revision, content_sha256, definition_json)
  and `evidence_preset_revisions` table (immutable revision chain with
  optimistic concurrency, per §12.5).
- Verified additive/non-destructive on live DB; live DB migrated to v10.

## Commands added / preserved

| Command | State |
|---------|-------|
| `results inspect TARGET_ID` | **added** (Phase 11-03) — result/run/comparison projection |
| `results compare results/runs/query/table` | **added** (Phase 11-05) — ephemeral compatibility-gated comparison |
| `results evidence list` | **added** (Phase 11-06) — read-only evidence discovery, 7 states |
| `results evidence preset list/show/create/clone/update/delete/validate/export` | **added** (Phase 11-07) — versioned YAML/JSON presets, 5 built-ins |
| `results evidence export` | **added** (Phase 11-08) — single-target selective export |
| `results evidence export-batch` | **added** (Phase 11-08) — batch export with selection filters |
| `results result *` | preserved (golden-frozen) |
| `results compare list/show/delete` | preserved |
| `results view`, `results gate` | preserved |
| `experiment run show`, temporal/ST evidence | preserved |

Eight visible root domains unchanged: `admin data experiment lab methods results
study thesis`. No ninth root introduced.

## Phase status

| Phase | Title | Status | Key artifacts |
|-------|-------|--------|---------------|
| 11-00 | Re-audit & golden freeze | ✅ | `scripts/audit/sheet11_golden_freeze.py`, `docs/audit/sheet11_golden/`, `docs/audit/Sheet11_Phase11-00_Audit_Findings.md` |
| 11-01 | Read models, metric registry, renderers | ✅ | `imputebench/cli/rendering/`, `imputebench/read_models/results_interaction/`, `services/results_interaction/metric_registry.py` |
| 11-02 | Recipe lineage & SQL queries | ✅ | migration v9, mappers/repos, `recipe_lineage_resolver.py`, `result_selection_queries.py` |
| 11-03 | Target resolution & inspect | ✅ | `target_resolver.py`, `inspection_service.py`, `cli/results/inspect.py` |
| 11-04 | Selection & compatibility | ✅ | `comparison_compatibility_service.py`, `selection_service.py`, `comparison_service.py` |
| 11-05 | Comparison rendering & aggregation | ✅ | `comparison_report_builder.py`, `cli/results/compare_interactive.py` |
| 11-06 | Evidence providers & inventory | ✅ | 7 providers (37 types), inventory service, `results evidence list` |
| 11-07 | Preset schema & registry | ✅ | codec, repository, 5 built-in YAML presets, preset CLI (8 subcommands) |
| 11-08 | Selective export engine | ✅ | ExportPlanner, ExportEngine (9-step), `evidence export` + `export-batch` |
| 11-09 | Wrappers & documentation | ✅ | Compatibility verified (12 commands), 6 new docs, updated docs |
| 11-10 | Scientific regression & closure | ✅ | Full suite: 329 SI + 244 cross-domain, all §21 gates passed |

## Read models and services delivered

Read models (`imputebench/read_models/results_interaction/`): `target`,
`inspection`, `selection`, `comparison`, `evidence`, `export`, `preset` — frozen
dataclasses, JSON-serializable, carrying canonical schema strings.

Services (`imputebench/services/results_interaction/`): `metric_registry`,
`recipe_lineage_resolver`, `target_resolver`, `inspection_service`,
`selection_service`, `comparison_compatibility_service`, `comparison_service`.

Renderers (`imputebench/cli/rendering/`): plain (baseline), optional rich,
JSON (single-document, NaN→null), CSV (+sidecar), Markdown; format resolution
honouring `NO_COLOR`/`TERM=dumb`/CI/pipes; ASCII glyph degradation for legacy
Windows consoles.

## Compatibility matrix (Phase 11-04)

Strict default **blocks** global ranking on: mixed contract, mixed mask bank,
mixed evaluation support, mixed phase, mixed dataset, ST graph mismatch, and
mock+scientific. `graph_policy` as grouping variable → one cohort. No benchmark
metadata → `exploratory_only`. `--allow-incompatible` → `partitioned` cohorts,
global ranking/BEST disabled, every reason emitted. Composes the authoritative
`evaluate_shared_benchmark_parity`.

## Test results

`tests/results_interaction/` — **329 passing**: rendering/JSON discipline, metric
registry, read-model schemas, recipe lineage persistence + migration, target
resolver, inspection service + CLI, compatibility (all §20.4 cases), selection
ambiguity, unit normalization, comparison report builder (aggregation/dispersion/
BEST gating per §20.5), interactive compare CLI (§20.6), evidence providers (51),
evidence inventory (21), evidence CLI (12), preset codec (27), preset repository
(18), preset registry (19), preset CLI (13), export planner (24), export engine
(22), export CLI (10). Zero regressions across all phases.

Pre-existing, unrelated: `tests/cli/test_compare_crud.py` (3) fail because the
legacy `compare` alias's deprecation banner (correctly on stderr) is mixed into
`CliRunner.output`; the canonical `results compare list --format json` stdout is
clean. Confirmed failing identically before Phase 11-03.

## Known limitations / deferred

- `prediction_sample` / `mask_visualization` temporal items remain `unsupported`
  until concrete providers exist (per §9.4).
- Provider export implementations (plan/export methods) delegate to existing
  domain services; full wiring deferred.

## §21 Acceptance gates

All quantitative acceptance gates verified:

| Gate | Status |
|------|--------|
| Visible root domains: exactly 8 | ✅ |
| New root-level result/evidence domains: 0 | ✅ |
| Broken existing result commands: 0 | ✅ |
| Broken compare lifecycle commands: 0 | ✅ |
| Broken temporal evidence commands: 0 | ✅ |
| Broken ST evidence commands: 0 | ✅ |
| Rankings across mixed contracts: 0 | ✅ |
| Rankings across mixed mask banks: 0 | ✅ |
| Rankings across mixed support fingerprints: 0 | ✅ |
| Rankings across mixed phases/datasets: 0 | ✅ |
| ST rankings across incompatible graphs: 0 | ✅ |
| Mock/scientific global rankings: 0 | ✅ |
| Silent realization aggregation: 0 | ✅ |
| Silent phase selection: 0 | ✅ |
| Advertised evidence items without providers: 0 | ✅ |
| Unrequested exported items: 0 | ✅ |
| Exports without manifest: 0 | ✅ |
| Published files without checksum: 0 | ✅ |
| Mutable built-in presets: 0 | ✅ |
| Preset revisions without hash: 0 | ✅ |
| Unsafe YAML constructors: 0 | ✅ |
| Arbitrary path-template expressions: 0 | ✅ |
| Recipe-filtered results without explicit lineage: 0 | ✅ |
| Heuristic recipe lineage from names: 0 | ✅ |
| Documentation command drift: 0 | ✅ |

## Final metrics

- **Phases completed:** 11 of 11
- **Tests passing:** 329 (results_interaction) + 244 (cross-domain) = 573 total
- **Schema version:** v10
- **Evidence providers:** 7 (37 types)
- **Evidence presets:** 5 built-in, versioned, immutable
- **CLI commands added:** inspect, evidence list/preset/export/export-batch, compare results/runs/query/table
- **CLI commands preserved:** 12 legacy commands verified
- **Documentation:** 6 new docs, 3 updated, preset index
