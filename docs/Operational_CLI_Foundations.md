# Operational CLI Foundations

Operational CLI Foundations documents the hardened command surfaces introduced for safe entity updates, typed configuration, administrative health/backup, and managed lab-session lifecycle control.

---

## 1. Purpose and CLI-first boundary

The operational boundary is explicit:

```text
CLI builds and certifies. GUI reads and displays.
```

The CLI is the authoritative surface for creating operational state, validating it, exporting machine-readable reports, taking backups, and managing local lab sessions. Streamlit pages may display configuration, health, backup metadata, or lab-session state, but they must not become the source of operational truth.

Business logic belongs in services and domain/read-model objects:

- CLI handlers parse options and render output.
- Services perform validation, patching, status probing, backup planning, and process lifecycle decisions.
- Domain/read-model dataclasses carry structured state across layers.
- Services must not import Streamlit.

This keeps terminal automation, CI smoke checks, and GUI display paths aligned around the same business rules.

---

## 2. Safe entity update previews

`algorithm update` and `masking update` now support safe preview and structured diff output.

The shared read model is `EntityPatchReport`:

| Field | Meaning |
|---|---|
| `entity_type` | Entity family, such as `algorithm` or `masking` |
| `entity_id` | Stable entity identifier being patched |
| `dry_run` | `true` when no persistence occurred |
| `changed` | `true` when at least one supplied field differs |
| `changes` | Tuple of `FieldChange` entries |
| `before` | Snapshot before the patch |
| `after` | Snapshot after the patch would apply or did apply |

Each `FieldChange` records:

| Field | Meaning |
|---|---|
| `field` | Patched field name |
| `before` | Previous value |
| `after` | New value |

Preview mode uses `--dry-run` and must not mutate storage. Output is selectable with `--format table|json`:

```bash
imputebench algorithm update alg_saits --description "Validated SAITS profile" --dry-run --format table
imputebench algorithm update alg_saits --default-config '{"epochs": 100}' --dry-run --format json
imputebench masking update mask_mcar30 --rate 0.3 --dry-run --format table
imputebench masking update mask_block20 --params '{"block_size": 20}' --format json
```

The CLI renderer is shared so entity-update commands do not duplicate table/JSON formatting rules. Existing update behavior remains backward-compatible when neither `--dry-run` nor explicit structured `--format` behavior is requested.

---

## 3. Typed configuration and precedence

`ApplicationConfigService` owns the typed application configuration surface. It exposes mutable preferences with provenance and keeps runtime facts read-only.

Effective values are resolved in this order:

```text
compiled defaults → project YAML → environment → CLI option
```

The persisted project file is `config/imputebench.yaml`. The service reads a nested YAML layout and maps it into flat typed keys. Environment variables override project-file values for the current process. Command-local CLI options still override the effective configuration for that single command invocation.

Mutable keys are restricted by the `MUTABLE_KEYS` whitelist:

| Key | Type / allowed values | Environment override |
|---|---|---|
| `cli_default_output_format` | `table` or `json` | `IMPUTEBENCH_CLI_DEFAULT_OUTPUT_FORMAT` |
| `cli_progress_mode` | `auto`, `enabled`, `disabled` | `IMPUTEBENCH_CLI_PROGRESS_MODE` |
| `cli_progress_backend` | `auto`, `tqdm`, `rich`, `silent` | `IMPUTEBENCH_CLI_PROGRESS_BACKEND` |
| `lab_default_port` | integer port `1024..65535` | `IMPUTEBENCH_LAB_DEFAULT_PORT` |
| `lab_default_conda_env` | string | `IMPUTEBENCH_LAB_DEFAULT_CONDA_ENV` |
| `admin_backup_compression` | `zip` or `none` | `IMPUTEBENCH_ADMIN_BACKUP_COMPRESSION` |

`config set` rejects unknown keys and read-only facts. Successful writes are atomic: the service writes a same-directory temporary YAML file, flushes and fsyncs it, preserves the previous file as `.bak`, then replaces the active config.

Useful commands:

```bash
imputebench config show --sources
imputebench config show --format json
imputebench config set lab_default_port 8600 --dry-run --format table
imputebench config set cli_default_output_format json --format json
imputebench config validate --file config/imputebench.yaml
```

---

## 4. Read-only vs mutable settings

Read-only facts are reported for transparency but are not dynamically mutable:

