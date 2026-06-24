# CLI Completeness P3 Audit — Secondary Gaps

**Generated:** 2026-06-13  
**Repository:** `Interfaces/EviBench/`  
**Scope:** Secondary ImputeBench CLI gaps deferred from the Sprint 06 baseline audit.  
**Intent:** Classify each secondary command as `READY`, `REQUIRES_FOUNDATION`, or `DEFERRED` before any CLI implementation work.

---

## 1. Classification Summary

| Command | Classification | Recommended priority | Rationale |
|---|---|---:|---|
| `admin status` | `READY` | P3-01 | Read-only health report can be implemented safely from existing storage, registry, and filesystem state. Expensive checks should be opt-in via `--deep`. |
| `lab list` | `REQUIRES_FOUNDATION` | P3-02 | `lab start` launches Streamlit directly and does not persist managed session metadata. |
| `lab stop` | `REQUIRES_FOUNDATION` | P3-02 | Safe stop requires a trusted session registry and process identity validation before killing any process. |
| `admin backup OUTPUT_DIR` | `REQUIRES_FOUNDATION` | P3-03 | Needs an explicit backup service, source inventory, atomic manifest, and checksum model. |
| `config show` | `REQUIRES_FOUNDATION` | P3-04 | Existing config CLI only exposes `config style show`; a canonical typed config service/schema does not exist. |
| `config set` | `REQUIRES_FOUNDATION` | P3-04 | Arbitrary key writes are unsafe without a typed schema, validators, and controlled persistence semantics. |
| `result copy` | `REQUIRES_FOUNDATION` | P3-05 | Existing result CLI can list/show/delete/export, but cloning needs explicit ID/provenance/artifact semantics. |
| `audit list` | `DEFERRED` | P3-06 | Audit commands are script wrappers; no canonical audit registry exists to list from. |
| `ingest list` | `DEFERRED` | P3-07 | The product contract for “ingestion history” versus “currently ingested resources” is not defined. |

---

## 2. Classification Definitions

| Label | Meaning |
|---|---|
| `READY` | Command can be added as a safe CLI/read-model layer over existing data sources without inventing lifecycle semantics. |
| `REQUIRES_FOUNDATION` | Command must wait for a service/model/schema that defines persistence, safety, identity, or mutation semantics. |
| `DEFERRED` | Command should not be scheduled until the domain contract or canonical registry is designed. |

---

## 3. Command Audits

### 3.1 `lab list`

**Classification:** `REQUIRES_FOUNDATION`  
**Recommended priority:** P3-02, paired with `lab stop` foundation.

#### Current state in codebase

- `imputebench/cli/lab_cmd.py` defines only `imputebench lab start`.
- `lab start` resolves the active package root and Streamlit app entrypoint, checks checkout drift, checks port availability, optionally kills an existing process when root `--force` is present, and then runs Streamlit with `subprocess.run(...)`.
- The command is foreground-oriented: it prints “Press Ctrl+C to stop” and waits for Streamlit to exit.
- No `lab list` command exists.
- No `LabSession` model or durable session registry was found for PID, port, command, or start-time tracking.

#### Missing foundation

- `LabSession` model with at least: session ID, PID, port, command line, executable path, app path, checkout root, start time, status, and last-seen time.
- Durable lab-session registry, preferably under a project-local runtime/state directory rather than inferred from ports alone.
- Stale-session detection that can distinguish dead processes from active sessions.
- Process identity validation to prove that a PID still represents the Streamlit process started by ImputeBench.

#### What needs to be built

1. Introduce a lab session persistence contract.
2. Update `lab start` to register sessions when launching managed sessions.
3. Add a read model for listing active, stopped, and stale sessions.
4. Implement stale cleanup rules before exposing `lab list`.

---

### 3.2 `lab stop`

**Classification:** `REQUIRES_FOUNDATION`  
**Recommended priority:** P3-02, after the `LabSession` registry exists.

#### Current state in codebase

- `lab start` currently contains ad hoc port conflict handling through `RuntimeEnvService.is_port_available()`, `get_process_on_port()`, and `kill_process()`.
- Kill behavior is scoped to resolving a port conflict during startup, not to managing a known ImputeBench-owned lab session.
- No `lab stop` command exists.

#### Missing foundation

- Managed session persistence from `lab start`.
- PID/port/command/start-time registry.
- Process identity validation before termination.
- Stale-session detection and cleanup.
- Stop semantics: stop by session ID, by port, or active default; graceful termination before force kill; clear not-owned-process refusal.

#### What needs to be built

1. Build the same `LabSession` registry required by `lab list`.
2. Add process identity checks using PID plus command/app path/start-time where available.
3. Define graceful shutdown timeout and escalation behavior.
4. Refuse to kill unrelated processes even when they occupy the requested port.

---

