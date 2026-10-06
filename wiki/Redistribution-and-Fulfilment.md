# F-005: Redistribution and Fulfilment

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core flow built |
| Actor | Management, Volunteer |
| Last reviewed | 6 October 2026 |

## Preconditions

- A recipient charity or project is active for the Branch.
- Eligible stock is available in inventory.

## Basic flow

1. The user chooses an active recipient, date, collection/delivery method, and eligible stock.
2. The system validates accepted categories, capacity, and available unreserved crates.
3. The allocation reserves stock in `Draft` status.
4. The user marks the allocation `Ready` when it is loaded or prepared.
5. Dispatch posts immutable outbound inventory movements.
6. The user completes the collection or delivery.
7. Any returned crates are recorded as positive movements against the original stock lot.

## Business rules

- Draft and Ready allocations reduce available stock but do not reduce stock on hand.
- Only categories accepted by the recipient can be allocated.
- An allocation cannot exceed recipient capacity or available stock.
- Draft or Ready allocations may be cancelled and release their reservation.
- Dispatch is atomic and cannot make a stock lot negative.
- Dispatched or completed lines may receive returns up to the quantity dispatched.
- Every lifecycle transition and return is audited.

## Acceptance criteria

- `F-005-AC1`: Concurrent allocations cannot reserve more crates than are available.
- `F-005-AC2`: Loading, dispatch, completion, and cancellation follow valid status transitions.
- `F-005-AC3`: Dispatch reduces inventory exactly once.
- `F-005-AC4`: A return restores stock and cannot exceed the dispatched quantity.
- `F-005-AC5`: Ineligible category, inactive recipient, wrong fulfilment method, and capacity violations are rejected.

## Current limitations

- The UI creates one stock-lot line at a time; the API and data model support multiple allocation lines.
- Van/route assignment, recipient cadence, proof of delivery, and automatic allocation are not yet modelled.
- Partial acceptance is represented by recording returned crates after dispatch.
