# Section D — Runtime & Efficiency

**Source data (time):** `chapter06/03_st_algorithm_lifecycle/{algo}/{result_id}/runtime_summary.json`
**Source data (accuracy):** `ResultService()` filtered to epoch_budget ≥ 50, graph_policy = `correlation_train_v1`
**Field used:** `elapsed_seconds` (wall-clock inference time for one realization)

These figures characterise **computational cost** and the **accuracy / efficiency trade-off**.
They are relevant for practical deployment discussions in Chapter 6.

---

## fig_17 — Inference Time Distribution (boxplot)

**File:** `fig_17_elapsed_seconds_boxplot.{png,svg}`

One boxplot per algorithm showing the distribution of **wall-clock inference time
in seconds** across all official Phase C runs.

**What to look for:**
- Absolute scale differences — DCRNN is expected to be significantly slower than
  STGCN/IGNNK due to its recurrent architecture.
- Variance — a wide box means inference time is sensitive to mask scenario or
  realization; a tight box means consistent cost.

> `elapsed_seconds` records end-to-end prediction time including data loading and
> post-processing, not raw forward-pass time.

---

## fig_18 — Accuracy / Efficiency Pareto Frontier

**File:** `fig_18_accuracy_efficiency_pareto.{png,svg}`

Each algorithm is represented as a **single point** at (mean inference time, mean RMSE),
with **error bars** showing ±1 std in both dimensions.
A **dashed Pareto frontier** connects the non-dominated algorithms (those where no other
algorithm achieves both lower RMSE *and* shorter inference time).

**What to look for:**
- Algorithms on the Pareto frontier are the efficient choices.
- An algorithm far above and to the right of the frontier is dominated:
  another model achieves better accuracy *and* faster inference.
- Error bar overlap between adjacent algorithms on the RMSE axis indicates
  the RMSE difference may not be practically significant.

---

*Generated: 2026-06-30 01:31 UTC*  
*Regenerate: `python -m imputebench st figures generate --section runtime --clean`*
