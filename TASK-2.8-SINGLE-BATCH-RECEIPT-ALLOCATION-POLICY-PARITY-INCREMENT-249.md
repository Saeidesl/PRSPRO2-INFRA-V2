# Task 2.8 — Single vs Batch Receipt Allocation Policy Parity (Increment 249)

Date: 2026-11-24
Evidence: E1 static source comparison.

## Scope
Compared the inspected single receipt and batch receipt validation paths for project-allocated items and general/unallocated quantity handling.

## Findings
- Batch receipt path explicitly supports receiving against an allocation-bearing item with no ProjectID as general warehouse stock, but only up to the unallocated portion (ordered quantity less total allocations).
- The inspected single receipt path, when itemAllocations exist, requires ProjectID and rejects missing project; the inspected branch does not show the same unallocated-general-warehouse allowance.
- Therefore single and batch interfaces appear to implement different receipt policy for the same item when order quantity includes a portion not allocated to any project. Static behavioral difference; whether this is intended is unknown.
- Both inspected paths validate against total ordered quantity and enforce project-specific allocation caps when a project is supplied.
- Receipt-status treatment is a separate issue documented in Increment 248.

## V2 policy decision and tests
1. Confirm whether unallocated surplus on partially allocated PO lines may be received into general warehouse stock.
2. Implement one canonical policy and share it across single and batch paths.
3. Contract test: item quantity 100, allocated 70; receive 30 with blank project (both paths), then project-specific receipt; reject >30 general, reject invalid project and overallocated project receipts.
4. Test batch mixed lines, replay and partial replay, and single/batch API parity.

## Verification
Static only; no tests run and no production changes. G-01/G-02/G-10 OPEN; schema readiness NOT APPROVED.
