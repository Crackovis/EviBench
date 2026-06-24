# Built-in Evidence Presets

EviBench ships with six built-in evidence presets. They are protected (immutable) — to customize, clone them first.

## Preset Index

| Preset ID | Items | Domain | Targets |
|-----------|-------|--------|---------|
| `quick_inspect` | 5 | Any | result, run |
| `comparison_ready` | 5 | Any | result, run |
| `thesis_chapter04` | 8 | temporal | result, comparison |
| `thesis_chapter06` | 8 | spatiotemporal | result, run |
| `debug_inventory` | 4 | Any | result, run |
| `human_thesis_sources` | 7 | Any | result, run, comparison |

## human_thesis_sources (Sheet 15)

Protected source-item set consumed by `thesis export-human`: `result_summary`,
`metric_table`, `runtime_breakdown`, `result_storyboard`, `provenance_manifest`,
`comparison_ready_signal`, `artifact_inventory`. It selects the structured
sidecars the human evidence layer reads; it never replaces the other presets and
is not intended for direct `results evidence export` use. See
[Human-Readable Evidence Export](../../Human_Readable_Evidence_Export.md).

## quick_inspect

Quick result overview: `result_summary`, `metric_table`, `runtime_breakdown`, `artifact_inventory`, `provenance_manifest`.

Use for: rapid inspection of a single result.

## comparison_ready

Comparison readiness check: `metric_table`, `comparison_ready_signal`, `artifact_inventory`, `comparison_table`, `comparison_gate_verdict`.

Requires comparison compatibility. Use for: verifying results are ready for comparison.

## thesis_chapter04

Temporal chapter evidence (Chapter 04): `metric_table`, `runtime_breakdown`, `training_evidence_pack`, `training_curve_figure`, `comparison_table`, `comparison_ready_signal`, `comparison_storyboard`, `comparison_gate_verdict`.

Targets: result, comparison. Domain: temporal. Mock forbidden. Gate profile: `comparison_ready`.

## thesis_chapter06

Spatiotemporal chapter evidence (Chapter 06): `graph_characterization`, `spatial_missingness_characterization`, `st_algorithm_lifecycle`, `st_comparison_pack`, `st_storyboard_pack`, `st_gate_pack`, `st_figure_pack`, `st_chapter_manifest`.

Targets: result, run. Domain: spatiotemporal.

## debug_inventory

Diagnostic inventory: `artifact_inventory`, `provenance_manifest`, `runtime_breakdown`, `comparison_ready_signal`.

Mock results allowed. Diagnostic data allowed. Use for: debugging, verifying pipeline integrity.

## Creating Custom Presets

Clone a built-in preset:

```bash
imputebench results evidence preset clone quick_inspect --new-id my_preset --title "My Custom Preset"
```

Customize:

```bash
imputebench results evidence preset update my_preset --expected-revision 1 --from-file my_preset.yaml
```

Validate:

```bash
imputebench results evidence preset validate my_preset
```

Use in export:

```bash
imputebench results evidence export TARGET_ID --preset my_preset
```

## Schema Reference

Full preset schema: `docs/Evidence_Preset_Schema_V1.md`

JSON Schema: `imputebench/resources/schemas/evidence_preset_v1.schema.json`

YAML source files: `imputebench/resources/evidence_presets/*.yaml`