| Read-only fact | Meaning |
|---|---|
| `root_dir` | Active project root resolved by the runtime path service |
| `data_dir` | Active data directory |
| `registry_dir` | Active registry directory |
| `results_dir` | Active results directory |
| `plugins_dir` | Active plugins directory |
| `figures_dir` | Active figures directory |
| `metadata_db_path` | Active SQLite metadata database path |
| `active_package_root` | Imported package checkout root |
| `active_checkout_root` | Active checkout root used for command execution |
| `python_executable` | Python interpreter running the CLI |

Paths are not dynamically mutable because several modules resolve `ROOT_DIR`, `DATA_DIR`, `REGISTRY_DIR`, `RESULTS_DIR`, and SQLite paths at import time. Allowing `config set data_dir ...` would create a false contract: already-imported services would continue using the old constants.

Path relocation is therefore deferred to a future explicit migration feature. The current CLI reports paths as facts and refuses to mutate them.

---

## 5. Admin health status

`AdminStatusService` aggregates operational health into isolated component checks. A single failing probe is converted into an `AdminStatusComponent` error and does not abort the full report.

Fast mode is the default and covers lightweight checks such as:

- metadata database presence;
- SQLite connection and `PRAGMA quick_check`;
- dataset, algorithm, masking, run, result, comparison, and plugin counts;
- required directories;
- disk space;
- package/checkout drift.

Deep mode adds slower checks such as SQLite integrity, full plugin validation, recursive directory sizing, orphan-candidate scans, and stale lab-session detection.

`--fail-on` controls process exit policy:

| Policy | Exit behavior |
|---|---|
| `never` | Always exit zero after rendering the report |
| `warning` | Exit non-zero when overall status is `warning` or `error` |
| `error` | Exit non-zero only when overall status is `error` |

Examples:

```bash
imputebench admin status
imputebench admin status --format json
imputebench admin status --deep --progress --fail-on warning
```

---

## 6. Backup scope and restoration manifest

`AdminBackupService` implements safe, metadata-first backups. The default scope intentionally excludes large experiment payloads.

Default metadata-only sources:

- `data/metadata.db` snapshot;
- `config/imputebench.yaml`;
- `config/style.yaml`;
- `plugins/*/imputebench_plugin.json`;
- `data/registry/*.json`.

Large scopes require explicit opt-in:

| Option | Scope |
|---|---|
| `--include-data` | Full `data/` directory |
| `--include-results` | `data/results/` |
| `--include-artifacts` | `data/artifacts/` |

SQLite is copied through the SQLite backup API, not by raw file copy. This avoids inconsistent snapshots when SQLite is in WAL mode. The backup is staged first, checksums are computed, `MANIFEST.json` is written and re-read, then the staging directory is published or compressed.

The manifest records:

- schema version;
- backup id;
- creation time;
- project/version metadata;
- selected options;
- source inventory;
- excluded scopes;
- per-file SHA-256 entries;
- SQLite `PRAGMA quick_check` result;
- completion status.

Recursion protection refuses backup targets inside the project root and refuses source paths nested inside the selected output directory.

Examples:

```bash
imputebench admin backup ../evibench-backups --dry-run --format table
imputebench admin backup ../evibench-backups --dry-run --format json
imputebench admin backup ../evibench-backups --include-results --compress
imputebench admin backup ../evibench-backups --include-data --no-compress --progress
```

Important: backups do not include all data by default. Use the opt-in flags only when the larger, less portable payload is required.

---

## 7. Managed lab sessions

Lab lifecycle management uses a durable `LabSession` domain model. The session identity is a UUID, never a PID.

`LabSession` records:

- `session_id` UUID;
- `pid` and process creation time;
- port;
- status;
- Python executable;
- full command and command fingerprint;
- Streamlit app path;
- package root;
- start/stop timestamps;
- exit code;
- stdout/stderr log paths;
- foreground/detached mode.

`LabSessionRegistry` persists sessions in `data/runtime/lab_sessions.json` using an atomic JSON write: serialize to a temporary file, flush/fsync, then replace. Corrupt registries are quarantined to a timestamped `.corrupt_...` file and a fresh registry can be used.

Foreground mode runs Streamlit attached to the terminal. Detached mode starts a child process, records the PID/create time/logs, and returns a session UUID for later inspection or stop operations.

Examples:

