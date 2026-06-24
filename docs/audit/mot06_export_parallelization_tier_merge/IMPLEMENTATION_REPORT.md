# MOT06 — Implementation Report

Export Parallelization, Classical Source Summaries, and Tier Merge for
EviBench-VIP. Implemented locally in the spec's mandated order; no push.

## HEAD

- Branch `main`, baseline HEAD `b298b91daa3c8842fae0548362a9243466951d88`.
- MOT04 (parallel export) + MOT05 (website-aware export) already landed at
  baseline; MOT07 (site overhaul) was being implemented **in parallel** in the
  same working tree. MOT06 changes were kept surgical (new files + minimal,
  locally-imported orchestrator hooks) so the two streams coexist — the combined
  focused suite is green (see Regression).

## Root causes — confirmed vs corrected

| Spec hypothesis | Finding |
|---|---|
| Bug C: classical results vanish as `source_summary_missing` because the source pipeline never writes `result_summary.json` | **Corrected.** The v2 writer (`providers/core_result_exporters.py::_result_summary_for_result`) is *unconditional* for any resolvable result; classical completed results already produce summaries (probe: 10/10, `missing_summary_ids == ()`). The brittle path the draft named (`services/source_export/providers/result_summary.py`) does not exist; the real provider is `core_result`. The genuine gaps were (a) no explicit `execution`/`training` blocks and (b) a generic blocked reason. |
| Bug B: tier a then tier b overlays instead of merging | **Confirmed.** No merge state existed. Added `site_experiment_state.json` + merge-before-selection + union rebuild. |
| Bug A: source export sequential; needs parallelism | **Largely pre-done by MOT04** (parallel policy, threaded hashing, write scheduler, process-safe chart/storyboard workers, performance trace). MOT06 added the named `parallel_file_ops.py`, the `HRE-PERF-*` codes, and determinism/equivalence tests. |

## Files changed (MOT06)

Modified:
- `imputebench/domain/evidence/human_export.py` — `experiment_merge_policy`,
  `recipe_profile_ids`, `TIER_ALL`, `EXPERIMENT_MERGE_*`, `HRE-SITE-MERGE-001..007`,
  `HRE-PERF-001..004`, validation.
- `imputebench/services/results_interaction/providers/_projections.py` —
  `execution_projection`, `training_projection` (no fabricated DL diagnostics).
- `imputebench/services/results_interaction/providers/core_result_exporters.py` —
  v2 summary now carries `execution` + `training` blocks.
- `imputebench/services/results_interaction/human_export/source_reader.py` —
  `missing_summary_reasons` (specific reasons §3.7).
- `imputebench/services/results_interaction/human_export/selection_service.py` —
  multi-profile union + `all` expansion (single-profile path unchanged).
- `imputebench/services/results_interaction/human_export/human_evidence_export_service.py` —
  merge-before-selection hook + state write (local imports; minimal footprint);
  specific blocked-reason message.
- `imputebench/cli/results/export_human.py` — `--experiment-merge-policy`,
  `--tier all`, `recipe_profile_ids` passthrough.

New:
- `services/.../human_export/site_experiment_state_service.py` — state model,
  load/validate, conflict gating, effective (union) request, slice recording.
- `services/.../human_export/recipe_profile_resolver.py` — `--tier all` →
  exportable profiles (excludes `runtime_only`/smoke), explicit failure.
- `services/.../human_export/parallel_file_ops.py` — deterministic
  `hash_files_parallel` / `checksums_text_parallel` / `copy_files_parallel` /
  `write_text_files_parallel`.
- `scripts/benchmark_human_export_mot06.py` — worker-count benchmark + speedups.
- `tests/human_evidence/mot06/` — 15 test files (64 tests).
- `docs/audit/mot06_export_parallelization_tier_merge/` — BASELINE + this report.

## Classical source summary coverage — before/after

- Before: classical v2 summary had no `execution`/`training` block; a missing
  summary reported only `source_summary_missing`.
