# Execution Truth Protocol (AD2)

This protocol defines how ImputeBench verifies algorithm execution truth through direct SQLite database queries. AD2 is the AUTHORITATIVE stage — it always overrides AD0 inventory and AD1.5 conformance when runtime evidence contradicts static analysis.

## Scope

AD2 audits every canonical algorithm by querying the benchmark SQLite database for runtime evidence: results, training history, prediction fingerprints, checkpoint contracts, mask evidence, metrics, and timing data.

## Verdict categories

| Verdict | Meaning |
| --- | --- |
| **execution_ready** | All required runtime evidence present and valid. |
| **execution_partial** | Some evidence present but key pieces missing. |
| **execution_invalid** | Runtime evidence is contradictory or invalid. |
| **not_executed** | No runtime evidence found in database. |
| **not_runnable** | Algorithm cannot be executed in current environment. |

## Family-specific gates

- **DL / Temporal**: checkpoint + training history + mock detection + prediction fingerprints
- **Baselines**: metrics + mask evidence
- **Classical**: metrics + mask evidence
- **ML**: metrics + mask evidence
- **Proposed**: checkpoint + training history + fingerprints (when applicable)

## Resolution priority

AD2 execution truth is **always authoritative**. When AD2 says `execution_ready` and AD0 says `partial`, the resolved status is `execution_ready`. Runtime evidence > static analysis — non-negotiable.
