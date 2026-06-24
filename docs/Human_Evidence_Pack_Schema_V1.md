# Human Evidence Pack — manifest schema v1

Schema id: `imputebench.human-evidence-pack/v1`
File: `provenance/manifest.json`

```jsonc
{
  "schema": "imputebench.human-evidence-pack/v1",
  "pack_id": "human-pack-...",            // not in the fingerprint
  "content_fingerprint": "...",            // stable SHA-256 of pack content
  "title": "LondonAQ Temporal Imputation Evidence",
  "generated_at": "2026-06-16T00:00:00Z", // not in the fingerprint
  "source_export": {
    "schema": "imputebench.evidence-export/v1",
    "export_id": "...",                    // not in the fingerprint
    "manifest_path": "provenance/source_manifest.json"
  },
  "selection": { ... },                    // SQL-first query + exclusions
  "cohorts": [ { "cohort_id": "...", "key": {...}, "comparability_status": "...",
                 "claim_level": "...", "ranking_allowed": true } ],
  "files": [ { "path": "README.md", "sha256": "...", "size_bytes": 123, "role": "readme" } ],
  "blocked_items": [ { "object_type": "...", "reason_code": "...", "message": "..." } ],
  "warnings": [],
  "pack_grade": "reportable_with_caveats",
  "claim_level": "bounded_comparison",
  "claim_limits": [ "..." ],
  "statistics_status": "computed | not_requested | blocked_dependency_missing | blocked_insufficient_data",
  "dashboard_gallery_policy": "auto",
  "numbering_status": "provisional | final",
  "environment": { "git_commit_hash": "...", "imputebench_version": "...", "python_version": "..." }
}
```

## Grades

```
thesis_ready > reportable_with_caveats > exploratory > diagnostic > partial > blocked
```

The presentation layer can only narrow this grade; it never promotes it above the
most restrictive source cohort.

## Content fingerprint

The fingerprint is the SHA-256 of a canonical (sorted-key) JSON of the pack
content. It **excludes**:

* `generated_at`, `pack_id`, the source-export `export_id`;
* `provenance/manifest.json`, `provenance/checksums.sha256` (meta);
* `provenance/source_manifest.json` (carries the source export's timestamp/id);
* `provenance/command.json` (records destination + flags, not content);
* `evidence_dashboard.html` (embeds the fingerprint itself).

`reuse-identical` compares this fingerprint, not the volatile manifest bytes.

## Checksums

`provenance/checksums.sha256` covers **every file except itself**, in
lexicographic order, `"<sha256>  <relative/path>"` per line. The manifest never
contains its own hash.

## Always-present files

Even a partial pack contains:

```
README.md
evidence_dashboard.html
provenance/manifest.json
provenance/source_manifest.json
provenance/selection.json
provenance/blocked_items.json
provenance/checksums.sha256
stats/STATUS.md
```

## Companion schemas

| Schema | File(s) |
|---|---|
| `imputebench.human-comparison-table/v1` | `tables/*.json` |
| `imputebench.human-evidence-id-map/v1` | `provenance/id_map.json` |
| `imputebench.human-pairwise-tests/v1` | `stats/pairwise_tests.json` |
| `imputebench.human-evidence-blocked/v1` | `provenance/blocked_items.json` |
| `imputebench.human-evidence-command/v1` | `provenance/command.json` |

## Error codes

```
HRE-SEL-001 empty_selection         HRE-SRC-001 source_export_failed
HRE-SEL-002 unresolved_target       HRE-SRC-002 source_manifest_missing
HRE-SEL-003 ambiguous_target        HRE-SRC-003 source_schema_unsupported
HRE-SEL-004 target_limit_exceeded   HRE-SRC-004 source_file_missing
HRE-SEL-005 no_test_results         HRE-SRC-005 source_sidecar_invalid
                                    HRE-SRC-006 source_checksum_mismatch
HRE-ID-001 algorithm_identity_missing       HRE-COHORT-001 benchmark_contract_missing
HRE-ID-002 mechanism_identity_missing       HRE-COHORT-002 mask_bank_mismatch
HRE-ID-003 realization_identity_missing     HRE-COHORT-003 support_mismatch
HRE-ID-004 unsafe_visible_name              HRE-COHORT-004 phase_mismatch
HRE-ID-005 alias_collision_unresolved       HRE-COHORT-005 metrics_missing
                                            HRE-COHORT-006 mock_result
HRE-STATS-001 dependency_missing            HRE-COHORT-007 insufficient_algorithms
HRE-STATS-002 insufficient_pairs            HRE-FIG-001 storyboard_blocked
HRE-STATS-003 all_zero_differences          HRE-FIG-002 image_missing
HRE-STATS-004 pairing_mismatch              HRE-FIG-003 sidecar_missing
HRE-STATS-005 correction_failed             HRE-FIG-004 ranking_blocked
                                            HRE-FIG-005 invalid_image
HRE-PACK-001 output_exists          HRE-PACK-004 checksum_failed
HRE-PACK-002 path_traversal         HRE-PACK-005 atomic_publish_failed
HRE-PACK-003 validation_failed      HRE-PACK-006 offline_dependency_detected
```
