# Human Evidence Multi-Dimensional Comparison Framework

The Sheet 19 framework extends the Human Evidence Export with a multi-dimensional
comparison across **accuracy, runtime, stability, rate robustness, and mechanism
robustness**, plus radar / Pareto / significance figures and six companion tables.

> A multi-dimensional comparison is valid only if each dimension preserves its
> provenance, its normalisation population, its coverage, and its interpretation
> limit.

## Architecture (post-hoc, read-only)

```
result_summary.json  (exported by the machine providers)
        │
        ▼
FrameworkObservationService   — runtime total (canonical only), parameter evidence
        │
        ▼
FrameworkMetricService        — accuracy / runtime / stability / robustness
        │                       + FrameworkNormalizationPolicy (relative, versioned)
        ▼
FrameworkMetricsBundle
        │
        ├─ FrameworkTableService   → tables/framework/*.md|csv|json
        ├─ FrameworkChartRenderer  → figures/framework/*.png + json + md
        ├─ Markdown renderer       → README multi-dimensional sections
        ├─ Offline dashboard       → embedded framework section (data URIs)
        └─ Manifest / fingerprint  → framework folded into the content fingerprint
```

The service never opens `data/metadata.db`, never recomputes RMSE/MAE from arrays,
and never mutates a `Result` row.

## What each dimension means — and what it does not

* **Accuracy** — relative RMSE position within each station-scoped cohort,
  macro-averaged over the balanced cohort intersection. It does *not* declare a
  universal best method; the strongest admissible phrasing is "algorithm A has the
  highest relative accuracy profile over N balanced cohorts."
* **Speed** — relative position of the canonical median runtime (admissible source
  quality only), log-normalised. It separates mean and median and exposes the
  source-quality distribution and coverage. An invalid or summed runtime is
  rejected.
* **Stability** — RMSE coefficient of variation (sample std, ddof=1) over the real
  realisations; `n < 3` is `unavailable`, a near-zero mean is `blocked_denominator`.
* **Rate robustness** — descriptive relative degradation between two missingness
  rates over strictly matched cells (same dataset view, station, algorithm,
  mechanism, phase, metric, recipe lineage). Negative degradation is retained and
  flagged.
* **Mechanism robustness** — `1 − relative_range` of RMSE across MCAR/MAR/MNAR;
  fewer than three mechanisms is `incomplete_mechanism_coverage`.
* **Parameter efficiency** — published only when explicit parameter evidence is
  exported. There is no ARMA `p+q+1` fallback and a learning-free algorithm is
  `not_applicable`, never a favourable zero.
* **Memory efficiency** — not instrumented, therefore `unavailable`; it is never
  set to `1.0`.

## Figures

* **Radar** — only real axes; blocked below three available axes; scores in `[0,1]`.
* **Pareto** — macro balanced RMSE (X) vs macro median runtime seconds (Y), with the
  lower/lower non-dominated front; blocked below two comparable algorithms.
* **Significance heatmaps** — one per cohort, colouring the existing BH-adjusted p
  and annotating the matched-pairs rank-biserial effect and `n`.

Every figure writes a PNG, a canonical JSON sidecar (which the fingerprint hashes),
and an Mddocx-compatible Markdown companion. Matplotlib only, `Agg` backend,
`DejaVu Sans`, one chart per figure.

## CLI

```
imputebench results export-human \
  --framework auto \
  --framework-min-coverage 0.80 \
  --framework-runtime-quality allow-summary \
  --framework-significance-charts
```

* `auto` computes what is available; defaults become diagnostics, never blocks.
* `required` fails (HFM-017) when the accuracy dimension or three usable dimensions
  are blocked.
* `off` preserves the Sheet 18 pack unchanged.

`--dry-run` prints the policy, available/blocked dimensions, balanced cohort count,
algorithm coverage, and the estimated chart count.

## Interpretation boundary

The framework can never promote the pack grade; it may only degrade it. All
narrative stays bounded to the selected dataset snapshot, benchmark contracts,
artificial missingness protocols, evaluation supports, metric definitions, and
execution environment. A non-significant Wilcoxon test never proves equivalence,
and missing memory values never prove equal memory efficiency.