### 3.3 `audit list`

**Classification:** `DEFERRED`  
**Recommended priority:** P3-06, after audit registry design.

#### Current state in codebase

- `imputebench/cli/audit_cmd.py` exposes many concrete audit commands, including `run`, `sqlite`, `masking-truth`, `mask-mechanism`, `benchmark-staleness`, `saits-inference-truth`, `training-history`, `runtime-timing`, `retire`, and `size`.
- Most audit commands are wrappers around scripts or service functions.
- Removed commands are documented inline where backing scripts are missing (`registry-deps`, `metadata`).
- No `audit list` command exists.

#### Missing foundation

- Canonical audit registry describing each audit’s ID, lifecycle status, scope, cost, output formats, failure modes, dependencies, and owning implementation.
- Agreement on whether script-wrapped audits, service-backed audits, hidden aliases, and removed audits appear in listings.
- Stable status taxonomy for available, deprecated, hidden, removed, and blocked audits.

#### What needs to be built

1. Design an audit registry contract before adding CLI output.
2. Register audit metadata close to command definitions or in a central service.
3. Add tests that prevent command/registry drift.
4. Only then expose `audit list`.

---

### 3.4 `admin status`

**Classification:** `READY`  
**Recommended priority:** P3-01.

#### Current state in codebase

- `imputebench/cli/admin_cmd.py` currently provides operational/admin wrappers: `migrate`, `seed-london`, `prepare-official`, `db-sanitize`, `inject-doc-nav`, and `recover-training-history`.
- Existing admin commands are mostly script delegates and lifecycle operations.
- No `admin status` command exists.

#### Missing foundation

- No new domain foundation is required if the command remains read-only and conservative.
- The only design boundary needed is cost control: fast status by default, expensive filesystem scans only under `--deep`.

#### What specifically needs to be built

`admin status` should be a read-only health report with a fast default path:

- storage/database health: metadata database exists, connection opens, basic integrity query succeeds;
- registry counts: datasets, algorithms/plugins, masking specs, runs, results, comparisons where canonical stores exist;
- plugin validity summary: valid/invalid/unregistered counts, not full repair;
- run counts by status;
- result and comparison counts;
- artifact/result-root size summary using cheap top-level measurements by default;
- free disk space for repository/data/artifact roots;
- `--deep` mode for expensive recursive scans, checksum-like checks, orphan-artifact detection, or plugin validation details.

#### Implementation notes

- The command must not migrate, sanitize, repair, delete, or write.
- JSON output can be added later, but the first version should keep stdout deterministic and testable.
- Avoid failing the whole command on one unhealthy subsystem; report component status and return non-zero only for explicit `--fail-on` behavior if that option is later introduced.

---

### 3.5 `admin backup OUTPUT_DIR`

**Classification:** `REQUIRES_FOUNDATION`  
**Recommended priority:** P3-03.

#### Current state in codebase

- `admin_cmd.py` has operational wrappers but no backup command.
- Existing admin commands call migration, seeding, preparation, database sanitation, doc navigation, and training-history recovery scripts.
- No `AdminBackupService` was found.

#### Missing foundation

- `AdminBackupService` with an explicit source inventory and safety contract.
- Canonical list of backup sources: metadata database, config files, registry/manifests, and selected project state.
- Policy for large data: datasets and artifacts must be opt-in.
- Atomic manifest/checksum generation.
- Partial failure handling and restore-readiness metadata.

#### What needs to be built

1. Create `AdminBackupService` that writes to a temporary staging directory before publishing the backup.
2. Include metadata database, config files, registry/manifests by default.
3. Add explicit `--include-datasets` and `--include-artifacts` options for large payloads.
4. Produce a manifest containing source paths, destination paths, file sizes, checksums, timestamp, repository version if available, and backup options.
5. Ensure backup output is never inside a source directory unless explicitly allowed and protected against recursion.

---

### 3.6 `config show`

**Classification:** `REQUIRES_FOUNDATION`  
**Recommended priority:** P3-04, before `config set`.

#### Current state in codebase

- `imputebench/cli/config_cmd.py` defines a root `config` group and a nested `config style` group.
- Current commands are `config style show` and `config style validate`.
- `config style show` reads a style YAML file through `StyleConfig.from_file(...)` and prints JSON.
- There is no top-level `config show` command.

#### Missing foundation

- Typed canonical config service/schema for global ImputeBench configuration.
- Clear distinction between style config, runtime defaults, storage paths, plugin paths, progress defaults, and environment-derived settings.
- Source precedence model: default values, project files, user files, environment variables, and CLI overrides.

#### What needs to be built

1. Define `ConfigService` and typed config models.
2. Specify supported config files and precedence.
3. Implement read-only `config show` over the canonical typed model.
4. Include validation status and source location per value where useful.

---

