# Progress Reporting Framework — Pre-Implementation Audit

**Date:** 2026-06-13  
**Auditor:** Sisyphus (implementation agent)  
**Spec:** SISYPHUS_IMPLEMENTATION_SHEET_05  
**Repository:** `Interfaces/EviBench/` — local source of truth

---

## 1. How is the root Click app wired?

- **Entry point:** `imputebench/__main__.py` → `from imputebench.cli import cli; cli()`
- **Root group:** `imputebench/cli/__init__.py`, line 57 — `@click.group(...)` with `context_settings={"help_option_names": ["-h", "--help"]}`
- **No root-level options or parameters on `cli()`** — the group function body is just `pass`
- **No `ctx.obj` initialization** at root level — `ctx.ensure_object(dict)` is NOT called
- **Commands added via `cli.add_command()`** (not decorators), using imported Click group/command objects
- **23+ command groups** registered: `dataset`, `algorithm`, `plugin`, `ingest`, `masking`, `run_group`, `result`, `locality`, `calibration`, `viewer`, `compare`, `lab`, `maintenance`, `env`, `config`, `admin`, `audit`, `repair`, `validate`, `evidence_gate`, `st`, `temporal`, `study`, `thesis`
- **`thesis`** is defined locally in `__init__.py` (as a group with subcommands), then added to root
- **Entry point in pyproject.toml:** `imputebench = "imputebench.cli:cli"`

**Conclusion:** Root is a plain group with no context object and no parameters. Root-level options CAN be added safely (Click supports `@click.group` params that apply before subcommands), but they must appear BEFORE the subcommand in argument order.

---

## 2. Can root-level options be safely added without breaking subcommands?

**Yes, but with the standard Click constraint:**

Click root options must appear BEFORE the subcommand:
```bash
python -m imputebench --progress study temporal arma-order
```

The spec (Section 3.1) correctly identifies this UX issue. The recommendation is:
- Add root-level options for global defaults
- Also provide command-level decorators for local overrides
- This dual approach satisfies both usage patterns

**Risk:** Root-level `--no-progress` could incorrectly suppress progress on commands that don't use the framework. Mitigated by graceful degradation — commands that don't consume the config will simply ignore `ctx.obj["progress_config"]`.

**Safety check:** Since no other root-level options exist, there are no ordering conflicts with existing options.

---

## 3. Which command files currently define long-running commands?

All identified long-running commands from code inspection:

| Command file | Command | Expected duration | Current progress |
|---|---|---|---|
| `cli/study_cmd.py` | `study temporal arma-order` | Minutes–hours | ✅ Has `--progress/--no-progress` flag |
| `cli/study_cmd.py` | `study temporal stationarity` | Minutes | ❌ No progress |
| `cli/study_cmd.py` | `study temporal acf-pacf` | Minutes | ❌ No progress |
| `cli/study_cmd.py` | `study dataset profile` | Seconds–minutes | ❌ No progress |
| `cli/study_cmd.py` | `study missingness profile` | Seconds–minutes | ❌ No progress |
| `cli/study_cmd.py` | `study results summarize` | Seconds | ❌ No progress |
| `cli/temporal_cmd.py` | `temporal experiment run` | Hours | ❌ No progress (has `temporal_progress_service.py` for snapshots) |
| `cli/temporal_cmd.py` | `temporal prepare materialize` | Minutes | ❌ No progress |
| `cli/st_cmd.py` | `st experiment run` | Hours | ❌ No progress |
| `cli/thesis_*_cmd.py` | `thesis all` | Minutes–hours | ❌ No progress |
| `cli/thesis_*_cmd.py` | `thesis dataset/missingness/compare/training` | Minutes each | ❌ No progress |

**Most long-running commands are silent or nearly silent.** Users cannot tell if the process is running, blocked, or close to completion.

---

## 4. Which commands already expose --progress/--no-progress?

**Only one:** `study temporal arma-order` in `cli/study_cmd.py` (line 338):
```python
@click.option("--progress/--no-progress", default=True, show_default=True, help="Show progress bar.")
```

The flag is passed to `ARMAOrderSelectionService.select_orders(progress=...)`, which passes it to `arma_order_selection_core.make_progress_iter(enabled=...)`.

