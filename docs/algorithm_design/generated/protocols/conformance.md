# Scientific Conformance Protocol (AD1.5)

This protocol defines how ImputeBench audits each algorithm's implementation for conformance to its declared scientific principle. Conformance is a statement about the implementation, not a claim of superiority over other methods.

## Scope

AD1.5 audits every canonical algorithm registered in the AD0 inventory. Each algorithm is assigned an expected scientific principle based on its name, role, and family. Static source probes verify that the implementation matches this principle. Optional AD2 execution truth bridge and behavioral probes provide additional confidence.

## Verdict categories

| Verdict | Meaning |
| --- | --- |
| **conformant** | Implementation matches the declared principle. |
| **mostly_conformant** | Core logic matches but minor deviations exist. |
| **partial_conformance** | Some aspects match but key mechanisms unconfirmed. |
| **non_conformant** | Implementation does not match the declared principle. |
| **not_auditable** | Cannot audit — source unresolved or contract incomplete. |
| **not_applicable** | Legacy/invalid entry excluded from canonical set. |

## Probe types

- **Static probe**: source-code pattern matching for expected signals.
- **Behavior probe**: deterministic synthetic tests for numerical output validation.
- **AD2 bridge**: cross-references AD2 execution truth verdicts for DL/proposed methods.
- **A1/A2 bridge**: SAITS-family fidelity and inference truth audit evidence.

## Conformance is not superiority

A conformant algorithm implements its declared principle correctly. This does not mean it performs better than a non-conformant algorithm. Conformance and predictive performance are orthogonal concerns.
