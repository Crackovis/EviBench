# FIX-ST-COND — ST condition resolution in `illustration plot` · Report

| Field | Value |
|---|---|
| HEAD SHA (at start) | `3b05c1d47699b6c9fb2ced49fd50096bed5e6f49` |
| Date | 2026-07-01 |
| File changed | `imputebench/illustration/plot_data.py` |
| Tests added | `tests/illustration/test_plot_data_st_condition_resolution.py` |
| Schema / DB | unchanged |

## What changed

`resolve_st_condition()` was too fragile (it only read `bank.mask_family` and
checked structured numeric rate fields before the protocol identity), and the ST
fallback was never wired into `load_heatmap_data()` / `load_per_station_data()`.
The fix centralises a pure, deterministic ST resolution chain and activates it
automatically.

### Resolution chain (`plot_data.py`)

* `_normalise_mask_family()` — family accepted only if in `_MASK_FAMILIES`.
* `_family_from_bank()` — structured aliases (`mask_family`, `scenario`,
  `mask_scenario`, `missingness_type`, `family`, `support_kind`) first; otherwise
  **word-boundary** match over the bank identity text (`name`/`artifact_dir`/
  `id`/`notes`/support fingerprint). No naive substring; `mcar` is never assumed.
* `_parse_rate_from_bank_identity()` rewritten — order is now **identity text
  first** (`mcar_30` → 0.30), then declared structured fields
  (`declared_rate`/`target_rate`/`requested_rate`/`rate`/`mask_rate`), then the
  first-realization sidecar. Helpers: `_parse_rate_value()` (accepts `[0,1]` and
  `(1,100]` percent), `_parse_declared_rate_from_text()` (family token adjacent to
  the rate, either order, `[_:\-]` separators), `_parse_rate_from_first_realization_metadata()`
  (reads only `mask_metadata.json`; uses `target_rate`/`requested_rate`; refuses a
  family-contradicting sidecar). **`realized_rate` is never used as a `--rate` filter.**
* `resolve_st_condition()` now: `resolve_condition()` → `_bank_from_descriptor()` →
  optional defensive `_bank_from_contract_descriptor()` → `_family_from_bank()` →
  `_parse_rate_from_bank_identity()`. Returns the *declared* rate; never invents
  family or rate.
* `_select_scoped_results()` builds the mask-bank service **lazily** (only when a
  descriptor fails classical resolution) and memoises per `(bank_id, contract_id)`
  so a multi-thousand-result ST scope hits each of ~48 banks once; classical
  scopes never construct the service.
* `load_heatmap_data()` / `load_per_station_data()` gained
  `mask_bank_service: Any | None = None` and forward it (hermetic-test seam); the
  lazy build means the **CLI needs no change**.

### Preserved (no regression)

* `resolve_condition()` unchanged — classical / recipe-lineage stays the priority
  path. `query_descriptors()` is still **not** filtered by `mask_families`/`rates`
  for ST (the `maskings` join is empty); family/rate filtering stays in Python
  after resolution. No `SELECT *`, no README parsing, no fabricated data.

## Tests

```
pytest tests/illustration/test_plot_data_st_condition_resolution.py -q   → 9 passed
pytest tests/illustration -q                                             → 115 passed
```

The 9 new hermetic cases (no real DB, JSON sidecar only): family+rate from
`mask_family`+name; family from name when `mask_family` empty
(`sensor_dropout`/0.3); rate from sidecar (`spatial_block`/0.1); `realized_rate`
never used (`node_holdout`/0.1, not 0.14); `load_heatmap_data` ST wiring
(`selected_result_count==1`, `condition_order==(("mcar",0.1),)`);
`load_per_station_data` ST wiring (both algorithms present); classical results
never touch the bank service (tripwire); clean `None` + `CODE_CONDITION_SCOPE_INCOMPLETE`
when no reliable source.

## Real smoke (dry-run, `exp_st_v2`, live DB)

`benchmark_mask_banks` holds 155 records (48 referenced by ST results); ST banks
are named `st_<dataset>_<family>_<rate_pct>` with `mask_family` set.

| Command | Result |
|---|---|
| `plot heatmap … stgcn mcar 0.3` | condition **resolved** → blocks `NO-STATION` |
| `plot heatmap … stgcn sensor_dropout 0.3` | condition **resolved** → blocks `NO-STATION` |
| `plot per-station … stgcn,dcrnn,grin,ignnk mcar 0.3` | condition **resolved** → blocks `AMBIGUOUS-STATION` |

In every case the loader passes the `if not rows` guard (rows non-empty), i.e. the
`(family, rate)` condition resolved from the bank and survived the `--rate`
filter; no `CODE_CONDITION_SCOPE_INCOMPLETE`. `realized_rate` is not used as a
selection filter (verified by `test_realized_rate_is_never_the_declared_rate` and
by the identity-text-first ordering).

## Remaining limitation (out of scope)

`illustration plot heatmap` / `per-station` are **per-station** figures. ST results
live in graph-node space and carry no single-station identity, so these two
commands still block with `NO-STATION` / `AMBIGUOUS-STATION` on `exp_st_v2` — a
legitimate station-scope block, **not** a condition-resolution failure (§8.7,
§8.10 satisfied). ST evidence figures for Chapter 4 (spatial ranking, tier
comparison, graph-policy heatmap, cross-family) use the graph-aware loaders, which
benefit from the same resolution chain. A node-axis ST heatmap, if desired, is a
separate enhancement.

## Acceptance (§8)

1. `resolve_condition()` classical behaviour unchanged ✔
2. `resolve_st_condition()` resolves family/rate from a bank even when
   `mask_family` is missing but the identity is usable ✔
3. Returned rate is the declared rate, not `realized_rate` ✔
4–5. `load_heatmap_data()` / `load_per_station_data()` activate the ST fallback
   with no caller intervention (lazy service) ✔
6. `mcar`, `mar`, `mnar`, `spatial_block`, `sensor_dropout`, `node_holdout`
   recognised ✔
7. A genuinely incomplete scope still raises `CODE_CONDITION_SCOPE_INCOMPLETE` ✔
8. No figure produced from an invented condition ✔
9. Hermetic tests pass without a real database ✔
10. Smoke commands no longer block on **condition resolution** when banks exist ✔
    (they may still block on station scope, which is inherent to ST).

No commit (orchestrator owns it). `KMP_DUPLICATE_LIB_OK` is not required by the
patch (used only as a local OpenMP workaround when running Python here).