**No other command in the entire codebase has `--progress` flags.**

---

## 5. Which services currently import tqdm or rich directly?

### tqdm usage:

Found via code inspection and grep:

| File | Usage | Pattern |
|---|---|---|
| `imputebench/services/study/arma_order_selection_core.py` (line 493) | `from tqdm.auto import tqdm` | Inside `make_progress_iter()` — try/except with fallback |
| *(awaiting full explore agent results for any additional uses)* | | |

### rich usage:

**None detected** in code inspection. `rich` is NOT in `pyproject.toml` dependencies or `requirements.txt`.

### Existing progress helper:

The only progress abstraction is `make_progress_iter()` in `arma_order_selection_core.py` (lines 479–496):
```python
def make_progress_iter(items, *, total=None, enabled=True, desc="Selecting ARMA orders"):
    if not enabled:
        return items
    try:
        from tqdm.auto import tqdm
    except ImportError:
        return items
    return tqdm(items, total=total, desc=desc)
```

**Characteristics:**
- Optional tqdm dependency (safe fallback)
- Writes to default tqdm output (likely stderr via `tqdm.auto`)
- No JSONL event logging
- No nested scope support
- No ETA/elapsed tracking beyond tqdm defaults
- Only used in serial path (n_jobs=1) — not passed to joblib workers

---

## 6. Which services currently write JSONL event logs or progress JSON?

### JSONL event logs:

- `imputebench/services/temporal/temporal_progress_service.py` — writes `TemporalProgressSnapshot` as JSON (not JSONL) for temporal experiment progress tracking
  - File: single JSON snapshot, overwritten on each save
  - NOT line-delimited JSON
  - Domain-specific to temporal experiments
  - Uses `json.dumps(..., indent=2, sort_keys=True)` — human-readable, not streaming

### Progress JSON:

- `test_hf2_runtime_event_jsonl_mirror.py` — test file name suggests runtime event JSONL mirror functionality exists
- `test_obj7_progress_event_contract.py` — test file for progress event contracts
- `test_progress_pypots_capture_filtering.py` — test file for progress capture filtering
- `test_streamlit_progress_bridge.py` — test for Streamlit progress bridge
- `test_hf_streamlit_progress_boundary_static_guards.py` — Streamlit progress boundary tests
- `test_hf_streamlit_safe_lifecycle_progress.py` — Streamlit lifecycle progress safety

