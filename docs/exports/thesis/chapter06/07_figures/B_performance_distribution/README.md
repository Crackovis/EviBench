# Section B — Performance Distribution

**Source data:** `ResultService()` filtered to epoch_budget ≥ 50, graph_policy = `correlation_train_v1`
**Filter:** Phase C official runs, global metric scope
**Algorithms:** STGCN, IGNNK, GRIN, DCRNN
**Realizations:** test_r000 – test_r004 (5 realizations × multiple mask scenarios)

These figures characterise **benchmark accuracy** — how well each model imputes
held-out values on the LondonAQ dataset under the correlation-trained graph.
They answer: "which algorithm is most accurate, and how spread is its performance?"

---

## fig_12 — MAE Distribution (violin + box)

**File:** `fig_12_mae_violin.{png,svg}`

**Violin plot** (kernel density estimate of the full distribution) with a narrow
**box-and-whisker plot** overlaid at the centre of each violin.
Metric: **MAE** (Mean Absolute Error, µg/m³).

**What to look for:**
- Width of the violin at each MAE level → density of results near that value.
- Median line and IQR box → central tendency and typical spread.
- Skew → whether the algorithm occasionally produces very poor predictions.

---

## fig_13 — RMSE Distribution (violin + box)

**File:** `fig_13_rmse_violin.{png,svg}`

Identical structure to fig_12 for **RMSE** (Root Mean Squared Error, µg/m³).
RMSE penalises large errors more than MAE; a larger MAE–RMSE gap indicates
the presence of occasional large prediction errors.

---

## fig_14 — MAE vs RMSE Scatter with 95 % Confidence Ellipses

**File:** `fig_14_mae_rmse_scatter.{png,svg}`

Each point is one result (one realization of one algorithm).
Points are **algorithm-coloured**; the **large marker** is the algorithm mean centroid.
Each algorithm gets a **2σ (≈ 95 %) confidence ellipse** derived from the
covariance of its (MAE, RMSE) pairs.
The **dashed diagonal** marks MAE = RMSE (theoretical minimum; all real results lie above it).

**What to look for:**
- Ellipse size → variance in both metrics simultaneously.
- Ellipse tilt → correlation between MAE and RMSE errors (near-diagonal tilt is typical).
- Separation between algorithm centroids → practical significance of ranking differences.

---

*Generated: 2026-08-17 22:32 UTC*  
*Regenerate: `python -m imputebench st figures generate --section performance --clean`*
