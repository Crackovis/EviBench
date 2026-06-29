# MOT 12 — ST Source Providers · Baseline audit

| Field | Value |
|---|---|
| HEAD SHA | `0f28c1315fb077cfa32d50669124ec492e7f5c3f` |
| Date | 2026-06-29 |
| Scope under test | `exp_st` (the 853 legacy ST results scoped in MOT 11) |

## ST result selection

| Metric | Value |
|---|---|
| `export-human --experiment-id exp_st` selected | 853 (SQL selection works since MOT 11) |
| ST algorithms | stgcn 214, dcrnn 213, grin 213, ignnk 213 |

## Blocked-item picture before MOT 12 (from the MOT 11 export)

* `blocked_item_count` ≈ 1706 — dominated by **`StoryboardBlocked`** with the
  generic reason *"No exact evaluation mask could be resolved; a reconstruction
  error figure would be scientifically ambiguous."*
* Root cause: `ReconstructionSourceResolver` only inspected persisted result mask
  fields + catalog artifacts; it never followed the ST
  `benchmark_mask_bank_id → artifact_dir → realization → mask.npy` chain.

## Critical finding — legacy ST mask banks are not on disk / not in DB

The 853 legacy ST results reference **44 distinct `benchmark_mask_bank_id`**, but
**0 of 44 resolve** to a `BenchmarkMaskBank` row (97 unrelated banks exist in the
DB). The graph-aware mask banks for the legacy cohort were removed during an
earlier DB cleanup.

Consequence for MOT 12:

* For the **legacy 853**, the exact evaluation mask is **genuinely absent**. After
  MOT 12 these rows block with the precise code **`STMaskBankMissing`** instead of
  the generic ambiguous message — an honest blocker, not a provider gap (§0, §2.1).
* Storyboard *rendering* for ST is validated against a **fresh** scoped run
  (`exp_st_v2`, produced by a GPU re-run — MOT 10 / deferred), which will carry
  resolvable banks + on-disk `mask.npy`. MOT 12 ships and unit-tests the resolver,
  the chain integration and the graph-node alignment so that fresh runs render.

## result_summary independence (already satisfied)

`CoreResultExporter.result_summary()` already builds `result_summary.json` from
persisted metrics (`metrics.global/split/pollutant/node_summary`) and registers
`result_id` in `source_ids`, **independently of the storyboard provider**. ST
results carry `node_metrics` (140 nodes), so `metrics.node_summary` is populated.
MOT 12 verifies this with a focused test rather than adding a second summary path.

See [SHAPE_AUDIT.md](SHAPE_AUDIT.md) for prediction/mask/original shapes.
