# ST Compatibility Bridge (MOT 11)

A thin, safe bridge that lets spatio-temporal (ST) results — **STGCN, DCRNN,
GRIN, IGNNK** — join the unified experiment pipeline:

```
experiment_id → SQL-first selection → results export-human → website-aware pack → evidence hub
```

The ST lane keeps its own graph assets, graph-aware mask banks, plans, gates and
specialised exports. The bridge only gives ST a *passport* (`experiment_id`) so
it shows up in the unified hub next to the classical experiments — nothing in the
ST scientific identity (graph identity, `phase_key='test'`, benchmark contracts)
changes.

## What changed

| Area | Change |
|---|---|
| ST CLI | `experiment st experiment run` gains `--experiment-id` (empty = legacy). |
| ST orchestrator / plan service | thread `experiment_id` onto every new `ExperimentRun`; results inherit it via `ResultService.save()`. |
| export-human | `experiment_id` now counts as a narrowing filter, so a scoped export needs no other filter. |
| Registry tooling | `inventory-st-bridge`, `plan-st-bridge-backfill`, `apply-st-bridge-backfill` for the legacy ST cohort. |

## New ST runs

```bash
imputebench experiment st experiment run \
  --experiment-id exp_st \
  --tier A \
  --algorithm stgcn --algorithm dcrnn --algorithm grin --algorithm ignnk \
  --graph-policy correlation_train_v1
```

Empty `--experiment-id` stays legacy-compatible. For official/public ST campaigns,
always pass `--experiment-id exp_st` (or `exp_st_v2`, …).

## Backfilling legacy ST results

Legacy ST results predate `experiment_id`. They are scoped only after an explicit,
reviewed, approved plan — **never** by an automatic algorithm-only migration.

```bash
# 1. Register the experiment scope
imputebench experiment registry create exp_st \
  --title "Spatio-temporal benchmark — London AQ graph-aware imputation" \
  --description "STGCN, DCRNN, GRIN and IGNNK on London AQ graph-aware benchmark supports." \
  --dataset-id london_aq \
  --algorithm-id stgcn --algorithm-id dcrnn --algorithm-id grin --algorithm-id ignnk \
  --tag st --tag graph-aware --tag thesis

# 2. Inventory the legacy ST cohort (read-only)
imputebench experiment registry inventory-st-bridge \
  --output docs/audit/mot11_st_compatibility_bridge/st_inventory.json

# 3. Write a review plan (suggests exp_st via the multi-field ST predicate)
imputebench experiment registry plan-st-bridge-backfill \
  --experiment-id exp_st \
  --output docs/audit/mot11_st_compatibility_bridge/ST_BACKFILL_PLAN.csv

#    Review the CSV, then set user_approved_experiment_id=exp_st for verified rows
#    (or re-run step 3 with --approve-suggested to pre-fill the whole matched cohort).

# 4. Apply (re-checks each row is still unscoped before writing)
imputebench experiment registry apply-st-bridge-backfill \
  --plan docs/audit/mot11_st_compatibility_bridge/ST_BACKFILL_PLAN.csv \
  --apply --auto-create
```

Safety guarantees:

* **multi-field predicate** — an ST `algorithm_id` is necessary but not
  sufficient; a graph / domain / execution-class signal must also be present;
* **never overwrites** a result already scoped to a different experiment;
* **never mutates `phase_key`** — ST stays `test` (public label may read
  "benchmark evaluation", but SQL is untouched);
* **one transaction**, propagating the scope to runs / results / artifact_records
  / runtime_timing_spans so the four tables stay consistent;
* a **plan file is always written** before anything is applied.

## Exporting ST to the hub

After scoping, ST exports through the *existing* `results export-human` use case —
no ST-specific export pipeline:

```bash
imputebench results export-human \
  --experiment-id exp_st \
  --algorithm-id stgcn --algorithm-id dcrnn --algorithm-id grin --algorithm-id ignnk \
  --phase test --primary-metric rmse --pack-format complete \
  --output-dir docs/.private_docs/exp_evidences \
  --hub --framework auto --storyboards representative \
  --max-targets 5000 --overwrite-policy replace-generated
```

A minimal scoped export is now also valid (thanks to the `has_filters()` fix):

```bash
imputebench results export-human --experiment-id exp_st --phase test \
  --output-dir docs/.private_docs/exp_evidences --hub
```

## Claim boundary

The hub card for `exp_st` stays bounded:

> Spatio-temporal results are reported for the selected London AQ graph-aware
> benchmark supports.

It must **not** claim ST superiority, state-of-the-art status, or that graph-aware
methods outperform temporal methods. Cross-family scientific ranking is
deliberately deferred (see MOT 12 — ST Human Evidence Enrichment).