**These tests indicate existing progress infrastructure exists in the Streamlit UI layer**, but not in the CLI layer (which is what we're building).

---

## 7. Which commands run in CI/non-TTY contexts and must default to silent?

All commands can potentially run in CI, but the most likely are:
- `study temporal arma-order` (has `--no-progress` already)
- `thesis all` and thesis subcommands (evidence generation pipelines)
- Any command that can be scripted/automated

**CI detection:** The codebase has NO existing CI detection patterns (no `GITHUB_ACTIONS`, `CI`, or `isatty` checks found in CLI layer). This must be built from scratch.

**Default behavior for CI:** silent (per spec Section 3.5).

---

## 8. Which commands are safe first integration targets?

**Priority ranking based on existing patterns and risk:**

### Priority A — ARMA order selection ✅ SAFEST
- Already has `--progress/--no-progress` flag
- Already has `make_progress_iter()` abstraction to refactor
- Service already accepts `progress: bool` parameter
- Isolated scope, well-understood behavior
- **Action:** Replace `make_progress_iter()` with framework `ProgressReporter.wrap()`, update CLI to use shared decorator

### Priority B — Study temporal stationarity ✅ SAFE
- `TemporalDiagnosticsService.run_stationarity_tests()` iterates over series
- No existing progress code to conflict with
- Clean integration point: wrap series iteration
- **Action:** Add progress reporter parameter to service, wrap CLI call

### Priority C — Study temporal ACF/PACF ✅ SAFE
- `TemporalDiagnosticsService.compute_acf_pacf()` iterates over sampled columns
- No existing progress code
- Clean integration point: wrap column iteration
- **Action:** Same pattern as stationarity

---

## 9. Which commands must be deferred because they need deeper lifecycle instrumentation?

### Deferred — Temporal experiment run
- Uses `temporal_progress_service.py` for internal progress snapshots (different paradigm)
- Complex orchestration with mask families, realizations, algorithms, evidence gates
- Has existing internal progress tracking that must not break
- **Action:** Defer to Patch 05h; align with existing `TemporalProgressService`

### Deferred — ST experiment run
- Most complex orchestration (graph assets, mask banks, plans, runs, evidence, gates, certification)
- Scientific outputs must not change
- High risk of destabilization
- **Action:** Defer to Patch 05i; only after temporal integrations are stable

### Deferred — Thesis commands
- Multiple independent subcommands
- Some may be fast enough not to need progress
- **Action:** Defer to Patch 05j; identify long-running thesis commands during implementation

### Out of scope — Lab/interactive commands
- `lab start` and related commands are Streamlit-based
- CLI-first design rule: "GUI reads and displays"
- **Action:** Not in scope for this framework

---

## Additional Audit Findings

### Optional dependencies status

| Library | In pyproject.toml? | In requirements.txt? | Usage in codebase |
|---|---|---|---|
| `tqdm` | ❌ No | ❌ No | Used via try/except import in `arma_order_selection_core.py` |
| `rich` | ❌ No | ❌ No | None detected |
| `streamlit` | ✅ Yes (>=1.30,<1.40) | ✅ Yes (>=1.30,<1.40) | Used in UI layer only (Streamlit apps) — NOT in CLI |

**Key insight:** Neither `tqdm` nor `rich` are declared dependencies. The framework must use optional imports with graceful fallback.

### Existing progress-related test files

| Test file | Domain | Relevance |
|---|---|---|
| `tests/study/test_arma_order_selection_core.py` | ARMA core | Must update for refactored progress |
| `tests/study/test_arma_order_selection_service.py` | ARMA service | Must update parameter interface |
| `tests/study/test_study_cli_temporal.py` | CLI tests | Must update for new progress flags |
| `tests/progress/` | **Does not exist yet** | Must create |

### Streamlit boundary check

All Streamlit usage is in:
- `app/` directory (Streamlit UI pages)
- `.streamlit/` config directory
- Various `test_streamlit_*` and `test_hf_streamlit_*` test files

**No Streamlit imports found in `imputebench/services/` or `imputebench/cli/`.** The invariant "CLI builds and certifies; GUI reads and displays" is maintained.

### Context propagation

- Current codebase has **no `ctx.obj` usage at root level**
- Services are called directly from CLI commands (no middleware layer)
- Pattern: CLI parses options → passes primitive values to service methods
- **Approach:** Introduce `ctx.obj` dictionary at root for `progress_config` propagation; services accept `progress: ProgressReporter | None = None` parameter

---

## Audit Validation Commands (to be run after implementation)

```bash
# Verify no Streamlit in services/CLI
grep -R "Streamlit\|streamlit" -n imputebench/services imputebench/cli || true

# Verify tqdm remains optional
grep -R "import tqdm\|from tqdm" -n imputebench/services/progress || true

# Verify no hard dependency on rich
grep -R "import rich\|from rich" -n imputebench/services/progress || true

# Verify progress terminal output goes to stderr
grep -R "file=sys.stderr\|file=stderr" -n imputebench/services/progress || true

# Verify root help shows progress options
python -m imputebench --help

# Verify backward compatibility
python -m imputebench study --help
python -m imputebench study temporal --help
python -m imputebench study temporal arma-order --help
python -m imputebench temporal --help
```

---

## Summary

| Question | Answer |
|---|---|
| Root Click app wired? | Plain `@click.group` with `add_command()`, no root params, no ctx.obj |
| Root options safe? | Yes, if before subcommand; dual root+local approach recommended |
| Long-running commands? | 10+ identified across study/temporal/st/thesis |
| Existing --progress flags? | Only `study temporal arma-order` |
| tqdm usage? | One function `make_progress_iter()` in ARMA core; safe optional import |
| rich usage? | None |
| JSONL event logs? | None (only domain-specific JSON snapshots in TemporalProgressService) |
| CI/non-TTY defaults? | Silent — detection must be built from scratch |
| First integration targets? | ARMA order selection → stationarity → ACF/PACF |
| Deferred commands? | Temporal experiment run, ST experiment run, thesis all |
