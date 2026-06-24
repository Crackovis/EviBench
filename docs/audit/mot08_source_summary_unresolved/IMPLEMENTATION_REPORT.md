# MOT 08 — Implementation report: robust `source_summary` resolution

**Spec:** `specs/FIX_SOURCE_SUMMARY_UNRESOLVED_EviBench_ROBUST.md`
**Baseline:** `BASELINE.md` (HEAD `02c9325d86e8e0bdf7bfe78fef044ec075c7ed0a`)
**Implemented:** 2026-06-24
**Policy:** local implementation only; no push.

## Root cause

The human source reader rebuilt `SourceResult`s by **parent directory** and
derived the result id from the summary JSON only. The source manifest already
carries the reliable `result_id ↔ summary` association via `items[].source_ids`
(written by `CoreResultExporter.add_source_ids(result_id)`), but the reader never
used it as an index. Any export whose layout diverged from the
`station/recipe/algo/realization` convention — non-colocalised sidecars
(website-aware MOT05/MOT07 exports), per-item subdirs, or a summary whose JSON
omits `identity.result_id` — caused planned ids to fall through
`_classify_missing()` to `source_summary_unresolved`, even though
`result_summary.json` existed on disk and was referenced by the manifest.

## Fix (reader-first, backward-compatible)

The reader now treats the manifest as the canonical index. Resolution order:

1. **Index** every `items[]` entry into `ManifestItemRef` (item id, status,
   `source_ids`, files, ordinal) plus a flat path→`SourceFile` index, in one pass.
2. **Locate** result-summary items: `item_id == "result_summary"`, status in
   `{"", "exported"}`, with a `result_summary.json` file.
3. **Key** each summary by the JSON `result_id` (`identity.result_id` →
   `target.id` → legacy top-level `result_id`); fall back to the item's best
   `source_ids` entry when the JSON carries no id.
4. **Attach** sidecars by `source_ids` first, then by shared directory
   (backward-compatible fallback), so a non-colocalised storyboard/signal is
   still recovered.
5. **Classify** failures distinctly: invalid JSON → `invalid_summary_ids` +
   `source_summary_invalid_json` reason (never silently missing); divergent
   duplicates → deterministic last-item + `duplicate_result_summary` warning;
   planned-but-absent → `source_summary_unresolved`; provider-blocked → carries
   the `error_class`.
6. **Fallback** to the historical directory-grouping path verbatim when a
   manifest records no `result_summary` item (truly legacy exports).

No metric is recomputed, no Markdown is parsed, no provider is re-run, and no
writer-only field was introduced — existing exports are fixed at read time.

## Files changed

| File | Change |
|---|---|
| `imputebench/services/results_interaction/human_export/source_reader.py` | Rewritten: `ManifestItemRef`, `SummaryResolution`, `_index_manifest_items`, `_summary_refs`, `_resolve_summaries`, `_related_files`, `_read_optional`, `_best_source_key`, manifest/legacy dispatch in `_build_results`; `_result_id` now v1/legacy-aware; `SourceExportBundle` gains `unresolved_summary_ids`, `duplicate_summary_ids`, `summary_resolution_report` and folds invalid-JSON reasons into `missing_summary_reasons`. |
| `imputebench/services/results_interaction/human_export/human_evidence_export_service.py` | `_blocked_items()` now emits a distinct `source_summary_invalid` blocked-evidence entry for `invalid_summary_ids` (malformed summaries surfaced, not hidden). |

`SourceExportBundle` field additions are additive/optional; the existing
`selection_ledger_service` and `CoverageLedger` consumers are unchanged.

## Tests added

