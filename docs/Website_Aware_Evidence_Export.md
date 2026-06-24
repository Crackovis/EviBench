# Website-Aware Human Evidence Export

MOT05 adds a cumulative local-site mode to `imputebench results export-human`.
The legacy mode is unchanged: without `--experiment-id`, `--output-dir` is the
pack directory.

## Usage

Legacy single-pack export:

```bash
imputebench results export-human \
  --recipe-book official_londonaq_classical_benchmark \
  --tier a \
  --output-dir ./evidence_pack
```

Website-aware export:

```bash
imputebench results export-human \
  --recipe-book official_londonaq_classical_benchmark \
  --tier a \
  --output-dir ./exp_evidences \
  --experiment-id exp1 \
  --hub
```

In website-aware mode:

- `--output-dir` is interpreted as the site root.
- The pack is published atomically into `site_root/experiment_id/`.
- `site_root/index.html` is regenerated only after the experiment pack is
  published successfully.
- Existing sibling experiments, `assets/`, and custom root files are preserved.

Each experiment directory contains:

- `index.html`, an alias of `evidence_dashboard.html`;
- `metadata.json`, included in the pack manifest and checksums;
- the usual `README.md`, `tables/`, `figures/`, `stats/`, and `provenance/`.

## Experiment IDs

`--experiment-id` must match:

```text
[a-z0-9][a-z0-9_-]{1,63}
```

The exporter rejects path separators, spaces, `:`, `.`, `..`, Windows reserved
device names, and site-reserved names such as `assets`, `provenance`,
`index.html`, `robots.txt`, and `sitemap.xml`.

## Migration

If an old single-pack export already exists at the site root, website-aware mode
does not delete it. It emits warning `HRE-SITE-004` and publishes only the
requested experiment subdirectory. A manual migration can move the old pack into
a chosen experiment directory, then rerun with `--hub`.

## Recovery

If hub rendering fails, the experiment pack remains published. Fix the invalid
metadata or hub title/subtitle/description, then rerun the export with
`--overwrite-policy reuse-identical --hub` or regenerate the hub through a normal
website-aware export.

The CLI performs no network access for recovery or migration.
