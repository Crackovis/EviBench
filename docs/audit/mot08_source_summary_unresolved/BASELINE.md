# MOT 08 — Baseline: `source_summary_unresolved` for classical results

**Spec:** `specs/FIX_SOURCE_SUMMARY_UNRESOLVED_EviBench_ROBUST.md`
**Captured at:** 2026-06-24

## Repository state

| Item | Value |
|---|---|
| HEAD SHA | `02c9325d86e8e0bdf7bfe78fef044ec075c7ed0a` |
| `source_reader.py` blob SHA (HEAD) | `16f5d517e5806cc2b6dc3740e7305418b8fca62b` |
| Target file | `imputebench/services/results_interaction/human_export/source_reader.py` |

## Current reader strategy (pre-patch)

`HumanEvidenceSourceReader.read()` builds a `file_index` keyed by relative path,
then `_build_results(root, file_index)` groups files **by parent directory** and
treats a directory as a result only when it physically contains a
`result_summary.json`:

```python
by_dir[parent].append(source_file)
for directory, files in by_dir.items():
    names = {f.name: f for f in files}
    if "result_summary.json" not in names:
        continue
    summary = read_json(...)
    result_id = _result_id(summary)
```

The result id is derived from the JSON only (`identity.result_id` →
`target.id`). The manifest's `items[].source_ids` association is **never** used
as an index.

## Root cause (confirmed by structure inspection)

A real source manifest encodes each result's `result_summary` item as:

```json
{
  "item_id": "result_summary",
  "provider_id": "core_result",
  "status": "exported",
  "source_ids": ["<result_id>"],
  "files": [{"path": ".../result_summary.json"}, ...]
}
```

i.e. the manifest already carries a reliable `result_id ↔ summary` association
via `source_ids` (written by `CoreResultExporter` via `writer.add_source_ids`).

The directory-grouping reader ignores this and depends on a fragile filesystem
convention:

1. **Directory collision** — if two results map to the same human-readable
   directory (`station/recipe/algo/realization`), the `names = {f.name: f}` dict
   keeps only the **last** `result_summary.json`, silently dropping the other
   result.
2. **Non-colocalised sidecars** — website-aware exports (MOT05/MOT07) can place
   the summary and its storyboard/figure sidecars in *different* directory trees;
   the directory group then lacks the sidecars (or the summary).
3. **Layout drift** — any export whose subdir is not exactly the expected
   convention yields directories the reader does not recognise as results.

When the summary is not found at the expected directory, the planned
`expected_result_id` falls through `_classify_missing()` to
`source_summary_unresolved`, even though `result_summary.json` exists on disk and
is referenced by the manifest.

## Manifest structure (verified against the live exporter)

Reproduced with `standard_cohort()` → `HumanSourceExportService.export()`:

- `items[]` entries have: `item_id`, `provider_id`, `provider_version`,
  `status` (`"exported"`), `files[]` (`path` / `sha256` / `size_bytes`),
  `source_ids[]`, `warnings[]`.
- Per-result items (`result_summary`, `metric_table`, `runtime_breakdown`,
  `comparison_ready_signal`, `provenance_manifest`, `artifact_inventory`,
  and `result_storyboard` when available) all carry the `result_id` in
  `source_ids`.
- File paths may use OS separators (`\\` on Windows); the reader normalises
  to `/`.
- `blocked_items[]` carry `item_id`, `target_id`, `provider_id`,
  `blocking_reasons[]`, `error_class`.

## Field baseline (production exports)

The spec records the pre-fix field counts (from the user's exp1/exp2 hub
exports):

| Experiment | `source_summary_unresolved` before |
|---|---|
| exp1 | 330 |
| exp2 | 390 |

These exports are produced from the production database/hub and are **not**
checked into this repository, so the offline baseline here is limited to the
code-level root cause and the live manifest structure above. The re-export
verification (spec §11.3 / §11.5) must be run in the environment that holds the
exp1/exp2 data; the post-fix counts are recorded in `IMPLEMENTATION_REPORT.md`.

## Pre-patch test coverage

`tests/human_evidence/test_source_reader.py` only exercises the colocalised
happy path (summary + signal in the same directory) plus the missing/bad
manifest guards. `tests/human_evidence/mot06/test_source_summary_missing_classical.py`
checks zero-missing for a clean colocalised cohort. Neither covers
manifest-index resolution, non-colocalised sidecars, invalid JSON, or duplicate
summaries — the gaps MOT 08 closes.
