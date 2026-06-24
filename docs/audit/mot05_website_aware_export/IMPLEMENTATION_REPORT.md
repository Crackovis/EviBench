# MOT05 Implementation Report

Implemented website-aware human evidence export.

## Functional Changes

- Added website fields to `HumanEvidenceExportRequest`:
  `experiment_id`, `hub`, `hub_title`, `hub_subtitle`, and `hub_description`.
- Added site-specific error codes `HRE-SITE-001` through `HRE-SITE-008`.
- Added strict experiment ID validation and site path resolution.
- In website mode, `output_dir` is treated as `site_root`; the effective pack
  target is `site_root/experiment_id`.
- Added `metadata.json` and `index.html` to the experiment staging directory
  before manifest/checksum generation.
- Added root hub rendering from `*/metadata.json` after successful pack publish.
- Preserved legacy mode when `experiment_id` is absent.
- Extended CLI output and JSON DTOs with site paths.

## Public Safety

- Public metadata is validated for UUID leaks, absolute paths, bounded strings,
  schema compatibility, and relative pack links.
- Hub title/subtitle/description are checked for UUID leaks before writing.
- Invalid experiment metadata is skipped with a hub warning instead of blocking
  other experiments.

## Atomicity

- The existing atomic pack publisher is reused with the effective experiment
  directory only.
- The hub renderer writes temporary files and replaces root files after render
  and UUID checks pass.
- Hub render failure leaves the published experiment pack intact and reports a
  warning from the export service.

## Tests

Added focused MOT05 tests under `tests/human_evidence/site/` for:

- experiment ID validation;
- site path resolution and legacy root detection;
- public metadata construction and safety;
- hub rendering, invalid metadata skipping, UUID rejection, and write failure
  preservation;
- CLI website options and validation;
- end-to-end website-aware export;
- experiment index alias;
- sibling/root preservation;
- dry-run site summary.
