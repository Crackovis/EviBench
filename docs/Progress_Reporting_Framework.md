# Progress Reporting Framework — User & Developer Guide

## 1. Why progress reporting exists

Long-running `imputebench` CLI commands (ARMA order selection, stationarity diagnostics,
temporal experiments) can run for minutes or hours. Before this framework, users had no way
to tell whether the process was running, blocked, or close to completion.

This framework provides:
- **Consistent terminal progress** via `tqdm` or `rich`
- **Machine-readable event traces** via JSONL
- **Low-overhead silent mode** for CI/scripting
- **Safe nested scopes** for hierarchical progress

## 2. CLI-first invariant

Progress reporting lives in the CLI/service layer. It does **not** depend on Streamlit
or any GUI component.

> CLI builds and certifies. GUI reads and displays.

## 3. User options

### Root-level options

Root-level options appear before the subcommand:

```bash
python -m imputebench --progress study temporal arma-order --dataset-id london_aq
python -m imputebench --no-progress study temporal arma-order --dataset-id london_aq
python -m imputebench --progress-backend rich study temporal arma-order --dataset-id london_aq
python -m imputebench --progress-event-log events.jsonl study temporal arma-order --dataset-id london_aq
```

### Command-local options

Long-running commands also expose the same options locally:

```bash
python -m imputebench study temporal arma-order --progress --dataset-id london_aq
python -m imputebench study temporal arma-order --no-progress --dataset-id london_aq
python -m imputebench study temporal arma-order --progress-backend tqdm --dataset-id london_aq
python -m imputebench study temporal arma-order --progress-event-log events.jsonl --dataset-id london_aq
```

Local options override root-level options.

### Available flags

| Flag | Values | Default | Description |
|---|---|---|---|
| `--progress` / `--no-progress` | flag | auto-detect | Show/hide terminal progress |
| `--progress-backend` | `auto`, `tqdm`, `rich`, `silent` | `auto` | Terminal backend |
| `--progress-event-log` | path | none | Write JSONL progress events |

## 4. Backend selection

### Auto-detection priority

1. `--no-progress` → silent (no terminal output)
2. CI environment detected (`GITHUB_ACTIONS`, `CI`, etc.) → silent
3. Non-TTY (piped, redirected) → silent
4. `NO_COLOR` environment variable → tqdm (simpler formatting)
5. `--progress-backend rich` and `rich` installed → rich
6. `--progress-backend tqdm` or `auto` and `tqdm` installed → tqdm
7. Fallback → silent

### Force terminal output

In redirected/piped contexts, use `--progress` explicitly (the framework will detect
that stderr is a TTY and use it). For full override, set `force_terminal=True` in the
`ProgressConfig`.

## 5. JSONL event logs

When `--progress-event-log PATH` is set, structured JSONL events are written even when
terminal output is silent.

### Event schema

Each line is a valid JSON object:

```json
{
  "ts": "2026-06-13T18:30:00.123Z",
  "scope": "arma_order",
  "name": "Selecting ARMA orders",
  "event": "advance",
  "n": 1,
  "done": 42,
  "total": 50,
  "elapsedS": 10.5,
  "etaS": 2.0,
  "detail": "series_0: best (2,3)"
}
```

### Event types

| Event | Meaning | Key fields |
|---|---|---|
| `start` | Scope entered | `name`, `total`, `scopePath` |
| `advance` | Progress advanced | `n`, `done`, `total`, `elapsedS`, `etaS`, `detail` |
| `message` | Informational | `detail` |
| `warning` | Warning | `detail` |
| `error` | Error | `detail` |
| `skip` | Items skipped | `n`, `done`, `total`, `detail` |
| `complete` | Scope completed successfully | `done`, `total`, `elapsedS` |
| `fail` | Scope failed with exception | `elapsedS`, `detail` |

### Field rules

- `ts` — always present, UTC ISO-8601
- `scope` — always present if available
- `name` — always present for context events
- `elapsedS` — present for events inside a context
- `etaS` — present when total and rate are known
- `scopePath` — nested scope hierarchy, e.g. `["temporal_experiment", "mask_family:mcar"]`

## 6. Nested progress

Child scopes are supported via the `ProgressContext.child()` method:

```python
with reporter.context("Outer loop", total=50) as outer:
    for series in series_list:
        outer.advance(1, detail=f"series {series.id}")
        with outer.child("Fitting orders", total=25) as inner:
            for p, q in grid:
                inner.advance(1, detail=f"order ({p},{q})")
```

JSONL events include `scopePath` for nested identification. Terminal rendering of
nested bars is best-effort (tqdm support is limited; rich supports nested progress bars).

## 7. Service integration pattern

### CLI layer

```python
from imputebench.cli.progress_options import progress_options, resolve_progress_config
from imputebench.services.progress.progress_manager import ProgressManager

@click.command("my-command")
@progress_options
@click.pass_context
def my_command(ctx, progress, progress_backend, progress_event_log):
    config = resolve_progress_config(progress, progress_backend, progress_event_log, ctx)
    reporter = ProgressManager().create_reporter(config)
    try:
        service.run(..., progress_reporter=reporter)
    finally:
        reporter.close()
```

### Service layer

```python
from imputebench.services.progress import ProgressReporter

class MyService:
    def run(self, ..., progress_reporter: ProgressReporter | None = None):
        if progress_reporter is not None:
            with progress_reporter.context("Processing", total=len(items)) as ctx:
                for item in items:
                    process(item)
                    ctx.advance(1, detail=f"processed {item.id}")
        else:
            for item in items:
                process(item)
```

### Minimal pattern (wrap)

```python
for item in reporter.wrap(items, desc="Processing items", total=len(items)):
    process(item)
```

## 8. Parallel processing guidance

- **Parent process** owns progress reporting
- **Workers** return results and diagnostics; do NOT update progress directly
- Parent advances progress after each result is collected
- For joblib: use progress on task submission or completed-result collection

## 9. CI / non-TTY behavior

In CI environments or when not attached to a TTY:
- Terminal progress is **automatically silent**
- JSONL event logs still write if `--progress-event-log` is set
- No tqdm/rich import overhead
- No string formatting overhead

## 10. Troubleshooting

| Symptom | Cause | Solution |
|---|---|---|
| No progress bar in terminal | CI detected, or stderr not a TTY | Use `--progress` to force |
| `ModuleNotFoundError: tqdm` | tqdm not installed | Install `tqdm` or use `silent` backend |
| Progress bar corrupted output | stdout used for JSON output | Progress writes to stderr by default |
| Nested bars not showing | Backend doesn't support nesting | Use `rich` backend or flat progress |
| Event log not writing | Path directory doesn't exist | Framework creates parent directories |
