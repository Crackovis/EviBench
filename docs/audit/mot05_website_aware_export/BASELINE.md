# MOT05 Baseline

Before MOT05, `results export-human` published exactly one evidence pack into
`request.output_dir`.

Observed baseline:

- `output_dir` was always the atomic publisher target.
- The publisher was already safe for one target directory, but could replace a
  whole site root if the user pointed `--output-dir` at a cumulative directory.
- No `experiment_id`, root hub, or public `metadata.json` existed.
- `evidence_dashboard.html` was the only direct HTML entry point.
- The root `index.html`, `assets/`, and sibling experiment directories had no
  first-class preservation policy because site mode did not exist.

Required correction:

- keep legacy behavior unchanged when `--experiment-id` is absent;
- when `--experiment-id` is present, publish only into
  `site_root/experiment_id`;
- include experiment metadata and index alias in the experiment pack before
  manifest/checksum collection;
- render the root hub after successful pack publication.
