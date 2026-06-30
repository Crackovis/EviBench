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

## Chapter 3 extension (GALPI design schematics)

Additive extension of the `GENERIC_FIGURES` registry — no new subsystem. Five
conceptual figures added to `generic_renderers.py` plus builders, captions
`CAPTION_3_1..3_5`, and registry entries. Module/CLI docstrings generalised from
"Chapter 2" to multi-chapter.

| Figure | command | figure_id | source_type |
|---|---|---|---|
| 3.1 | `galpi-flowchart` | `fig_3_1_galpi_flowchart` | conceptual_schematic |
| 3.2 | `window-adaptation` | `fig_3_2_window_adaptation` | conceptual_schematic |
| 3.3 | `degree-selection` | `fig_3_3_degree_selection` | conceptual_schematic |
| 3.4 | `short-vs-long-gap` | `fig_3_4_short_vs_long_gap` | conceptual_schematic |
| 3.5 | `scaled-time` | `fig_3_5_scaled_time_conditioning` | conceptual_schematic |

`generic --help` now lists 9 conceptual figures (4 Ch2 + 5 Ch3). Tests:
`tests/illustration/test_generic_renderers_ch03.py` and
`tests/cli/test_illustration_generic_ch03_cli.py`. Degree-selection plots the
*effective* degree `min(rule, ⌊w/3⌋)` and the support cap as a (non-binding)
envelope, so the figure cannot contradict the prose. Figure 3.4 uses a
deterministic synthetic signal with the long gap kept inside a single-curvature
segment so the cubic tracks the trend faithfully.

## Data-driven Chapter 3 plots (Figures 3.6–3.8)

Evidence-aware figures from persisted `exp2` results, selected via
`ResultSelectionQueryService.query_descriptors` + `ResultService.get` (no raw
SQL, no fabricated values). The masking condition is resolved from the joined
`mask_family`/`mask_rate`, falling back to the canonical `recipe_entry_id`
(`<book>:<family>:<rate%>`) — benchmark results carry a `protocol:` masking ref
that does not join the `maskings` table, so the join alone returns NULL.

| Figure | command | source_type | data |
|---|---|---|---|
| 3.6 | `plot heatmap` | sql_selection | 5 stations × 6 conditions, 150 results |
| 3.7 | `plot ranking` | human_evidence_pack | GALPI mean rank 1.156 |
| 3.8 | `plot per-station` | sql_selection | GALPI vs Linear, 300 results |

Added: `HeatmapCell/HeatmapData`, `PerStationEntry/PerStationData`,
`load_heatmap_data`, `load_per_station_data`, `resolve_condition`,
`resolve_station` (`plot_data.py`); `render_heatmap`, `render_per_station`
(`plot_renderers.py`); `heatmap`/`per-station` commands + shared options
(`plot.py`); seven stable failure codes (`contracts.py`). `plot ranking` now
takes `--figure-number/--chapter/--section-hint/--figure-id` for thesis identity.
Figure 3.1 loop-back arrow re-routed to the right of the chain (never crossing
blocks). Tests: `test_plot_heatmap_data.py`, `test_plot_per_station_data.py`,
`test_plot_ch03_renderers.py`, `test_plot_cli_ch03.py` (hermetic fakes — no real
`metadata.db`). Full illustration suite: 92 passed.

Completeness is enforced, not faked: an entirely-absent requested condition →
`CONDITION-SCOPE-INCOMPLETE`; a condition missing for some stations →
`STATION-SCOPE-INCOMPLETE`; no station identity → `HEATMAP-NO-STATION-METRICS`;
unbalanced algorithm cohorts → `UNBALANCED-COHORTS` (unless `--allow-unbalanced`).

## Known limitations

- `plot ranking` reads from a **human evidence pack** only. `--source sql`
  returns exit 3 with guidance (descriptor-first SQL ranking is deferred).
- `plot heatmap` / `plot per-station` are implemented over SQL-selected results;
  they block honestly when station identity, the metric, or a requested condition
  is absent in the scope.
- `plot timeseries` is intentionally delegated to the storyboard services.
- `plot distribution` blocks (cohort-compatible sampling not yet implemented).
- PNG byte-identity is not guaranteed across runs; layout, dimensions, sidecar
  (minus `created_at_utc`) and caption are deterministic per seed.
- Promotion into `rendering/7-figures/` is a manual, confirmed step after visual
  approval; nothing is auto-promoted.
