# MOT04 Implementation Report

Implemented:

- `HumanExportParallelPolicy` with auto worker resolution and strict sequential mode.
- CLI flags for `imputebench results export-human` and compatible `thesis export-human`.
- Deterministic threaded hashing shared by the source export engine and human pack manifest.
- Atomic `WriteTask` scheduler for independent file writes.
- Human-pack storyboard copy/sidecar scheduling.
- Framework chart PNG rendering through module-level process worker functions.
- Performance trace emission at `provenance/performance_trace.json`, excluded from the content fingerprint.
- Provider-level metadata plus guarded storyboard source-export fan-out. Workers receive scalar ids/paths and rebuild `EvidenceContext` locally; provider instances are not pickled.
- Benchmark script at `scripts/benchmark_human_export_parallel.py`.

Validation run locally:

- `pytest tests\human_evidence\performance -q` -> 18 passed before source fan-out, 22 passed after source fan-out/source-export coverage.
- `pytest tests\human_evidence\test_request_validation.py tests\human_evidence\test_manifest_service.py tests\human_evidence\test_human_pack_determinism.py tests\human_evidence\test_human_pack_integration.py tests\human_evidence\test_human_pack_security.py -q` -> 35 passed.
- `pytest tests\human_evidence\framework\test_framework_radar_chart.py tests\human_evidence\framework\test_framework_pareto_chart.py tests\human_evidence\framework\test_framework_integration.py tests\human_evidence\framework\test_framework_cli.py -q` -> 18 passed.
- `pytest tests\results_interaction\test_export_engine.py tests\results_interaction\test_sheet11b_export_integration.py tests\results_interaction\test_result_storyboard_exporter.py -q` -> 30 passed.
- `pytest tests\human_evidence -q` -> 224 passed.
- `pytest tests\results_interaction -q` -> 368 passed, 81 warnings.
- `pytest -q` -> stopped during collection on unrelated missing modules/packages: `tests.cli`, `plugins.stgcn`, `scripts.audit_algorithm_design_inventory`, and `imputebench.services.algorithm_*`.

Deferred:

- Source export fan-out is guarded off by default and only applies to storyboard run fan-out when explicitly enabled.
- The 400-result performance benchmark must be run on the target machine with the provided script.
