# Results Inspect CLI Reference

`imputebench results inspect TARGET_ID` — human and scientific projection of a result, run, or comparison.

## Syntax

```
imputebench results inspect TARGET_ID
  --target-type auto|result|run|comparison
  --phase all|train|validate|test|execute
  --sections SECTION,...
  --format auto|rich|plain|table|json
```

## Target Resolution

The resolver discovers by prefix or explicit type:

| Target Type | Detected By |
|-------------|-------------|
| `result` | UUID with `-` separators (default) |
| `run` | UUID with `-` separators, explicit `--target-type run` |
| `comparison` | Non-UUID comparison spec ID |

Auto-resolution prefers result → run → comparison.

## Output Sections

### Identity
Result id, run id, dataset, algorithm, masking, phase, requested/actual execution class, domain, timestamps.

### Status
Execution status, scientific mode, mock mode, result ready, benchmark ready.

### Experimental Context
Masking authority, train strategy, realized rate, realization id, test posture.

### Metrics
Global metrics, split/phase metrics, pollutant metrics, node metrics — separated by scope. Phase-aware; the renderer never merges scopes.

### Timing
Runtime breakdown with source quality. Resolves `runtime_summary` → legacy `runtime_s` → missing.

### Scientific Lifecycle
Best epoch, stop epoch, epoch budget, stop reason, early stopping status, selection metric, checkpoint path, restore proof. Classical results show as not-applicable.

### Benchmark & Comparability
Benchmark contract, mask bank, realization, evaluation support fingerprint, shared parity status, metric scope.

### Graph Context
Graph id, graph policy, graph fingerprint, node index policy, node mapping fingerprint. Shown only when graph context exists (ST results).

### Artifacts
Canonical artifact paths with roles: prediction, mask, checkpoint, training history, artifacts directory. Metadata-first — no file content loaded.

### Evidence Availability
Summary of discoverable evidence items per state (available, derivable, blocked, missing, etc.). Full inventory via `results evidence list`.

### Provenance
Git commit, ImputeBench version, Python version, environment digest, recipe book/revision/entry/profile/materialization lineage.

## Formats

### Plain (`--format plain`)
Terminal table with sections, fields, and rows. ASCII-safe glyph fallback on legacy terminals.

### JSON (`--format json`)
Single valid JSON document with `imputebench.results-inspection/v1` schema.

## Examples

```bash
# Inspect a classical result
imputebench results inspect 7dc94779-26f3-4f9c-a760-72380cb2150f

# Inspect only metrics and timing
imputebench results inspect 7dc94779 --sections metrics,timing

# Inspect a run summary
imputebench results inspect 613cb0d2 --target-type run

# JSON output for scripting
imputebench results inspect 7dc94779 --format json

# Inspect test-phase results only
imputebench results inspect 7dc94779 --phase test
```

## Caveats

- Never loads complete prediction arrays (metadata-first)
- Evidence availability delegates to the inventory service; failure degrades to a pointer note
- Mock results display a banner and are excluded from rankings
