# Human-Readable Evidence Export

`thesis export-human` assembles an **auditable, offline, portable evidence
pack** on top of the existing technical export engine. It does not create a
second export engine: it reuses `ExportPlanner`, `ExportEngine`, and the evidence
providers, reads their structured machine export, and projects a presentation
layer that a human can open, read, and verify without a server.

## Invariants

```
every visible name is human
every metric is traceable
every comparison belongs to a cohort
every BEST is gated
every test is paired
every figure has a provenance
every block stays visible
every claim stays bounded
every file is portable
every pack is atomic
```

The pack is a projection. It never replaces the persisted results, the
comparison specs, the gates, or the machine manifest, and it can never raise the
grade of a claim above its most restrictive source.

## What it produces

```
evidence_pack/
├── README.md                  ← explains the pack, links the dashboard
├── evidence_dashboard.html    ← single self-contained offline page
├── tables/                    ← comparison matrix + per-recipe tables (md/csv/json)
├── figures/                   ← ranking chart + storyboard gallery (+ sidecars)
├── stats/                     ← paired Wilcoxon tests + STATUS
└── provenance/                ← manifest, source manifest, id map, selection,
                                  blocked items, command, environment, checksums
```

## Pipeline

```
SQL-first selection
→ temporary technical export (source_export/)
→ read structured sidecars (never parse Markdown for a metric)
→ scientific compatibility (cohorts + eligibility)
→ human aliases (no UUID in any visible name)
→ comparable cohorts
→ tables / figures / gated statistics
→ executive README
→ offline HTML dashboard
→ provenance / checksums
→ validation
→ atomic publication
```

## Two stagings, atomic publication

```
<parent>/.evibench_human_<token>/
├── source_export/   ← the reused technical export
└── pack_staging/    ← the assembled human pack
```

`pack_staging/` is validated, checksummed, and only then **moved** into
`--output-dir`. If anything fails, the existing output directory is left intact.

## Grades and claims

The pack grade is bounded by the most restrictive source cohort:

```
thesis_ready > reportable_with_caveats > exploratory > diagnostic > partial > blocked
```

* A cohort without a benchmark contract is *descriptive*: it appears in the
  tables, but ranking, BEST highlighting, and significance claims are disabled.
* A mock result is *diagnostic only* and is never comparable.
* A blocked primary artifact caps the grade at `partial`.

## Statistics (optional, gated)

`--include-stats` runs paired **Wilcoxon signed-rank** tests. Pairing is exact:
two observations pair only when they share the benchmark realization **and** the
evaluation-support fingerprint. SciPy is an optional extra:

```bash
pip install -e ".[evidence-stats]"
```

Without SciPy the statistics section is blocked with an explicit reason and
**no p-value is fabricated**. At `n = 5` an exact two-sided Wilcoxon without ties
cannot reach `p < 0.0625`, and the pack records that power caveat rather than
hiding it.

## Determinism

Two identical generations (ignoring timestamps and ids) produce the same
content fingerprint, the same visible names, the same ordering, and the same
text. The fingerprint excludes `generated_at`, `pack_id`, the temporary
source-export id, invocation/destination metadata, and the dashboard (which
embeds the fingerprint itself).

See also: [CLI reference](Human_Readable_Evidence_Export_CLI.md) ·
[Manifest schema](Human_Evidence_Pack_Schema_V1.md) ·
[Worked example](examples/human_evidence/README.md).
