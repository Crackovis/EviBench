# Section A — Training Dynamics

**Source data:** `chapter06/03_st_algorithm_lifecycle/{algo}/{result_id}/training_history.json`
**Filter:** Phase C official runs (epoch_budget ≥ 50), all realizations.
**Algorithms:** STGCN, IGNNK, GRIN, DCRNN
**Epochs per run:** 100

These figures document how the four spatiotemporal models learn during training.
They support the convergence claim made in Chapter 6 and complement the
structured `convergence_report.json` in `06_st_gates/`.

---

## fig_08 — Training Loss Convergence Overlay

**File:** `fig_08_training_loss_overlay.{png,svg}`

All four algorithms on a single set of axes.
Each curve is the **mean training loss** across all realizations of that algorithm,
with a ±1σ shaded band.
Loss values are **normalised to the first epoch** (loss / loss₁) so that algorithms
with heterogeneous loss scales (IGNNK uses absolute µg/m³; others use normalised MSE)
can be compared on a shared [0, 1]-ish axis.

**What to look for:** convergence speed (how many epochs to reach a stable minimum),
tightness of the ±1σ band (run consistency), and whether any algorithm plateaus early.

---

## fig_09 — Training Curves Spaghetti Grid

**File:** `fig_09_training_curve_spaghetti.{png,svg}`

2 × 2 grid of subplots, one per algorithm.
Each subplot shows **every individual run** as a thin semi-transparent line (α = 0.18),
with the **bold mean curve** overlaid.

**What to look for:** within-algorithm run variability. If the spaghetti is tight,
the algorithm is stable across mask realizations. A divergent run will appear as
an outlier strand far from the bundle.

---

## fig_10 — Training Curves Mean ± Band Grid

**File:** `fig_10_training_curve_mean_band.{png,svg}`

Same 2 × 2 layout as fig_09, but shows only the **mean ± 1σ shaded band** (no individual strands).
Each subplot also annotates the **best mean epoch** (minimum of the mean curve) with its loss value.

**When to use:** cleaner than fig_09 for publication; use fig_09 for the appendix
or for spotting individual outlier runs.

---

## fig_11 — Final Training Loss Boxplot

**File:** `fig_11_final_loss_boxplot.{png,svg}`

One boxplot per algorithm showing the **distribution of final-epoch (epoch 100) training loss**
across all official runs.
Y-axis is **log-scale** because IGNNK's absolute loss (µg/m³²) is orders of magnitude
larger than the normalised losses of STGCN, GRIN, and DCRNN.

**What to look for:** median final loss and spread. A narrow box indicates consistent
training outcomes; a wide box or many fliers suggests sensitivity to initialisation
or mask realization.

> **Note:** IGNNK reports loss in absolute µg/m³ units; the other three use
> normalised MSE. Direct comparison of absolute values across algorithms is not
> meaningful — use fig_08 (normalised) for cross-algorithm convergence comparison.

---

*Generated: 2026-08-17 22:32 UTC*  
*Regenerate: `python -m imputebench st figures generate --section training --clean`*
