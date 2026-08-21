# Section C — Realization Stability

**Source data:** `ResultService()` filtered to epoch_budget ≥ 50, graph_policy = `correlation_train_v1`
**Filter:** Phase C official runs, 5 benchmark realizations (test_r000 – test_r004)

These figures answer: **"Is the ranking of algorithms stable across random mask realizations,
or does it depend on which evaluation mask was drawn?"**
A scientifically robust benchmark should produce consistent rankings regardless of
the specific realization.

---

## fig_15 — Realization Ranking Heatmap

**File:** `fig_15_realization_ranking_heatmap.{png,svg}`

A **5 × 4 heatmap** (realizations × algorithms).
Each cell shows the algorithm's **MAE rank** within that realization (1 = lowest MAE = best)
and the underlying **mean MAE value** as a number.
Colour scale: green (rank 1) → yellow-green (rank 2) → orange (rank 3) → red (rank 4).

**What to look for:**
- A column that is consistently green → the algorithm wins in most realizations.
- Colour variation within a column → rank instability; the algorithm's relative
  performance changes depending on the realization.
- A row where colours are mixed → one realization is particularly discriminating.

---

## fig_16 — MAE Coefficient of Variation

**File:** `fig_16_mae_cv_barplot.{png,svg}`

A **bar chart** where each bar is one algorithm's
**coefficient of variation** of MAE: CV = σ(MAE) / μ(MAE).
CV is a dimensionless stability metric: **lower = more consistent** across realizations
and mask scenarios.

**What to look for:**
- Algorithms with CV < 0.2 are considered stable.
- A high CV for an algorithm that ranks well on mean MAE is a warning sign —
  good average performance may mask realization-dependent variance.

---

*Generated: 2026-08-17 22:32 UTC*  
*Regenerate: `python -m imputebench st figures generate --section stability --clean`*
