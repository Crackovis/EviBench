# Operational CLI Foundations Update Report

Closure report for the Operational CLI Foundations documentation and implementation alignment. The implementation scope consolidates CRUD update hardening, typed configuration, administrative health/backup, and managed lab sessions under the CLI-first rule.

**Date:** 2026-06-13

---

## Audit differences from the two preliminary pinned drafts

The consolidated Operational CLI Foundations scope supersedes two preliminary drafts:

- `SISYPHUS_IMPLEMENTATION_SHEET_07_P1_CRUD_UPDATE_COMMANDS.md`
- `SISYPHUS_IMPLEMENTATION_SHEET_08_P2_CONFIG_ADMIN_LAB.md`

| Preliminary draft expectation | Repository-audited correction | Final documented behavior |
|---|---|---|
| P1 treated `algorithm update` and `masking update` as missing commands | Both update commands already existed | Scope became hardening: `--dry-run`, `--format table|json`, shared `EntityPatchReport`, service-level preview/apply patch methods |
| P1 suggested CLI handlers could directly apply updates | Business rules needed to move into services | CLI parses/renders; services construct previews, validate, and persist |
| P1 accepted generic JSON params for masking | Masking params needed object semantics | `--params` must decode to a JSON object; masking rate remains constrained to `[0, 1]` |
| P2 proposed generic config keys such as `data_dir`, `default_metrics`, `output_format` | Runtime paths are import-time facts and cannot be safely mutated dynamically | Only whitelisted typed preferences are mutable; path facts are read-only and reported separately |
| P2 proposed `config set` with `--key/--value` style | Existing CLI style favors positional key/value | Final command is `imputebench config set <key> <value>` with optional `--dry-run` and `--format` |
| P2 described `admin status --check-disk` | Status needed a general fast/deep split | Final command uses fast default plus `--deep`, `--progress`, and `--fail-on never|warning|error` |
| P2 described backup as copying registry/config/data | Raw recursive copy and raw SQLite copy were unsafe | Final backup is metadata-only by default, uses the SQLite backup API, stages output, and writes SHA-256 manifest entries |
| P2 proposed `lab stop` by PID/process enumeration | PID-only stopping risks PID reuse and arbitrary process termination | Final lab identity is session UUID plus process snapshot/fingerprint verification; `--force` does not bypass ownership checks |
| P2 only listed `lab list` and `lab stop` | Safe lifecycle required durable registry inspection and cleanup | Final lab surface includes `start --detach`, `list`, `show`, `stop`, and `cleanup` |

---

## Files created

### Documentation created in this pass

| File | Description |
|---|---|
| `docs/Operational_CLI_Foundations.md` | Main operational guide covering CLI-first boundary, safe update previews, typed config, admin health/backup, managed lab sessions, process ownership, command reference, and troubleshooting |
| `docs/Operational_CLI_Foundations_Update_Report.md` | This closure/update report |

### Implementation files created by the feature family

| File | Description |
|---|---|
| `imputebench/read_models/cli/entity_patch.py` | `FieldChange` and `EntityPatchReport` read models for update previews/diffs |
| `imputebench/cli/entity_patch_output.py` | Shared table/JSON renderer for `EntityPatchReport` |
| `imputebench/domain/config/application_config.py` | Typed application configuration, mutable-key whitelist, environment mapping, read-only facts |
| `imputebench/services/application_config_service.py` | Config load/report/set/validate service with precedence tracking and atomic writes |
| `imputebench/read_models/admin/status.py` | Admin health component/report read models |
| `imputebench/services/admin_status_service.py` | Fast/deep administrative health aggregation service |
| `imputebench/domain/admin/backup.py` | Backup plan/source/entry/manifest/result domain models |
| `imputebench/services/admin_backup_service.py` | Metadata-first backup planner/executor with SQLite backup API, staging, checksums, manifest, compression |
| `imputebench/domain/lab/session.py` | `LabSession` domain model with UUID identity and command fingerprint |
| `imputebench/services/lab_session_registry.py` | Atomic JSON registry for lab sessions with corrupt-file quarantine |
| `imputebench/services/lab_session_service.py` | Managed lab start/list/get/reconcile/stop/cleanup service |

