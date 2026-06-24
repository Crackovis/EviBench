# Human evidence pack — worked example

This walks through producing and reading a pack for the LondonAQ classical
benchmark.

## 1. Produce the pack

```bash
imputebench thesis export-human \
  --recipe-book official_londonaq_classical_benchmark \
  --tier a \
  --algorithm-id linear_interpolation \
  --algorithm-id nearest_interpolation \
  --algorithm-id dacpi \
  --title "LondonAQ Temporal Imputation Evidence" \
  --output-dir ./evidence_pack
```

With statistics (optional extra):

```bash
pip install -e ".[evidence-stats]"
imputebench thesis export-human \
  --recipe-book official_londonaq_classical_benchmark --tier a \
  --include-stats --caption-chapter 4 \
  --output-dir ./evidence_pack_stats
```

## 2. Open it

* Open `evidence_pack/evidence_dashboard.html` in any browser — it is a single
  offline file (no server, no network).
* Or start at `evidence_pack/README.md`, which links the dashboard, the primary
  comparison table, and the provenance manifest.

## 3. Verify the numbers

```bash
# Re-check checksums (every file except checksums.sha256 itself)
cd evidence_pack
python - <<'PY'
import hashlib, pathlib
root = pathlib.Path(".")
listed = dict(
    line.split("  ", 1)[::-1]
    for line in (root / "provenance/checksums.sha256").read_text().splitlines() if line
)
bad = []
for rel, sha in listed.items():
    h = hashlib.sha256((root / rel).read_bytes()).hexdigest()
    if h != sha:
        bad.append(rel)
print("OK" if not bad else f"MISMATCH: {bad}")
PY
```

The per-recipe CSVs in `tables/comparison_by_recipe/` carry explicit
`is_best`, `rank_allowed`, `comparison_status`, and `claim_level` columns so a
reader can re-derive every BEST and rank.

## 4. Trace a name back to its source

Visible names are human (e.g. `mcar_30_dacpi_test_r000`). The mapping back to the
internal result/run/realization ids lives only in
`provenance/id_map.json` — UUIDs never appear in any visible file.

## What the gates mean

* **`†` after a cell** — the cohort is not comparable (no benchmark contract),
  so the value is descriptive only.
* **bold cell** — a gated BEST: compatible cohort, known metric direction, ≥2
  algorithms, non-mock.
* **`EXPLORATORY ONLY` banner** — ranking, BEST, and significance claims are
  disabled for that selection.
* **a blocked storyboard card** — the technical export could not render it; the
  pack shows the block and never fabricates a figure.
