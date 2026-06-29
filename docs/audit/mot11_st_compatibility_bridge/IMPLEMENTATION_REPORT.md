# MOT 11 — ST Compatibility Bridge · Implementation report

| Field | Value |
|---|---|
| HEAD SHA (at baseline) | `2f6846419d761c194b8420a9a935a41806d352b4` |
| SCHEMA_VERSION | `13` (unchanged — MOT 11 adds no migration) |
| Date | 2026-06-29 |
| DB backup | `data/metadata.db.pre_mot11_20260629_073354.bak` |
| Agent | Sisyphus (implementation) |

## Baseline counts (before mutation)

| Metric | Value |
|---|---|
| Unscoped ST-like candidates | 853 |
| stgcn / dcrnn / grin / ignnk | 214 / 213 / 213 / 213 |
| phase_key | `test`: 853 |
| status | `COMPLETED`: 853 |
| graph_policy | correlation_train_v1 489, grid_4n_v1 124, distance_knn_v1 120, grid_8n_v1 120 |

See [BASELINE.md](BASELINE.md) for the full capture.

## Files changed

### Required (4)
* `imputebench/domain/evidence/human_export.py` — `has_filters()` now counts
  `experiment_id` and `legacy_unscoped` as narrowing filters (MOT 11-05).
* `imputebench/cli/experiment/st/campaign.py` — `experiment st experiment run`
  gains `--experiment-id`; echoes `ExperimentID:` only when non-empty (MOT 11-03).
* `imputebench/services/spatiotemporal/st_experiment_orchestrator.py` —
  `run(experiment_id=...)`, stored on `ExperimentPlan`, threaded to the plan
  service (MOT 11-04).
* `imputebench/services/spatiotemporal/st_plan_service.py` —
  `create_plans(experiment_id=...)` and `_build_run(experiment_id=...)` set
  `run.experiment_id` only when non-empty; ST scientific identity untouched.

### New tooling
* `imputebench/services/experiment/st_bridge_backfill_service.py` —
  `STBridgeBackfillService` (multi-field ST predicate, plan, transactional apply
  delegating to the MOT 09 backfill engine, never overwrites scoped rows, never
  mutates `phase_key`).
* `imputebench/cli/experiment/registry.py` — `inventory-st-bridge`,
  `plan-st-bridge-backfill`, `apply-st-bridge-backfill`.

### Docs / audit
* `docs/ST_Compatibility_Bridge.md`
* `docs/audit/mot11_st_compatibility_bridge/BASELINE.md`
* `docs/audit/mot11_st_compatibility_bridge/st_inventory.json`
* `docs/audit/mot11_st_compatibility_bridge/ST_BACKFILL_PLAN.csv`
* `docs/audit/mot11_st_compatibility_bridge/IMPLEMENTATION_REPORT.md` (this file)

### Tests
* `tests/st_bridge/` — 7 modules (CLI, orchestrator, plan, backfill plan, phase
  preservation, export selection, hub integration).
* `tests/human_evidence/test_export_human_experiment_id_filter.py`.

## Backfill

| Metric | Value |
|---|---|
| Plan path | `docs/audit/mot11_st_compatibility_bridge/ST_BACKFILL_PLAN.csv` |
| Approved rows | 853 (via `--approve-suggested`, predicate-matched cohort) |
| Applied results | 853 |
| Applied runs | 176 |
| Skipped (already scoped to other) | 0 |
| Remaining unscoped ST candidates | **0** |
| phase_key after backfill | `test`: 853 (preserved — no `execute` rewrite) |
| Registry `exp_st` result_count / run_count | 853 / 176 |

No automatic algorithm-only migration was used; no result already scoped to a
different experiment was touched; the four tables (runs/results/artifact_records/
runtime_timing_spans) were updated in a single transaction by the reused MOT 09
engine.

## New ST smoke run

Deferred for this pass (live execution focused on scoping the existing 853 legacy
results). The CLI path is covered by `tests/st_bridge/test_st_experiment_id_cli.py`
and the propagation by the plan/orchestrator tests. To create a scoped run:

```bash
imputebench experiment st experiment run --experiment-id exp_st_smoke \
  --tier smoke --algorithm stgcn --graph-policy grid_4n_v1 \
  --mask-family mcar --realizations 1 --no-evidence
```

## Export-human command (executed)

```bash
imputebench results export-human \
  --experiment-id exp_st \
  --algorithm-id stgcn --algorithm-id dcrnn --algorithm-id grin --algorithm-id ignnk \
  --phase test --primary-metric rmse --pack-format complete \
  --output-dir docs/.private_docs/exp_evidences \
  --hub --framework auto --storyboards representative \
  --max-targets 5000 --overwrite-policy replace-generated
```

The pack metadata `title` is the default `Evidence pack`, **consistent with the
existing `exp1` and `exp2` packs** (the MOT 07 rich hub cards key off
`experiment_id`, not the title). A title override was tried but correctly refused
(`HRE-PACK-001`, reuse-identical fingerprint differs) and would have made `exp_st`
inconsistent with the other experiments, so it was not pursued. The registry entry
`exp_st` carries the full human title separately.

| Metric | Value |
|---|---|
| SQL selection (`selected_count`) | **853** (experiment_id scope works end-to-end) |
| pack_grade / claim_level | `exploratory` / `exploratory_only` (bounded, no overclaim) |
| blocked_item_count | 1706 (ST storyboards/figures — ST source providers deferred to MOT 12) |
| Hub experiments listed | `exp_st`, `exp1`, `exp2` |
| UUID leak in hub | none |

The SQL-first selection matched **all 853** ST results by `experiment_id` alone —
the core bridge goal. Downstream storyboard/figure blocking is the **deferred**
ST-specific source-provider work (§11), not a bridge defect: the pack still
publishes and the hub lists `exp_st` through the existing pipeline, with no
ST-specific export pipeline.

## Hub paths

```
docs/.private_docs/exp_evidences/exp_st/metadata.json
docs/.private_docs/exp_evidences/exp_st/index.html
docs/.private_docs/exp_evidences/index.html
docs/.private_docs/exp_evidences/site_hub_manifest.json
```

## Tests run

| Suite | Result |
|---|---|
| `tests/st_bridge` + filter test | 20 passed |
| `tests/experiment_identity` | 81 passed |
| `tests/human_evidence` | 346 passed |
| `tests/results_interaction` | 368 passed |

Pre-existing, unrelated: `tests/spatiotemporal/test_st_recipe_materialization.py`
fails with `KeyError: 'calibration_scientific'` (file outside this change set);
`test_st_plugin_device_contract.py` and a duplicate `test_experiment_id_validation.py`
basename are pre-existing collection issues.

## Known limitations / deferred (MOT 12)

* ST-specific storyboard / source providers for `export-human` (high
  `blocked_item_count`); graph-sensitivity figures and graph-policy comparison
  cards in the unified hub.
* Cross-family temporal-vs-ST scientific ranking (intentionally not claimed).
* New ST runs remain unscoped by default; official campaigns must pass
  `--experiment-id`.

## Acceptance summary

* New ST runs accept and propagate `experiment_id` → results inherit it ✔
* Legacy ST inventory + approved, non-heuristic backfill; no phase mutation; no
  overwrite of scoped rows ✔
* `export-human --experiment-id exp_st` works without a recipe book; `experiment_id`
  is a narrowing filter; phase `test` works ✔
* `exp_st` published under the site root and listed alongside `exp1`/`exp2`;
  metadata public-safe ✔
* No temporal-export regression; ST gates and specialised ST export untouched;
  selection stays SQL-first descriptor-only ✔