---

## Files modified

### Documentation modified in this pass

| File | Changes summary |
|---|---|
| `docs/CLI_Reference.md` | Added examples and warnings for hardened entity updates, config show/set/validate, admin status/backup, and managed lab list/show/stop/cleanup |

### Implementation files modified by the feature family

| File | Changes summary |
|---|---|
| `imputebench/cli/algorithms_cmd.py` | Added structured update preview/output path for `--dry-run` and `--format table|json`; retained backward-compatible update behavior |
| `imputebench/services/algorithm_service.py` | Added service-level `preview_patch` and `apply_patch` returning `EntityPatchReport` |
| `imputebench/cli/masking_cmd.py` | Added structured update preview/output path; enforced object-shaped params for structured flow |
| `imputebench/services/masking_service.py` | Added masking patch preview/apply semantics and `EntityPatchReport` construction |
| `imputebench/cli/config_cmd.py` | Added `config show`, `config set`, and `config validate` while preserving `config style` commands |
| `imputebench/cli/admin_cmd.py` | Added `admin status` and `admin backup` commands with table/JSON output, progress options, fail-on policy, and backup flags |
| `imputebench/cli/lab_cmd.py` | Hardened lab startup with configurable default port/detach support and added `list`, `show`, `stop`, `cleanup` |
| `imputebench/services/runtime_env_service.py` | Added `ProcessSnapshot`, command fingerprinting, platform-specific process inspection, identity verification, and graceful/force termination helpers |

---

## Commands added or hardened

| Command | Status | Key additions / hardening |
|---|---|---|
| `imputebench algorithm update <id>` | Hardened | `--dry-run`, `--format table|json`, shared diff renderer, service-level patch report |
| `imputebench masking update <id>` | Hardened | `--dry-run`, `--format table|json`, params object validation, service-level patch report |
| `imputebench config show` | Added | Effective mutable preferences, read-only facts, source display, JSON/table output |
| `imputebench config set <key> <value>` | Added | Whitelist validation, dry-run preview, atomic YAML write, `.bak` preservation |
| `imputebench config validate` | Added | YAML schema validation with table/JSON output and non-zero exit on invalid config |
| `imputebench admin status` | Added | Fast/deep health checks, isolated component failures, `--fail-on` gate policy |
| `imputebench admin backup <output_dir>` | Added | Metadata-first inventory, dry-run, optional large scopes, staging, manifest, compression |
| `imputebench lab start --detach` | Hardened | Managed session registration, UUID identity, logs, configured default port |
| `imputebench lab list` | Added | Session registry listing, active/stale filters, reconciliation, table/JSON output |
| `imputebench lab show <session_id>` | Added | Full session details by UUID |
| `imputebench lab stop <session_id>` | Added | Dry-run preview, ownership verification, graceful timeout, optional force after verification |
| `imputebench lab cleanup` | Added | Stale/stopped registry-entry cleanup with dry-run and confirmation/force controls |

---

## Config keys exposed

### Mutable keys

| Key | Type / allowed values | Default | Environment override |
|---|---|---|---|
| `cli_default_output_format` | `table` or `json` | `table` | `IMPUTEBENCH_CLI_DEFAULT_OUTPUT_FORMAT` |
| `cli_progress_mode` | `auto`, `enabled`, `disabled` | `auto` | `IMPUTEBENCH_CLI_PROGRESS_MODE` |
| `cli_progress_backend` | `auto`, `tqdm`, `rich`, `silent` | `auto` | `IMPUTEBENCH_CLI_PROGRESS_BACKEND` |
| `lab_default_port` | integer `1024..65535` | `8501` | `IMPUTEBENCH_LAB_DEFAULT_PORT` |
| `lab_default_conda_env` | string | `deeplearning` | `IMPUTEBENCH_LAB_DEFAULT_CONDA_ENV` |
| `admin_backup_compression` | `zip` or `none` | `zip` | `IMPUTEBENCH_ADMIN_BACKUP_COMPRESSION` |

### Read-only facts

