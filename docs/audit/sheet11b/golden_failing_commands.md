# Sheet 11B — Golden failing command outputs (baseline)

These are the user-visible failures Sheet 11B must eliminate. Captured at HEAD
`3cbc8db6`, schema v10.

## A. Provider export raises Phase 11-08 marker

Every built-in provider's `export()` (inherited from `BaseEvidenceProvider`)
raises:

```text
NotImplementedError: Provider '<provider_id>' export lands in Phase 11-08.
```

Consequence: `results evidence export RESULT_ID --what result_summary` reaches
`ExportEngine._invoke_provider`, the export raises, and the item is recorded as
**blocked** with `error_class=NotImplementedError`. No real file is written.

## B. Fresh-database preset list is empty

```text
$ imputebench results evidence preset list
No presets found.
```

(Programmatic reproduction: `PresetRegistryService(EvidencePresetRepository(fresh_conn),
PresetCodec()).list_presets()` → `[]`.)

## C. Batch export ignores recipe/family/rate filters

`results evidence export-batch` accepts only run / dataset / algorithm /
execution-class / phase, hydrates the whole `results` table via
`ResultService.list(run_id=None)`, and has no `--recipe-book`, `--tier`,
`--mask-family`, or `--rate`. The brief command

```text
imputebench results evidence export-batch \
  --recipe-book official_londonaq_classical_benchmark \
  --tier a --mask-family mcar --rate 0.3 --phase execute \
  --preset comparison_ready --dry-run
```

fails with `no such option: --recipe-book`.

## Acceptance after 11B

- A. zero built-in provider `NotImplementedError` paths.
- B. >= 5 built-ins visible on a fresh DB.
- C. recipe/tier/family/rate selection resolves through SQL-first services.
