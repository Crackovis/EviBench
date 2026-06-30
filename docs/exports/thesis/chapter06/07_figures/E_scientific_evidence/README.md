# Section E — Scientific Evidence Summary

**Source data:** `chapter06/09_scientific_gate/SCIENTIFIC_GATE_REPORT.json`
**Policy:** SCI-FIX09 scientific gate (calibration mode)

This section provides a **single-figure summary** of the scientific readiness
assessment for all four algorithms.

---

## fig_19 — Scientific Gate Checks Heatmap

**File:** `fig_19_gate_checks_heatmap.{png,svg}`

A **heatmap** with algorithms on the Y-axis and individual gate checks on the X-axis.
Each cell is annotated with:
- ✓ (green) — check **passed**
- ⚠ (yellow) — check **warned** (non-blocking caveat)
- ✗ (red) — check **failed / blocked**

**Gate checks shown** (left to right):

| Check | Meaning |
|-------|---------|
| Plugin Contract | The algorithm plugin exposes the required interface |
| Claim Policy | Claim level is consistent with evidence (diagnostic / comparison) |
| Algo Cards | Algorithm card files are present and complete |
| Corrected Variant | The scientifically corrected variant is used (e.g. `stconv_v1` for STGCN) |
| Sci. Training | Training was conducted at the required scientific tier (100 epochs, Phase C) |
| Convergence | Training loss converged without NaN/Inf; validation loss recorded |
| Metrics | MAE and RMSE are present and non-negative for all test realizations |
| Mask Support | Evaluation was performed on the declared hidden-support fingerprint |
| Graph Evidence | Graph asset files (adjacency, node mapping) are present and fingerprinted |
| Artifacts | Prediction artifacts (`.npz`) are present and traceable |

**What to look for:**
- All green → the algorithm is scientifically ready for thesis evidence.
- Any red → a blocking issue exists; check `SCIENTIFIC_GATE_REPORT.md` for details.
- Yellow → a caveat is recorded but does not block the evidence claim.

The global gate status (`global_status`, `scientific_ready`) is reported in
`09_scientific_gate/SCIENTIFIC_GATE_REPORT.{json,md}`.

---

*Generated: 2026-06-30 01:31 UTC*  
*Regenerate: `python -m imputebench st figures generate --section gates --clean`*
