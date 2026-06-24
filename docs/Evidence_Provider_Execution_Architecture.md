# Evidence Provider Execution Architecture (Sheet 11B)

This document describes how evidence *discovery* (inventory state) relates to
evidence *execution* (export capability), and the contract every provider obeys.

## Inventory state vs export capability

Two orthogonal facts about an evidence item for a target:

- **state** (`derivable`, `available`, `missing`, `blocked`, `unsupported`,
  `not_applicable`, `stale`) — produced by `provider.inspect(target)`,
  metadata-first, no payload load.
- **capability** (`EvidenceExportCapability.executable`) — produced by
  `provider.capabilities()`, declaring whether the provider can actually export
  the item.

**Invariant (derivable ⟺ executable):** an item may only be `derivable` when its
owning provider reports `executable=True`. `EvidenceProviderRegistry.
validate_export_capabilities()` enforces format coverage and single ownership;
`registry.is_executable(item_id)` is the single lookup used by tests and admin
audit.

## Provider contract

```python
class EvidenceProvider(Protocol):
    provider_id: str
    provider_version: str
    def descriptors(self) -> tuple[EvidenceTypeDefinition, ...]: ...
    def inspect(self, target) -> list[EvidenceItemDescriptor]: ...
    def plan(self, target, item_ids) -> list[EvidenceTypeDefinition]: ...
    def export(self, plan, staging_dir) -> EvidenceProviderReport: ...
    def capabilities(self) -> tuple[EvidenceExportCapability, ...]: ...
```

`BaseEvidenceProvider` (no longer raises Phase 11-08 markers):

- `plan` validates ownership and returns the matching type definitions.
- `export` dispatches on `item_id` to `self._exporters()`; an unknown item
  returns a **structured blocked report** (`UnsupportedEvidenceItem`), never
  `NotImplementedError`.
- `capabilities` derives `executable` from the keys of `_exporters()`.

Concrete providers override `_exporters()` (and optionally `capabilities()`).

## Shared export I/O

`providers/_export_io.py::ProviderExportWriter` is the only file writer:

- safe **relative** paths (no absolute, no `..`, no `repo://` output, nothing
  outside staging);
- canonical JSON (`allow_nan=False`), UTF-8 Markdown/CSV, Matplotlib PNG;
- non-empty validation on every write;
- `EvidenceProviderReport` / `ExportedItem` / `EvidenceBlockedItem` builders.

The `ExportEngine` remains the sole publication boundary: validate → stage →
invoke providers → verify → SHA-256 → manifest → atomic publish → catalog.
Providers write only inside the engine-supplied staging directory.

## Exporter families

- **core_result** (`core_result_exporters.py`): full rich exporters for the
  nine result/run items, sharing pure projections in `_projections.py`.
- **storyboard** (`storyboard_exporter.py`): the reconstruction figure.
- **gate** (`gate_exporter.py`): `EvidenceCompletenessGateService` composition.
- **training / temporal / spatiotemporal / comparison**: truthful
  descriptor-level evidence (`_descriptor_export.py`) — real JSON + Markdown
  recording target identity, claim relevance/scope, canonical source artifact
  ids/paths, and a claim boundary pointing at the domain renderer. These never
  fabricate a scientific figure; the heavy domain commands stay authoritative.

## Memoized context (`EvidenceContext`)

Per-pass memoized access to result/run/comparison, catalog records, and
registries (dataset/algorithm/masking/run-details) plus path locality, so the
seven providers inspecting one target never re-query persistence.