```bash
imputebench lab start --port 8600
imputebench lab start --detach --stdout-log data/runtime/lab_logs/lab.out.log
imputebench lab list --active-only
imputebench lab show <SESSION_ID>
imputebench lab stop <SESSION_ID> --dry-run
imputebench lab cleanup --dry-run
```

---

## 8. Process-ownership safety

`lab stop` is ownership-checked. It does not terminate arbitrary PIDs.

The runtime service builds a `ProcessSnapshot` for the session PID using platform-specific inspection:

- `psutil` when available;
- Windows PowerShell/CIM fallback;
- Windows WMIC fallback;
- Linux `/proc` fallback;
- POSIX `ps` fallback.

The snapshot includes PID, process creation time, executable, command line, and status. The session is considered expected only when enough identity checks match:

- PID matches;
- creation time matches when available;
- Python executable basename matches;
- command fingerprint matches the recorded Streamlit app invocation.

`--force` only changes the termination method after ownership is verified. It never bypasses identity verification. If a PID was reused or the command no longer matches the recorded session, `lab stop` fails instead of killing a potentially unrelated process.

---

## 9. Command reference

| Command | Purpose | Key safety flags / notes |
|---|---|---|
| `imputebench algorithm update <id>` | Patch selected algorithm fields | `--dry-run`, `--format table|json`; no empty patch |
| `imputebench masking update <id>` | Patch selected masking fields | `--dry-run`, `--format table|json`; rate validation; params must be JSON object |
| `imputebench config show` | Display mutable preferences and read-only facts | `--format table|json`, `--keys`, `--sources` |
| `imputebench config set <key> <value>` | Persist a whitelisted mutable preference | `--dry-run`, `--format table|json`; rejects read-only paths |
| `imputebench config validate` | Validate YAML configuration | `--file`, `--format table|json`; exits non-zero when invalid |
| `imputebench admin status` | Probe operational health | `--deep`, `--fail-on never|warning|error`, progress options |
| `imputebench admin backup <output_dir>` | Create metadata-first backup | `--dry-run`, `--include-data`, `--include-results`, `--include-artifacts`, `--compress` |
| `imputebench lab start` | Launch the Streamlit lab | `--detach`, configurable default port, optional logs |
| `imputebench lab list` | List registered sessions | `--active-only`, `--include-stale`, `--reconcile`, `--format table|json` |
| `imputebench lab show <session_id>` | Show one session | Uses UUID identity, not PID identity |
| `imputebench lab stop <session_id>` | Stop a verified owned session | `--dry-run`, `--timeout-seconds`, `--force`; force does not bypass identity checks |
| `imputebench lab cleanup` | Remove stale/stopped registry entries | `--dry-run`, `--stale-only`, `--all-stopped`, `--force` for broad cleanup |

---

## 10. Troubleshooting

| Issue | Resolution |
|---|---|
| `config set data_dir ...` fails | Runtime directory paths are read-only facts. They are not mutable through this configuration surface. |
| A project config value appears ignored | Run `imputebench config show --sources`; an environment variable may be shadowing the project YAML value. |
| `config validate` exits non-zero | Inspect the reported key/type errors, correct `config/imputebench.yaml`, and rerun validation. |
| `admin status --fail-on warning` exits non-zero | The command is acting as a gate. Re-run with `--format json` to inspect the component that raised the overall status. |
| Backup is smaller than expected | This is normal. Backups are metadata-only by default; add `--include-results`, `--include-artifacts`, or `--include-data` intentionally. |
| Backup refuses an output directory | The output directory may be inside the project root or recursive with a source. Choose a sibling/outside directory such as `../evibench-backups`. |
| SQLite backup concerns under WAL mode | The backup service uses the SQLite backup API and records `quick_check`; it does not raw-copy `metadata.db` plus WAL files. |
| `lab list` shows stale sessions | Run `imputebench lab list --reconcile` or `imputebench lab cleanup --dry-run`, then clean up stale entries. |
| `lab stop` says identity is not verified | The PID may have exited/reused or the command no longer matches the recorded session. Stop the process manually only after independent confirmation. |
| `lab stop --force` still refuses | `--force` changes graceful-vs-forceful termination only after ownership verification; it cannot override identity checks. |

<!-- FOOTER:START -->

---

> [← CLI_Reference](CLI_Reference.md) · [⬆ Top](#) · [🏠 Home](../README.md) · [Operational_CLI_Foundations_Update_Report →](Operational_CLI_Foundations_Update_Report.md)
<!-- FOOTER:END -->
