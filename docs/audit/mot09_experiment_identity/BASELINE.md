# MOT 09 — Baseline (before implementation)

Captured at the start of the MOT 09 *Experiment Identity and Data Isolation*
implementation, per spec §15 (MOT09-00).

## Repository state

| Field | Value |
|---|---|
| HEAD SHA | `3da72cd40573deb918f6b74bb710d446f11a7992` |
| `SCHEMA_VERSION` (before) | `12` |
| Local DB | `data/metadata.db` |

## Counts (local `data/metadata.db`, schema v12)

| Table | Rows |
|---|---|
| `results` | 2063 |
| `runs` | 506 |
| `artifact_records` | 1162 |
| `runtime_timing_spans` | 11646 |
| `results` with `payload_json.experiment_id` set | 0 |
| `experiments` table | did not exist |

## Pre-implementation `experiment_id` surface

* `runs`, `results`, `artifact_records`, `runtime_timing_spans` had **no**
  `experiment_id` column (only `experiment_domain` on `results`, which is the
  *workflow family*, not a user iteration).
* `HumanEvidenceExportRequest.experiment_id` existed but meant only the **site
  publication directory** (MOT 05); it was not a SQL selection filter.
* `ResultSelectionQuery` / `ResultSelectionQueryService` had no `experiment_id`
  filter — a recipe/tier export could silently include legacy results from any
  prior iteration. This was the observed contamination symptom that motivated
  MOT 09.

## Contamination probe (illustrative)

A recipe/tier `export-human` selection before MOT 09 resolved **all** matching
`results` rows regardless of which experimental campaign produced them, because
no column distinguished `exp1` from `exp2`. After MOT 09, the same selection is
strictly scoped by `results.experiment_id` (or explicitly opted into legacy via
`--legacy-unscoped`).
