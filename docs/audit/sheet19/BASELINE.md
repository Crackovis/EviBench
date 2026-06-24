# Sheet 19 — Baseline and Framework Freeze (patch 19-00)

This baseline freezes the state captured before the multi-dimensional framework
was implemented. It is the fail-closed gate required by §21 / §23 of the sheet:
the private comparison-metrics framework MUST be resolvable and pinned by SHA-256.

## Repository

| Field | Value |
|---|---|
| Effective `HEAD` | `8171d44c40d92509f944ae6ab66194404fc05f48` |
| Workdir | `Interfaces/EviBench/` |
| Human pack schema (before) | `imputebench.human-evidence-pack/v2` |
| Framework schema (added) | `imputebench.human-framework-metrics/v1` |
| Framework policy id (added) | `human_multidimensional_relative_v1` |

## Framework reference freeze

| Field | Value |
|---|---|
| Path | `docs/.private_docs/reviews/COMPARISON_METRICS_FRAMEWORK.md` |
| Resolved | yes (local checkout / private submodule) |
| SHA-256 | `b3ed2880d325f8b0fce14a48d1014cdb915e4c0f2d06ad0a6a3c5144b13b5fa4` |
| Size | 34329 bytes |

The reference is present, so the gate is **green**. Had it been absent, patch
`19-00` would fail-closed (HFM-001 `framework_policy_missing`) rather than
silently proceeding without the authoritative methodology.

## Runtime source qualities (consumed, never reconstructed)

The framework reads `runtime.stages_ms.total` + `runtime.source_quality` from the
exported `result_summary.json` and never re-opens `data/metadata.db`:

```
native_timing_spans      → scoreable
runtime_summary_payload  → scoreable_with_caveat
legacy_runtime_s         → descriptive_only
missing                  → unavailable
```

## Cardinality policy

`540` (`4 algorithms × 3 stations × 9 recipes × 5 realizations`) is **not** a
production invariant. The framework gates on the `SelectionLedger`, the effective
result count, the balanced cohort intersection, and the per-metric availability
ledger. No literal `540` appears in production logic.
