# ST Algorithm Card Consolidation Summary

**Policy version:** SCI-FIX05-v1  
**Generated at:** 2026-06-30T03:39:02.027565+00:00

---

## Algorithm Table

| Algorithm | Corrected Variant | Manifest | Impl Card | Ref Align | Dev Log | Claim Policy | Sci Ready |
|---|---|---|---|---|---|---|---|
| grin | mpgru_v1 | ✅ | ✅ | ✅ | ✅ | ✅ | false |
| stgcn | stconv_v1 | ✅ | ✅ | ✅ | ✅ | ✅ | false |
| ignnk | dgc_kriging_v1 | ✅ | ✅ | ✅ | ✅ | ✅ | false |
| dcrnn | dcgru_reconstruction_v1 | ✅ | ✅ | ✅ | ✅ | ✅ | false |

## Variant Table

| Algorithm | Corrected Variant | Claim Policy |
|---|---|---|
| grin | mpgru_v1 | paper_alignment_pending_scientific_training |
| stgcn | stconv_v1 | paper_alignment_pending_scientific_training |
| ignnk | dgc_kriging_v1 | paper_alignment_pending_scientific_training |
| dcrnn | dcgru_reconstruction_v1 | paper_alignment_pending_scientific_training |

## Reference-Alignment Completeness

| Algorithm | Reference Alignment OK |
|---|---|
| grin | ✅ Complete |
| stgcn | ✅ Complete |
| ignnk | ✅ Complete |
| dcrnn | ✅ Complete |

## Deviation-Log Completeness

| Algorithm | Deviation Log OK |
|---|---|
| grin | ✅ Complete |
| stgcn | ✅ Complete |
| ignnk | ✅ Complete |
| dcrnn | ✅ Complete |

## Claim-Policy Status

| Algorithm | Claim Policy OK | Label |
|---|---|---|
| grin | ✅ | paper_alignment_pending_scientific_training |
| stgcn | ✅ | paper_alignment_pending_scientific_training |
| ignnk | ✅ | paper_alignment_pending_scientific_training |
| dcrnn | ✅ | paper_alignment_pending_scientific_training |

## Blocked Claim Summary

_No blocking reasons — all algorithm cards are complete._

## Warnings

_No warnings._

## Next Required Steps

1. Ensure all four manifests use consistent SCI-FIX01 vocabulary.
2. Verify all corrected variants are declared consistently.
3. Complete any missing implementation card sections (19 required).
4. Verify 7-column reference-alignment tables for all algorithms.
5. Verify 8-column deviation-log tables for all algorithms.
6. Verify 7-section claim-policy documents for all algorithms.
7. Ensure algorithm-specific blockers are present.
8. SCI-FIX02 — scientific training tier.
9. SCI-FIX03 — convergence checks.
10. SCI-FIX08 — scientific evidence export.
11. SCI-FIX09 — final scientific gate.
