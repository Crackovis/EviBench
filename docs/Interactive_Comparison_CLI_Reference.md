# Interactive Comparison CLI Reference

`imputebench results compare` — interactive result comparison with scientific-validity gating.

## Preserved Commands

| Command | Purpose |
|---------|---------|
| `results compare list` | List persisted comparison specs |
| `results compare show SPEC_ID` | Display a comparison spec |
| `results compare delete SPEC_ID` | Delete a comparison spec |

## Interactive Commands

### `results compare results ID1 ID2 ...`

Compare explicit result IDs side by side.

```
imputebench results compare results RESULT_ID... [--aggregate MODE] [--format table|json|csv|markdown]
```

Scientific gates apply: mixed benchmark contracts, mask banks, support fingerprints, phases, datasets, or graphs block global ranking.

### `results compare runs RUN_ID ...`

Compare all results from specified runs.

```
imputebench results compare runs RUN_ID... --phase PHASE [--aggregate MODE] [--format ...]
```

Requires `--phase` to disambiguate which result phase to compare.

### `results compare query`

Compare results matching SQL-first selection filters.

```
imputebench results compare query --dataset-id ID --algorithm-id ID --execution-class CLASS --phase PHASE ...
```

### `results compare table SPEC_ID`

Render a persisted comparison spec as a side-by-side table.

```
imputebench results compare table SPEC_ID [--format table|json]
```

## Aggregation Modes

| Mode | Description |
|------|-------------|
| `none` | Raw per-result values (default) |
| `mean` | Arithmetic mean |
| `median` | Median |
| `std` | Standard deviation |
| `CI95` | 95% confidence interval |
| `IQR` | Interquartile range |

Mode `none` is required when `n < 3`. Aggregation requires `--aggregate`.

## BEST Marker

The algorithm with the best metric value (lowest MAE/RMSE) in a **compatible** cohort receives a `BEST` marker. BEST is disabled when:
- Cohort is `exploratory_only` (no benchmark metadata)
- `--allow-incompatible` flag partitions cohorts (no global BEST)
- Aggregation mode is active (per-realization, not aggregate)

## Compatibility Gating

Checked before ranking (§20.4):

| Check | Effect of Mismatch |
|-------|-------------------|
| Benchmark contract | Blocks cohort |
| Mask bank | Blocks cohort |
| Evaluation support fingerprint | Blocks cohort |
| Phase | Blocks cohort |
| Dataset | Blocks cohort |
| Graph policy/fingerprint | Blocks cohort (ST) |
| Execution class (mock/scientific) | Blocks cohort |
| Node mapping (ST) | Blocks cohort |

`--allow-incompatible` partitions into compatible sub-cohorts; global BEST is disabled.

## Formats

| Format | Output |
|--------|--------|
| `table` | Terminal table with BEST marker, dispersion columns |
| `json` | Single valid JSON document; no stdout contamination |
| `csv` | CSV with header row |
| `markdown` | Markdown table |

## Examples

```bash
# Compare two classical results
imputebench results compare results RESULT_A RESULT_B

# Compare all test-phase results from a run
imputebench results compare runs RUN_ID --phase test

# Ranked comparison with aggregation
imputebench results compare results R1 R2 R3 R4 --aggregate mean --format table
```
