# `thesis export-human` — CLI reference

```bash
imputebench thesis export-human [OPTIONS]
```

Assembles a human-readable evidence pack. Selection is **SQL-first** (the same
query engine as `results evidence export-batch`); no directory scan ever selects
results. The command is a thin wrapper — every scientific decision lives in the
`ExportHumanEvidencePackUseCase` and the assembly services.

## Selection options

| Option | Meaning |
|---|---|
| `--result-id` | Explicit result id (repeatable) |
| `--run-id` | Experiment run id (repeatable; expanded to results) |
| `--comparison-id` | Persisted comparison spec id (primary source) |
| `--dataset-id` | Dataset id |
| `--recipe-book` / `--recipe-revision` | Recipe lineage |
| `--tier` / `--recipe-profile` | Recipe tier (maps to `recipe_profile_id`) |
| `--recipe-entry` | Recipe entry id (repeatable) |
| `--algorithm-id` | Algorithm id (repeatable) |
| `--algorithms a,b` | Comma alias for `--algorithm-id` |
| `--algorithm-family` | Algorithm family (repeatable) |
| `--masking-id` / `--mask-family` / `--rate` | Missingness filters |
| `--phase` | Lifecycle phase (default: `test`) |
| `--realization` | Realization id (repeatable) |
| `--benchmark-contract` | Benchmark contract id |
| `--graph-policy` / `--status` / `--execution-class` | Further filters |
| `--include-unlinked` | Include recipe-unlinked legacy results |
| `--limit` / `--max-targets` | Hard cap (default 100; exceeding fails with `HRE-SEL-004`) |

## Presentation options

| Option | Default | Meaning |
|---|---|---|
| `--title` | `Evidence pack` | Pack title |
| `--primary-metric` | `rmse` | Primary comparison metric |
| `--figures-dpi` | `150` | DPI for figures the human layer creates |
| `--dashboard-gallery` | `auto` | `auto` / `all` / `representative` / `none` |
| `--max-dashboard-images` | `30` | Cap on embedded gallery images |
| `--caption-chapter` | `0` | Chapter number (0 → provisional numbering) |
| `--pack-format` | `complete` | `complete` / `html` / `markdown` |

## Statistics options

| Option | Default | Meaning |
|---|---|---|
| `--include-stats / --no-stats` | `--no-stats` | Compute paired Wilcoxon tests |
| `--stats-strict` | off | Fail (`HRE-STATS-001`) if SciPy is missing |
| `--alpha` | `0.05` | Significance threshold |
| `--correction` | `benjamini_hochberg` | Multiple-comparison correction |

## Publication options

| Option | Default | Meaning |
|---|---|---|
| `--output-dir` | — | Output pack directory (required unless `--dry-run`) |
| `--overwrite-policy` | `fail` | `fail` / `reuse-identical` / `replace-generated` |
| `--strictness` | `skip-unavailable` | `skip-unavailable` / `fail-on-missing` |
| `--dry-run` | off | Plan only; write nothing |
| `--keep-failed-staging` | off | Retain staging on failure |
| `--format-output` | `auto` | `auto` / `text` / `json` |

## Examples

```bash
# Whole recipe tier
imputebench thesis export-human \
  --recipe-book official_londonaq_classical_benchmark --tier a \
  --output-dir ./evidence_pack

# Specific algorithms with statistics
pip install -e ".[evidence-stats]"
imputebench thesis export-human \
  --recipe-book official_londonaq_classical_benchmark --tier a \
  --algorithm-id linear_interpolation --algorithm-id nearest_interpolation \
  --algorithm-id dacpi --include-stats --output-dir ./evidence_pack_stats

# Persisted comparison as the primary source
imputebench thesis export-human --comparison-id <id> --include-stats \
  --output-dir ./evidence_pack

# Dry run (no writes), JSON output
imputebench thesis export-human \
  --recipe-book official_londonaq_classical_benchmark --tier a \
  --dry-run --format-output json
```

## Exit codes

| Code | Meaning |
|---|---|
| `0` | Pack published (or dry run succeeded) |
| `1` | Assembly error (`HRE-SRC-*`, `HRE-PACK-*`, …) |
| `2` | Blocked selection / cohort / output-exists (`HRE-SEL-*`, `HRE-COHORT-*`, `HRE-PACK-001`) |

## Error codes

`HRE-SEL-*` selection · `HRE-SRC-*` source export/read · `HRE-ID-*` naming ·
`HRE-COHORT-*` comparability · `HRE-STATS-*` statistics · `HRE-FIG-*` figures ·
`HRE-PACK-*` packaging. See the manifest schema for the full list.
