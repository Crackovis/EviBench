# MOT06 — Baseline (Export Parallelization, Classical Summaries, Tier Merge)

Captured before any MOT06 patch.

## HEAD

- Repo: `Crackovis/EviBench-VIP`
- Branch: `main`
- HEAD SHA at baseline: `b298b91daa3c8842fae0548362a9243466951d88` ("New Specs")
- Working tree: clean at baseline.

## Pre-existing landed work (important — the spec assumes a pre-MOT04 baseline)

MOT04 (parallel human export) and MOT05 (website-aware export) **already landed**
on this HEAD (commit `153dd6ba`). Consequences for MOT06:

- **Bug A (parallelization) is largely already implemented.** Present today:
  - `services/results_interaction/human_export/parallel_policy.py`
    (`HumanExportParallelPolicy`, `policy_from_request`, auto worker resolution,
    strict sequential mode, hard cap 64).
  - Deterministic threaded hashing (`manifest_service`) and an atomic
    `file_write_scheduler.py` (`WriteTask` / `execute_write_tasks`).
  - Framework chart rendering via a module-level **process** worker
    (`presentation/human_evidence/framework_chart_worker.py`).
  - Guarded storyboard source-export fan-out
    (`providers/storyboard_worker.py`); workers receive scalar ids/paths and
    rebuild `EvidenceContext` locally — provider instances are **not** pickled.
  - Performance trace at `provenance/performance_trace.json`
    (`performance_trace.py`), excluded from the content fingerprint.
- The request already carries the parallel fields
  (`parallel_enabled`, `max_workers` — default **4**, not the spec's `0`;
  `parallel_source_export=False`, `parallel_storyboards`, `parallel_hashing`,
  `parallel_file_io`, `parallel_framework_charts`, `parallel_build_pack`).
- The request already carries the website fields (`experiment_id`, `hub`,
  `hub_title`, `hub_subtitle`, `hub_description`) and the site error codes
  `HRE-SITE-001..008`. Atomic publish targets `site_root/experiment_id`.

So the **net-new** MOT06 surface is:

1. **Bug C** — classical `result_summary.json` completeness (execution + training
   blocks; explicit `training: not_applicable` for classical; specific blocked
   reasons).
2. **Bug B** — tier overlay → true A+B **merge**: `site_experiment_state.json`,
   multi-profile selection, `--tier all` expansion, merge-before-selection,
   dedup, conflict gating (`HRE-SITE-MERGE-001..007`).
3. **Parallel refinements** — `HRE-PERF-001..004` error codes; reconcile defaults
   without regressing MOT04 tests.

## Bug C reproduction attempt — NOT reproduced structurally

The spec hypothesises that ~33 % of completed classical results are excluded as
`source_summary_missing` because the source pipeline never produces
`result_summary.json`. Verified against the **real** planner/engine/providers
(via `tests/human_evidence/_fakes.py::standard_cohort` + `build_source_service`):

```text
classical exec class: classical   status: COMPLETED
num result_summary.json: 10 of 10 results
read results: 10
missing_summary_ids: ()
schema: imputebench.result-summary/v2
top keys: artifacts, benchmark, caveats, claim_limits, identity, metrics,
          provenance, runtime, schema, scientific_status, target
has training block: False
has execution block: False
```

Findings:

- The v2 `result_summary.json` writer (`providers/core_result_exporters.py::
  _result_summary_for_result`) is **unconditional** for any *result* target that
  resolves in the `EvidenceContext`; classical results already produce it and
  `missing_summary_ids` is empty. The brittle path the draft names
  (`services/source_export/providers/result_summary.py`) does **not** exist; the
  real provider is `core_result` in the `results_interaction` registry.
- A summary is only "missing" when the result is **unresolved** in the context
  (provider blocked), or on a schema/I-O error — never merely for being
  classical or completed-with-metrics.
- The v2 payload has **no** `execution` or `training` block today, so a classical
  summary does not yet *explicitly* declare training as not-applicable. That is
  the concrete MOT06-01 gap to close (without fabricating DL diagnostics).

## CLI baseline (`results export-human --help`)

Already present: `--tier` (maps to single `recipe_profile_id`),
`--recipe-profile` (alias of `--tier`), `--experiment-id`, `--hub`, and the full
parallel flag set (`--parallel/--no-parallel`, `--max-workers` [default 4],
`--parallel-source-export`, `--parallel-storyboards`, `--parallel-file-io`,
`--parallel-hashing`, `--parallel-framework-charts`).

Absent (MOT06 to add): `--experiment-merge-policy`, `--tier all` expansion,
repeatable/multi-profile selection.

## Tests baseline

- `tests/human_evidence/mot06/` does not exist yet.
- Focused suites used as gates (per project memory; full `pytest -q` has many
  unrelated pre-existing failures): `tests/human_evidence`,
  `tests/human_evidence/performance`, `tests/human_evidence/site`,
  `tests/results_interaction`.