- After: every classical summary carries `execution.actual_execution_class` and
  `training = {"status": "not_applicable", "reason": "classical single-shot
  result"}`; DL summaries point to the `training` provider
  (`instrumented_elsewhere`) without fabricating loss/checkpoint/epoch/LR.
  `source_summary_missing` for completed classical results with metrics: **0**
  (gate test). A genuinely missing summary now carries a specific reason
  (`source_target_not_planned` / `source_export_blocked: …` / `…unresolved`).

## Tier merge — state schema example (`provenance/site_experiment_state.json`)

```json
{
  "schema": "imputebench.human-evidence-site-state/v1",
  "experiment_id": "exp1",
  "merge_policy": "merge",
  "identity": {"dataset_id": "ds1", "recipe_book_id": "official_londonaq_classical_benchmark",
               "recipe_revision": null, "benchmark_contract_id": "contract_a", "primary_metric": "rmse"},
  "export_slices": [
    {"slice_id": "...tier_a", "recipe_profile_ids": ["a"], "source_result_count": 2, "...": "..."},
    {"slice_id": "...tier_b", "recipe_profile_ids": ["b"], "source_result_count": 2, "...": "..."}
  ],
  "effective_selection": {"recipe_profile_ids": ["a", "b"], "source_result_count": 4, "...": "..."}
}
```

End-to-end (`test_tier_merge_rebuilds_full_pack.py`): tier a then tier b under
one `experiment_id` yields slices `[["a"], ["b"]]`, effective profiles
`["a","b"]`, deduplicated `source_result_count == 4`, and `selection.json` with
all four ids (pack rebuilt from the union, not overlaid). `replace` keeps only
the current tier; sibling experiments are untouched. Conflicts
(`dataset`/`contract`/`metric`/`recipe_revision`) block with
`HRE-SITE-MERGE-003..006`.

## Parallel policy

Defaults (unchanged from MOT04 to avoid regressing its CLI tests): `parallel`
on, `max_workers=4` (0 = auto → `min(8, cpu_count)`; 1 = strict sequential;
`<0` rejected). Deterministic primitives sort inputs and outputs by relative
path; worker failures raise `HRE-PERF-002` deterministically (first by sorted
key); Matplotlib + storyboard fan-out use module-level process-safe workers
(`plt.close` in the renderer) — no provider/service/SQLite is pickled.

### Note on the spec's `max_workers=0` default

The spec §5.1 suggests a default of `0`; MOT04 shipped `4`. Kept `4` because (a)
both auto-resolve to a sane bounded worker count and (b) MOT04's CLI test pins
`--max-workers [default: 4]`. Documented rather than silently regressed.

## Sequential vs parallel equivalence

`test_parallel_sequential_equivalence.py`: `--no-parallel` vs
`--parallel --max-workers 4` produce byte-identical `tables/*.json`, framework
JSON sidecars, and `stats/pairwise_tests.json`. PNG byte-equality is not gated
(varies across processes/platforms); JSON sidecars are the deterministic truth.

## Performance benchmark

`scripts/benchmark_human_export_mot06.py` runs the real CLI per worker count and
records `speedup_vs_workers_1` + per-stage timings from
`provenance/performance_trace.json`. The ≥3×/≥5× targets must be measured on the
reference machine against a populated database (not run here — needs result
storage). `PERFORMANCE_REPORT.json` is written to this audit directory by the
script.

## Regression

Per project memory, full `pytest -q` carries ~600 unrelated pre-existing
failures; MOT06 is validated against focused suites:

- `tests/human_evidence/mot06` — **64 passed**.
- `tests/human_evidence` + `tests/results_interaction` — **696 passed**.
- `tests/human_evidence/site` + `tests/human_evidence/performance` — green
  (MOT05 + MOT04 unaffected by the merge hook).

## Remaining / deferred

- `max_workers` default left at 4 (see note).
- `--tier all` resolves profiles from the **bundled** recipe-book YAML; a
  DB-mutated book's profiles are not consulted (acceptable — profiles are
  effectively static; consistent with the spec's "based on recipe book
  definitions").
- Performance numbers require a populated DB run on the reference machine.
- Provider-level registry parallelism, incremental dashboard patching, and the
  other §16 items remain out of scope.
