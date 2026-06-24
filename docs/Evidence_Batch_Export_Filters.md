# Evidence Batch Export Filters (Sheet 11B §12)

`results evidence export-batch` selects a cohort through the **SQL-first**
`ResultSelectionService` — never by hydrating the whole `results` table and
never by parsing run names.

## Canonical command

```bash
imputebench results evidence export-batch \
  --recipe-book official_londonaq_classical_benchmark \
  --tier a --mask-family mcar --rate 0.3 --phase execute \
  --preset comparison_ready --dry-run
```

## Options

```
--result-id        (repeatable)      --run-id            (repeatable)
--dataset-id                          --recipe-book
--recipe-revision                     --tier / --recipe-profile
--recipe-entry     (repeatable)       --algorithm-id      (repeatable)
--algorithm-family (repeatable)       --masking-id        (repeatable)
--mask-family      (repeatable)       --rate              (repeatable)
--phase            (repeatable)       --realization       (repeatable)
--benchmark-contract                  --graph-policy      (repeatable)
--status           (repeatable)       --execution-class   (repeatable)
--include-unlinked                    --limit / --max-targets
--what | --preset                     --output-dir / --layout
--overwrite-policy / --strictness     --dry-run / --execute / --confirm-token
```

### Deprecated aliases (export-batch only)

`--families` → `--mask-family`, `--rates` → `--rate`. A one-line deprecation
guidance message is emitted; the values are merged into the canonical filters.

### Tier ↔ profile

`--tier a` normalises to `recipe_profile_id = "a"`. Supplying both `--tier` and
`--recipe-profile` with different values is a usage error.

## SQL semantics

`result_selection_queries.py` joins:

```sql
FROM results r
LEFT JOIN algorithms a ON a.id = r.algorithm_id
LEFT JOIN maskings   m ON m.id = r.masking_id
```

and filters with `a.family IN (...)`, `m.type IN (...)`,
`ABS(m.rate - ?) <= 1e-9`, `r.actual_execution_class IN (...)`. The descriptor
projection adds `algorithm_family`, `mask_family`, `mask_rate`. Ordering is
stable: recipe entry → algorithm id → mask family → rate → realization → phase →
result id.

Recipe filters exclude recipe-unlinked legacy rows unless `--include-unlinked`
is passed. Runtime-generated masks without indexed family/rate are excluded from
family/rate filters and counted as unlinked.

## Outcomes

| Selection status | Exit |
|---|---|
| `selected` | plan & (with `--execute`) export |
| `blocked:no_results` | 2 |
| `ambiguous_selection` | 2 + grouped ambiguity report |

The full selection report (`query`, `status`, `total_matched`, `selected_ids`,
`unlinked_ids`, `ambiguities`, `truncated`, `warnings`) is recorded in the
export manifest under `selection.selection`.
