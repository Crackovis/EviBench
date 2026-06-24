# Sheet 15 — Implementation Report (Human-Readable Evidence Export)

## Summary

The Human-Readable Evidence Export was implemented as an **assembly layer** on
top of the existing technical export engine. No second export engine was
created: `ExportPlanner`, `ExportEngine`, and the evidence providers remain the
source of provider logic, checksums, target resolution, and the machine
manifest. The human layer reads that structured export and projects an
auditable, offline, portable pack whose grade is bounded by its most restrictive
source.

All ten invariants hold: every visible name is human, every metric is traceable,
every comparison belongs to a cohort, every BEST is gated, every test is paired,
every figure has a provenance, every block stays visible, every claim stays
bounded, every file is portable, every pack is atomic.

## Delivered

### Domain + read models
- `imputebench/domain/evidence/human_export.py` — request + validation, alias,
  cohort, metrics, statistics, storyboard contracts, grades/claim lattice, error
  codes, naming primitives (slugify, rate label, UUID regex, canonical stem).
- `imputebench/read_models/human_evidence_export.py` — pack aggregate +
  `imputebench.human-evidence-pack/v1` manifest with a stable content
  fingerprint.

### Services (`imputebench/services/results_interaction/human_export/`)
`selection_service`, `source_export_service`, `source_reader`,
`identity_service`, `cohort_service`, `metric_service`, `table_service`,
`statistics_service`, `caption_service`, `narrative_policy`, `storyboard_service`,
`ranking_figure_service`, `manifest_service`, `pack_validator`, `pack_publisher`,
and the orchestrating `human_evidence_export_service`.

### Presentation (`imputebench/presentation/human_evidence/`)
`css_template`, `javascript_template`, `html_template` (escaping + skeleton),
`markdown_renderer` (README + stats sidecars), `dashboard_renderer`
(single-file offline HTML, CSP, data-URI gallery).

### Application + CLI + preset
- `application/commands/human_evidence_commands.py`,
  `application/use_cases/export_human_evidence_pack.py`.
- `cli/thesis/export_human.py`, wired into `cli/thesis/group.py`.
- `resources/evidence_presets/human_thesis_sources.yaml` (protected source preset).

### Dependencies
- `pyproject.toml` — added the optional `evidence-stats = ["scipy>=1.11,<2"]`
  extra. The core never imports SciPy; statistics degrade explicitly when absent.

### Tests
`tests/human_evidence/` — 24 files covering request validation, naming,
selection, source export/read, cohorts, metrics, tables, statistics,
storyboards, ranking, captions, narrative, renderers, manifest, validator,
publisher, use case, CLI, integration, security, determinism, performance.

### Docs
`docs/Human_Readable_Evidence_Export.md`,
`docs/Human_Readable_Evidence_Export_CLI.md`,
`docs/Human_Evidence_Pack_Schema_V1.md`,
`docs/examples/human_evidence/README.md`; updated
`docs/results/evidence_presets/README.md` and `docs/CLI_Reference.md`.

## Verification

- `pytest tests/human_evidence` — 108 passed (+ 1 slow performance test: 90
  results assembled in ~14 s, well under the 30 s budget).
- Regression — `tests/results_interaction` export/planner/engine/providers/
  storyboard/comparison/preset suites pass. The one pre-existing unrelated
  failure (`test_recipe_lineage_persistence::test_schema_version_is_ten`,
  schema at 11 from prior sheets) is not touched by this work.
- Key behaviours observed end-to-end: DACPI ranks BEST with *lower* RMSE, fully
  gated; statistics report the honest `p = 0.0625` floor at n=5 with the power
  caveat (never a fabricated `p = 0.008`); the dashboard is a single offline file
  with a CSP and no external URL or visible UUID; two identical generations
  produce the same content fingerprint and identical table/README text.

## Notable decisions

- **Source items, not a seeded preset.** The source export passes the
  `human_thesis_sources` item ids directly to the planner, so it does not depend
  on the preset registry being seeded. The protected YAML still ships and is
  auto-discovered by the builtin seeder (preset count 5 → 6).
- **Fingerprint exclusions.** Beyond `generated_at`/`pack_id`/source-export id,
  the fingerprint also excludes `source_manifest.json` (foreign timestamp),
  `command.json` (destination/flags), and `evidence_dashboard.html` (it embeds
  the fingerprint). This is what makes the determinism guarantee hold across
  output directories.
- **`test_builtin_preset_bootstrap.py`** was updated from an exact `== 5` count
  to assert the canonical five plus `human_thesis_sources`, preserving the real
  invariant (re-seeding never bumps a revision past 1).

## Acceptance

Architecture (machine engine preserved, SQL-first, thin CLI, no Streamlit in the
human services), pack structure, naming (zero public UUID, `0.3 → 30`,
deterministic collisions, complete id map, path traversal rejected), tables
(mean/std/n, gated BEST, ties, clean CSV, NaN-free JSON, deterministic order),
figures (storyboard reuse, no fake PNG, sidecars, captions, boundaries, blocked
visible), statistics (paired, optional, raw+adjusted p, effect size, n visible,
K=5 caveat, no hard-coded p), HTML (offline, CSP, escaped, printable,
accessible), claims (inherited grade, no auto-promotion, mock separated,
exploratory banner), performance, and regression criteria are met.
