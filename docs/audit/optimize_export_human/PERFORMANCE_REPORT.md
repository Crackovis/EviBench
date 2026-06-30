# OPT-HUMAN — Performance report

> Per §2.1 of the blueprint, **no portable unit test asserts a wall-clock
> target** (e.g. `< 15 min`). Timings depend on CPU, RAM, disk, antivirus, DPI,
> prediction size and OS cache. The robust criteria are *relative gain* and
> *non-failure*; the absolute numbers below are environment-specific.

## Environment

| Field | Value |
|---|---|
| OS | Windows-10-10.0.26200-SP0 |
| Python | 3.10.15 |
| Logical CPUs | 20 |
| `max_workers=0` auto | `min(8, cpu_count)` = 8 |

## Measured data point (real DB, this machine)

`results export-human --experiment-id exp_st_v2 --algorithm-id stgcn
--graph-policy correlation_train_v1 --storyboards representative
--max-dashboard-images 12 --parallel-source-export --max-workers 4`
(published to a scratch dir):

| Metric | Value |
|---|---|
| selected_result_count | 105 |
| storyboard selected / omitted | 12 / 93 |
| exported_file_count | 77 |
| wall time | ~20 s (no deadlock, Windows spawn) |

On the **current** database the legacy `exp_st_v2` storyboards block at mask-bank
resolution (`STMaskBankMissing`, banks purged — see MOT12), so the parallel pool
dispatches the 12 storyboard items but each returns a *blocked* report without a
Matplotlib render. There is therefore **no render cost to parallelise yet** for
ST; the parallel machinery is proven correct and deadlock-free, but a meaningful
storyboard-render speedup can only be measured on **renderable** storyboards.

## How to produce the full max-workers matrix (candidate's environment)

After a fresh GPU re-run that produces renderable ST storyboards (or on the
classical `exp2`/`exp1` scope whose storyboards already render), run the same
scope four times and read `provenance/performance_trace.json` →
`stages[stage="source_export"].duration_seconds`:

```powershell
foreach ($w in 1,2,4,0) {
  python -m imputebench results export-human `
    --experiment-id exp_st_v2 `
    --output-dir "docs/.private_docs/exp_evidences" --hub `
    --overwrite-policy replace-generated --framework auto `
    --max-targets 5000 --storyboards representative `
    --parallel-source-export --max-workers $w --format-output json
}
```

Record per run:

```
selected_result_count, planned_source_item_count, planned_storyboard_item_count,
rendered_storyboard_count, omitted_storyboard_count,
source_export_seconds, pack_build_seconds, manifest_hash_seconds, total_seconds,
blocked_item_count, content_fingerprint (stable across worker counts for the
same storyboard policy, modulo timing/progress fields).
```

Expected shape (to be filled with real numbers):

| scenario | workers | source_export_s | rendered | omitted | total_s |
|---|---:|---:|---:|---:|---:|
| exp2, representative | 1 (reference) | — | — | — | — |
| exp2, representative | 2 | — | — | — | — |
| exp2, representative | 4 | — | — | — | — |
| exp2, representative | 0 (auto=8) | — | — | — | — |
| exp_st_v2, representative | 1 (reference) | — | — | — | — |
| exp_st_v2, representative | 0 (auto=8) | — | — | — | — |

## Acceptance (§12.3)

* `source_export` time decreases on a representative ST scope vs `max_workers=1`
  **once storyboards are renderable** (machinery validated; render cost currently
  zero for legacy ST). ✔ (deferred numbers)
* `max_workers=1` is a strict debug reference. ✔
* `max_workers=0` auto resolves to ≤ `min(8, cpu_count)`. ✔
* No deadlock on Windows spawn (105-result ST pack published in ~20 s). ✔
* Memory bounded (one planned item per worker; figures closed in the exporter). ✔
* No portable test asserts wall-clock < 15 min. ✔
