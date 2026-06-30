# `imputebench illustration` — thesis-ready figures

The `illustration` domain produces reproducible, thesis-ready figures. Every
figure is an **evidence artifact**, not just an image: each render emits a PNG
plus a machine-readable JSON sidecar and a Mddocx-ready caption.

```
imputebench illustration
├── generic        conceptual schematics (no experimental data)
│   ├── mcar-mar-mnar      Figure 2.1
│   ├── taxonomy           Figure 2.2
│   ├── sensor-graph       Figure 2.3
│   ├── research-gaps      Figure 2.4
│   ├── galpi-flowchart    Figure 3.1   (GALPI design schematics)
│   ├── window-adaptation  Figure 3.2
│   ├── degree-selection   Figure 3.3
│   ├── short-vs-long-gap  Figure 3.4
│   └── scaled-time        Figure 3.5
├── plot           evidence-aware figures (from results / human packs)
│   ├── ranking
│   ├── heatmap            (blocked without station/node metrics)
│   ├── timeseries         (delegated to storyboard services)
│   └── distribution       (blocked without cohort-compatible samples)
└── list           enumerate available figures
```

## Output triplet

For every figure `<figure_id>`:

| File | Purpose |
|------|---------|
| `<figure_id>.png` | the rendered image |
| `<figure_id>.json` | sidecar — schema `imputebench.illustration/v1` |
| `<figure_id>.caption.md` | Mddocx-ready caption |

The sidecar records identity (`figure_number`, `chapter`, `section_hint`),
`source_type` (`conceptual_schematic` vs `human_evidence_pack`),
`not_experimental_evidence`, rendering config (dpi, size, effective font, colour
policy), and a `claim_boundary`. **No absolute paths** appear in the sidecar —
only artifact basenames — so it is safe for public rendering.

## Conceptual vs evidence-aware

- `generic` figures are **conceptual schematics**. They may use stylised
  synthetic matrices but are explicitly marked `not_experimental_evidence: true`
  and must never be presented as London AQ results.
- `plot` figures are **experimental evidence**, derived from persisted results or
  a human evidence pack. They are marked `not_experimental_evidence: false` and
  record their selection scope in the sidecar.

## Output directories & safety

Default resolution order for `--output-dir`:

1. `--output-dir` explicit
2. `EVIBENCH_THESIS_FIGURES_DIR`
3. `EVIBENCH_EVALUATIONS_ROOT` + `…/v0.3/_/generated/figures`
4. `./exports/illustrations/`

Writing into a `rendering/7-figures` path requires `--confirm-rendering-output`;
otherwise the command exits with code **4** and writes nothing. The recommended
flow is: generate into safe staging (`v0.3/_/generated/figures/ch02`), review,
then promote explicitly.

## Common options

```
--output-dir PATH              destination directory
--format png                   image format (png only in v1)
--dpi 300                      render DPI (>=150; 300 for thesis)
--width-cm / --height-cm       size (defaults are per-figure)
--seed 42                      deterministic layout seed
--style thesis
--overwrite-policy fail|replace-generated|reuse-identical
--confirm-rendering-output     required for rendering/7-figures targets
--dry-run                      validate & report, write nothing
--format-output text|json      summary format
--caption/--no-caption         (generic) emit caption file
--sidecar/--no-sidecar         (generic) emit JSON sidecar
```

Plot commands additionally accept `--experiment-id`, `--metric`, `--phase`,
`--algorithm-id` (repeatable), `--graph-policy` (repeatable), `--tier`,
`--recipe-book`, `--source auto|sql|human-pack`, `--evidence-root`,
`--max-results`.

## Exit codes

| Code | Meaning |
|---|---|
| 0 | success |
| 1 | unexpected runtime error |
| 2 | input validation error |
| 3 | evidence unavailable / ambiguous / delegated |
| 4 | unsafe output target without `--confirm-rendering-output` |

Plot guards surface stable codes: `ILLUSTRATION-PLOT-EMPTY-SCOPE`,
`ILLUSTRATION-PLOT-HEATMAP-NO-STATION-METRICS`,
`ILLUSTRATION-PLOT-TIMESERIES-DELEGATED`,
`ILLUSTRATION-PLOT-DISTRIBUTION-INCOMPATIBLE`.

## First lot — Chapter 2 (generate into safe staging)

```bash
imputebench illustration generic mcar-mar-mnar \
  --output-dir "<v0.3>/_/generated/figures/ch02"
imputebench illustration generic taxonomy \
  --output-dir "<v0.3>/_/generated/figures/ch02"
imputebench illustration generic sensor-graph \
  --output-dir "<v0.3>/_/generated/figures/ch02"
imputebench illustration generic research-gaps \
  --output-dir "<v0.3>/_/generated/figures/ch02"
```

Promote to rendering only after visual approval:

```bash
imputebench illustration generic mcar-mar-mnar \
  --output-dir "<v0.3>/rendering/7-figures/ch02" \
  --confirm-rendering-output --overwrite-policy replace-generated
```

## Evidence-aware ranking

`plot ranking` aggregates the **within-cohort mean rank** per algorithm from a
human evidence pack's `comparison_global.json` (it never averages raw metric
values across incompatible cohorts):

```bash
imputebench illustration plot ranking \
  --experiment-id exp2 --metric rmse --phase test \
  --evidence-root docs/.private_docs/exp_evidences/exp2 \
  --output-dir ./exports/illustrations
```

## Notes

- Renderers are pure matplotlib + numpy (already core dependencies). No Graphviz
  or NetworkX is required.
- Illustration commands are classified `gpu_not_applicable`: they never trigger a
  Conda/CUDA relaunch.
- The CLI never mutates global environment variables (no `KMP_DUPLICATE_LIB_OK`).
  If a local OpenMP duplicate-library clash occurs, set it in your own shell.
- If Times New Roman is unavailable, the renderer falls back (Times → DejaVu
  Serif) and records the effective font in the sidecar.