| Fact | Why read-only |
|---|---|
| `root_dir` | Import-time project root used by path services |
| `data_dir` | Derived from runtime path constants; not safely mutable after imports |
| `registry_dir` | Derived from runtime path constants; registries already resolve against it |
| `results_dir` | Derived from runtime path constants and result services |
| `plugins_dir` | Derived from project root and plugin discovery rules |
| `figures_dir` | Derived from project root and reporting/export conventions |
| `metadata_db_path` | SQLite connection path is resolved outside the config mutation layer |
| `active_package_root` | Reflects imported package checkout; mutation would not switch imports |
| `active_checkout_root` | Runtime fact used to detect checkout drift |
| `python_executable` | Interpreter fact for the current CLI process |

---

## Backup safety decisions

### Why metadata-only by default

Backups default to metadata/config/manifest scope because experiment outputs can be large, slow to copy, private, or redundant with regenerable artifacts. A metadata-first backup is suitable for frequent operational snapshots and avoids surprising users with multi-gigabyte copies. Large payloads remain available through explicit intent flags: `--include-data`, `--include-results`, and `--include-artifacts`.

### Why SQLite backup API

The SQLite database may be in WAL mode. Raw file copy can miss committed WAL state or capture an inconsistent pair of database/WAL files. The backup service therefore snapshots through SQLite's backup API and records `PRAGMA quick_check` in the manifest.

### Additional backup safeguards

- backup target cannot be inside the project root;
- sources cannot be nested inside the output directory;
- staging is used before final publication;
- `MANIFEST.json` is written and re-read;
- each copied file receives a SHA-256 checksum;
- compression happens from staging only after the manifest is built.

---

## Lab process identity strategy

### Why UUID, not PID

PIDs are operating-system handles and can be reused. A PID alone cannot prove that a process is the same lab process started by ImputeBench. `LabSession.session_id` is therefore the user-facing identity, while PID is only one recorded attribute.

### Why fingerprint verification

`lab stop` must verify ownership before termination. The runtime process snapshot is compared with the registered session using PID, process creation time when available, Python executable basename, and a command fingerprint derived from the Streamlit invocation. This prevents stale records and PID reuse from terminating unrelated processes.

`--force` is deliberately scoped: it escalates termination only after identity verification has passed. It never grants permission to stop an unverified or arbitrary process.

---

## Tests planned

| Subsystem | Planned targeted test files | Count |
|---|---:|---:|
| CRUD update hardening | service tests for algorithm patch, service tests for masking patch, CLI entity-update hardening tests | 3 |
| Config | application config service tests, config CLI tests | 2 |
| Admin | admin status service tests, admin backup service tests, admin CLI tests | 3 |
| Lab | lab session registry tests, lab session service tests, lab CLI tests | 3 |
| Broad regression | CLI/services/progress regression suite | 1 aggregate suite |

Total targeted planned files: **11**, plus **1** broader aggregate regression suite.

---

## Known limitations

- Config path mutation is intentionally unsupported; runtime directory paths are reported as read-only facts.
- Some process-detection fidelity is platform-specific. Windows uses PowerShell/CIM and WMIC fallbacks; other platforms use `psutil`, Linux `/proc`, or POSIX `ps` depending on availability.
- The lab session registry assumes single-writer CLI access. It uses atomic file replacement but does not implement distributed/concurrent locking.

---

## Deferred capabilities

| Capability | Deferred reason |
|---|---|
| Config path mutation | Requires an explicit path migration model and service reload/rebind semantics, not a simple `config set` |
| Distributed lab management | Requires multi-host/session coordination and stronger locking/heartbeat semantics |
| Backup restore automation | Current backup artifacts are restorable by manifest-guided manual process; automated restore needs a separate safety design and overwrite policy |

---

## Claim-safety notes

- No documentation claims that config paths are dynamically mutable.
- No documentation claims that `lab stop` can terminate arbitrary PIDs.
- No documentation claims that backup includes all data by default.
- The `--force` warning is explicit: it never bypasses process-ownership verification.

<!-- FOOTER:START -->

---

> [← Operational_CLI_Foundations](Operational_CLI_Foundations.md) · [⬆ Top](#) · [🏠 Home](../README.md) · [CLI_Reference →](CLI_Reference.md)
<!-- FOOTER:END -->
