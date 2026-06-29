# MOT 11 — ST Compatibility Bridge · Baseline audit

> Captured before any data mutation (§5.1). No SQL changes were applied in this step.

| Field | Value |
|---|---|
| HEAD SHA (submodule EviBench-VIP) | `2f6846419d761c194b8420a9a935a41806d352b4` |
| SCHEMA_VERSION | `13` (MOT 09 baseline, unchanged) |
| Captured at | 2026-06-29 |
| Local DB | `data/metadata.db` |

## ST-like results (stgcn / dcrnn / grin / ignnk)

ST candidate = **unscoped** (`experiment_id` NULL/'') **AND** ST algorithm **AND**
a structural ST signal (`experiment_domain='spatiotemporal'` OR
`actual_execution_class='spatiotemporal'` OR non-empty `graph_id`/`graph_policy`/
`graph_fingerprint`). This is the conjunction predicate of §4.2 — never algorithm-only.

| Metric | Value |
|---|---|
| `backfill_candidate_count` (unscoped ST-like) | **853** |
| All ST-algorithm results | 853 (all currently unscoped) |

### Candidates by algorithm

| Algorithm | Count |
|---|---|
| stgcn | 214 |
| dcrnn | 213 |
| grin | 213 |
| ignnk | 213 |

### Candidates by phase / status / domain / execution class

| Dimension | Distribution |
|---|---|
| `phase_key` | `test`: 853 |
| `status` | `COMPLETED`: 853 |
| `experiment_domain` | `spatiotemporal`: 853 |
| `actual_execution_class` | `spatiotemporal`: 853 |

### Candidates by graph policy

| Graph policy | Count |
|---|---|
| correlation_train_v1 | 489 |
| grid_4n_v1 | 124 |
| distance_knn_v1 | 120 |
| grid_8n_v1 | 120 |

## Interpretation

* The entire ST cohort is `phase_key='test'`, `status='COMPLETED'`,
  `experiment_domain='spatiotemporal'` — confirming §1.4 (ST already carries a
  strong scientific identity) and §2.2 (phase `test` is ST truth and must be
  preserved, never globally rewritten to `execute`).
* All 853 ST results are unscoped (`experiment_id=''`), so none are at risk of an
  overwrite; the bridge only ever fills empty scopes (§5.3).
* The observed local count `853` matches the spec's field note (§2.3). It is a
  field observation, **not** a test invariant — the acceptance tests assert
  relative properties (candidates > 0, applied == approved, remaining == expected).

## Current export-human behaviour (before fix)

Before MOT 11, `results export-human --experiment-id exp_st` with no other filter
could be rejected as `empty_selection` because
`HumanEvidenceExportRequest.has_filters()` did not count `experiment_id`
(§1.6). MOT 11-05 fixes this; see the implementation report.
