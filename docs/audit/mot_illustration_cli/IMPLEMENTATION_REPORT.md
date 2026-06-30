# IMPLEMENTATION REPORT — MOT `illustration` CLI

Status: **implemented, first lot generated, tests green.**

## Files created

Rendering core (Click-free, testable headless):

```
imputebench/illustration/__init__.py
imputebench/illustration/contracts.py        FigureRequest/FigureArtifact, error+exit codes
imputebench/illustration/style.py            Agg backend, font fallback, palettes
imputebench/illustration/output.py           FigureWriter, sidecar, safety, overwrite policy
imputebench/illustration/generic_renderers.py  4 Chapter-2 figures + registry
imputebench/illustration/plot_data.py        scope guards + ranking aggregation
imputebench/illustration/plot_renderers.py   ranking renderer
```

CLI wiring:

```
imputebench/cli/illustration/__init__.py
imputebench/cli/illustration/group.py        domain group, shared options, list, error mapping
imputebench/cli/illustration/generic.py      4 generic commands + aliases
imputebench/cli/illustration/plot.py         ranking + heatmap/timeseries/distribution guards
```

Tests:

```
tests/illustration/conftest.py
tests/illustration/test_cli_registration.py
tests/illustration/test_generic_mcar_mar_mnar.py     (incl. determinism)
tests/illustration/test_generic_taxonomy.py
tests/illustration/test_generic_sensor_graph.py
tests/illustration/test_generic_research_gaps.py
tests/illustration/test_output_contract.py
tests/illustration/test_style_policy.py
tests/illustration/test_plot_scope_validation.py
tests/illustration/test_plot_ranking_from_human_pack.py
tests/illustration/test_no_cuda_relaunch_classification.py
```

Docs:

```
docs/illustration_cli.md
docs/audit/mot_illustration_cli/BASELINE.md
docs/audit/mot_illustration_cli/IMPLEMENTATION_REPORT.md
```

## Files modified

```
imputebench/cli/root.py
  - added "illustration" to _KNOWN_ROOT_COMMANDS
  - registered build_illustration_group() in _register_canonical_layout()
imputebench/cli/runtime_command_classifier.py
  - classify path[0] == "illustration" as GPU_NOT_APPLICABLE
```

`pyproject.toml` was **not** modified: no new mandatory dependency.

## Tests run

```
pytest tests/illustration/ -q            -> 49 passed
pytest tests/runtime_environment/ tests/cli/test_recipe_book_cli.py -q  -> 41 passed
```

Pre-existing, unrelated failures in `tests/cli/test_{run,plugin,result}_*` (legacy
deprecation banner polluting JSON) were confirmed to fail identically with the
MOT changes stashed; they are not caused by this work.

## Figure inventory (first lot — Chapter 2)

Generated into `…/v0.3/_/generated/figures/ch02/` at 300 DPI:

| Figure | figure_id | px (300 dpi) | source_type |
|---|---|---|---|
| 2.1 | `fig_2_1_mcar_mar_mnar` | 1772×827 | conceptual_schematic |
| 2.2 | `fig_2_2_imputation_taxonomy` | 1890×1181 | conceptual_schematic |
| 2.3 | `fig_2_3_sensor_graph_flow` | 1890×886 | conceptual_schematic |
| 2.4 | `fig_2_4_research_gaps` | 1890×1063 | conceptual_schematic |

Each with `.png` + `.json` + `.caption.md`.

## Sidecar schema example

```json
{
  "schema": "imputebench.illustration/v1",
  "figure_id": "fig_2_1_mcar_mar_mnar",
  "figure_number": "Figure 2.1",
  "chapter": 2,
  "section_hint": "2.1",
  "title": "Missing data mechanisms: MCAR, MAR, and MNAR",
  "source_type": "conceptual_schematic",
  "not_experimental_evidence": true,
  "inputs": {"seed": 42, "experiment_id": "", "metric": "", "data_source": "none"},
  "rendering": {"format": "png", "dpi": 300, "width_cm": 15.0, "height_cm": 7.0,
                "font_family_effective": "Times New Roman", "color_policy": "colorblind_safe"},
  "outputs": {"png": "fig_2_1_mcar_mar_mnar.png", "caption_md": "fig_2_1_mcar_mar_mnar.caption.md"},
  "claim_boundary": "Conceptual illustration only; not an experimental result.",
  "created_at_utc": "…"
}
```

Evidence-aware ranking sidecars carry `source_type: human_evidence_pack`,
`not_experimental_evidence: false`, and `inputs` with `experiment_id`, `metric`,
`phase`, `direction`, `selected_result_count`, and `selection_query`. Validated
against the real `exp2` pack: GALPI mean rank 1.156 over 45 cohorts / 900
underlying results.

## Visual approval checklist

- [x] Figure 2.1 — three panels share one value field; MCAR uniform, MAR right-band,
      MNAR high-value holes; hatched missing cells; per-panel dependency subtitles.
- [x] Figure 2.2 — clean three-branch tree; "Polynomial / gap-adaptive interpolation"
      leaf (no GALPI design detail); no DACPI/ARMA/SAITS.
- [x] Figure 2.3 — miniature 6×4 grid (not the 14×10 experimental grid); spatial
      edges + temporal-flow arrows; dataset-agnostic.
- [x] Figure 2.4 — prior-work chips, two amber gap boxes, thesis-positioning node;
      careful phrasing ("can be difficult to outperform"), no "Linear dominates".
- [x] Captions follow `Figure 2.x:` style and state "conceptual / schematic".

## Known limitations

- `plot ranking` v1 reads from a **human evidence pack** only. `--source sql`
  returns exit 3 with guidance (descriptor-first SQL ranking is deferred).
- `plot heatmap` blocks (no station/node metrics exposed in v1).
- `plot timeseries` is intentionally delegated to the storyboard services.
- `plot distribution` blocks (cohort-compatible sampling not yet implemented).
- PNG byte-identity is not guaranteed across runs; layout, dimensions, sidecar
  (minus `created_at_utc`) and caption are deterministic per seed.
- Promotion into `rendering/7-figures/` is a manual, confirmed step after visual
  approval; nothing is auto-promoted.