### 3.7 `config set`

**Classification:** `REQUIRES_FOUNDATION`  
**Recommended priority:** P3-04, after `config show` and typed schema.

#### Current state in codebase

- `config_cmd.py` only manages style display/validation.
- No generic config mutation API exists.
- No top-level `config set` command exists.

#### Missing foundation

- Same typed canonical config service required by `config show`.
- Controlled list of mutable keys.
- Validators and type coercion for each supported setting.
- Atomic writes and backup/rollback behavior for config files.

#### What needs to be built

1. Defer arbitrary key writes.
2. Add mutation only for typed, documented settings.
3. Implement dry-run/preview semantics before writing.
4. Preserve comments/formatting where possible or document canonical rewrite behavior.
5. Refuse unknown keys instead of silently creating them.

---

### 3.8 `result copy`

**Classification:** `REQUIRES_FOUNDATION`  
**Recommended priority:** P3-05.

#### Current state in codebase

- `imputebench/cli/results_cmd.py` currently exposes `result list`, `result show`, `result delete`, `result export-bundle`, `result spatial-show`, `result export-spatial`, and `result export-training-evidence`.
- Existing result behavior is read/delete/export oriented.
- No `result copy` command exists.
- Existing `ResultService` is used for list/get/delete operations, but clone semantics are not present in the CLI file.

#### Missing foundation

- `ResultCloneService` with explicit clone semantics.
- New result ID strategy.
- Provenance fields linking clone to source result.
- Artifact-copy policy: deep copy, reference, deduplicate, or export/import bundle path.
- Source linkage and audit trail.
- Collision handling for output paths and artifact roots.

#### What needs to be built

1. Define clone use cases: duplicate for experimentation, freeze for publication, or branch for repair.
2. Implement `ResultCloneService` that clones metadata and artifacts consistently.
3. Record source result ID, clone timestamp, clone reason, and operator/context metadata where available.
4. Validate all artifact references after clone.
5. Add CLI only after the service defines safe rollback on partial copy failure.

---

### 3.9 `ingest list`

**Classification:** `DEFERRED`  
**Recommended priority:** P3-07.

#### Current state in codebase

- `imputebench/cli/ingest_cmd.py` exposes `ingest inspect`, `ingest dataset`, `ingest plugin`, and `ingest bundle`.
- These commands inspect or ingest external resources through `_build_resource_ingestor()`.
- No `ingest list` command exists.

#### Missing foundation

- Canonical ingestion-history contract.
- Decision on whether `ingest list` means:
  - historical ingestion events;
  - currently registered resources that originated from ingestion;
  - failed/pending ingestion attempts;
  - imported bundles only;
  - all resource provenance records.
- Event schema for source path, resource type, target ID, timestamp, overwrite behavior, result, warnings/errors, and content fingerprint.

#### What needs to be built

1. Define the ingestion-history domain contract.
2. Decide whether historical ingestion events are persisted independently from current registries.
3. Add ingestion event recording to existing ingest commands if history is required.
4. Only expose `ingest list` once the command has a canonical source of truth.

---

## 4. Recommended Implementation Order

| Priority | Work item | Why first/next |
|---:|---|---|
| P3-01 | `admin status` | Safe, read-only, useful operational visibility; no lifecycle mutation semantics required. |
| P3-02 | Lab session foundation, then `lab list`/`lab stop` | Prevents unsafe PID/port handling and blocks accidental termination of unrelated processes. |
| P3-03 | `AdminBackupService`, then `admin backup OUTPUT_DIR` | Backup is valuable but must be atomic and manifest-backed before exposure. |
| P3-04 | Canonical config service/schema, then `config show`/`config set` | Avoids arbitrary writes and config corruption. |
| P3-05 | `ResultCloneService`, then `result copy` | Clone semantics need provenance and artifact consistency before CLI. |
| P3-06 | Audit registry, then `audit list` | Listing script wrappers without canonical metadata would be misleading. |
| P3-07 | Ingestion-history contract, then `ingest list` | The command name is ambiguous until “history” vs “current resources” is resolved. |

---

## 5. Explicit Non-Goals for This Audit

- Do not implement any commands.
- Do not add placeholder/fake command handlers.
- Do not create service files.
- Do not add command signatures that are not backed by the current files and the foundation described above.
- Do not treat PID/port ownership, config mutation, result cloning, audit enumeration, or ingestion history as safe without a canonical model.

---

## 6. Delta from Sprint 06 Baseline Audit

The Sprint 06 baseline classified `admin status` as deferred/lower priority in the secondary gap table. This P3 audit refines that decision: `admin status` is `READY` if constrained to a safe, read-only health report with `--deep` for expensive scans. The remaining secondary commands either require foundation or remain deferred until their domain contracts are canonical.

---

*End of P3 audit.*
