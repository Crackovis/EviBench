# MOT03 Mask Bank Archive Blindness Baseline

Captured for `MASK_BANK_ARCHIVE_BLINDNESS_EviBench_ROBUST.md`.

## Repository

- HEAD SHA at implementation audit: `5191f959f7aa95670d9ec8c55c2ccb814682e88b`
- Implementation root: `Interfaces/EviBench`
- SQLite schema version constant: `12`
- Local policy: no duplicate deletion, no resolver weakening, no schema migration

## Database Snapshot

Database inspected:

`H:\Documents\Uncompressed\byContext\ResearchCenter\Takoudjou\MasterDegree\inControlScienceEngineering\Experimentations\Interfaces\EviBench\data\metadata.db`

| Table | Total rows | SQL archived != payload_json.archived |
|---|---:|---:|
| `benchmark_mask_banks` | 94 | 29 |
| `benchmark_contracts` | 91 | 0 |

## MCAR 0.3 Mask Bank Snapshot

The MCAR 0.3 lane contained 12 matching mask-bank payloads. The important
baseline signal is that SQL and payload disagreed on active membership.

| Count | Value |
|---|---:|
| MCAR 0.3 total candidates | 12 |
| Active by SQL column | 1 |
| Active by stale payload | 12 |
| SQL/payload archive mismatches | 11 |

Observed active-by-SQL official bank:

- `cabf2377-f3e9-4b41-b24d-1d78a0f782b7`
- `Official LondonAQ Mask Bank - MCAR 0.3 - test`

Examples of SQL-archived rows still active in `payload_json`:

- `c3c715b9-c452-4423-b1af-0dab740723da`
- `02b8fb64-3a20-49a3-90d3-41f6c00a23a4`
- `f21cd127-df19-483d-900e-33e2a4019dcd`

## Prepare Baseline Note

The spec names:

```bash
python -m imputebench experiment temporal prepare status --recipe-book official_londonaq_classical_benchmark --tier a --format json
```

The installed CLI has no `prepare status` subcommand; available prepare commands
are `dry-run`, `materialize`, and `verify`. The equivalent non-mutating validation
used for this patch is `prepare dry-run`.

Before the repository overlay, stale `payload_json.archived=false` could expose
the 11 SQL-archived MCAR 0.3 duplicates to the service/resolver. After the patch,
the same data is read with the SQL `archived` column as authoritative.