| File | Cases |
|---|---|
| `tests/human_evidence/test_source_reader_summary_resolution.py` | 10 — source_ids resolution, non-colocalised storyboard, JSON-id-without-source_ids, legacy v1, invalid JSON, identical-file dedupe, divergent-duplicate determinism + warning, planned-unresolved, manifest-blocked error_class, invalid optional sidecar isolation. |
| `tests/human_evidence/test_source_reader_classical_regression.py` | 2 — full 4-algorithm (linear/nearest/cubic_spline/galpi) non-colocalised export resolves with zero missing + sidecars attached; direct contrast proving the legacy directory path loses the displaced sidecars the manifest index recovers. |
| `tests/human_evidence/test_human_pack_source_summary_resolution.py` | 3 — end-to-end pack (dry-run + full-run provenance) coverage ledger = 4/4 with no `source_summary_missing`; `source_ids`-only summaries (no JSON `result_id`) that the old reader dropped now resolve the whole pack. |

The pre-existing `tests/human_evidence/test_source_reader.py` (colocalised happy
path + guards) and `tests/human_evidence/mot06/test_source_summary_missing_classical.py`
(zero-missing for a clean cohort) continue to pass unchanged.

## Verification (code level, this environment)

| Gate | Result |
|---|---|
| `pytest tests/human_evidence/test_source_reader.py -q` | pass (3) |
| `pytest tests/human_evidence/test_source_reader_summary_resolution.py -q` | pass (10) |
| `pytest tests/human_evidence/test_source_reader_classical_regression.py -q` | pass (2) |
| `pytest tests/human_evidence/test_human_pack_source_summary_resolution.py -q` | pass (3) |
| `pytest tests/human_evidence -q` | **328 passed** |
| `pytest tests/human_evidence tests/results_interaction -q` | **711 passed** |

(The full `pytest -q` carries ~600 unrelated pre-existing failures outside the
evidence surface; per project guidance the focused suites above are the gate.)

### Before / after — summary resolution (code level)

| Scenario | Old reader | New reader |
|---|---|---|
| Summary keyed only by `source_ids` (no JSON `result_id`) | dropped → `source_summary_unresolved` | resolved (manifest_source_id) |
| Storyboard/signal in a separate tree | summary resolved, sidecar **lost** | resolved, sidecar **attached** |
| Two divergent summary items for one id | non-deterministic / last on disk | deterministic last-in-manifest + warning |
| Malformed `result_summary.json` | propagated/ambiguous | `invalid_summary_ids` + specific reason |
| Truly legacy manifest (no `result_summary` item) | directory grouping | directory grouping (unchanged) |

## Field re-export (exp1 / exp2) — to run where the data lives

The exp1/exp2 hub exports are produced from the production database/hub and are
**not** checked into this repository, so the field counts in spec §12.5
(exp1 before 330, exp2 before 390) cannot be reproduced offline here. Run the
spec §11.3 / §11.5 commands in the environment that holds the data and record
the post-fix counts below:

```bash
imputebench results export-human --experiment-id exp2 --hub \
  --output-dir docs/.private_docs/exp_evidences \
  --recipe-book official_londonaq_classical_benchmark --tier all \
  --algorithm-id linear_interpolation --algorithm-id nearest_interpolation \
  --algorithm-id cubic_spline --algorithm-id galpi \
  --framework auto --overwrite-policy replace-generated --format-output json
# then repeat with --experiment-id exp1
```

Inspect the published `provenance/selection_ledger.json` (`source_summary_success`
== expected result count, empty `missing_summary_ids`/`invalid_summary_ids`) and
`provenance/blocked_items.json` (no `source_summary_missing` /
`source_summary_unresolved` reason codes). Given the manifests are clean and the
summaries exist, the expected post-fix `source_summary_unresolved` is **0** (or
the residual is a justified genuine absence carrying a specific
`error_class` / `source_target_not_planned` reason).

| Experiment | `source_summary_unresolved` before | after |
|---|---|---|
| exp1 | 330 | _record after re-export_ |
| exp2 | 390 | _record after re-export_ |

## Remaining blockers

None at the code level — all focused and regression suites are green. Residual
field blockers, if any after re-export, are expected to be genuine absences
classified with a specific reason (provider `error_class` or
`source_target_not_planned`), never a generic `source_summary_unresolved`.

## Deferred (per spec §7 / §14)

`ExportedItem.target_id` / `summary_path` shortcut fields and a
`results source-read-audit` CLI remain optional future work (MOT 08a / MOT 09).
The reader intentionally does **not** depend on them.
