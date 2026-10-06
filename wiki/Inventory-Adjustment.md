# EF-004: Inventory Adjustment

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Partial |
| Actor | Management |
| Last reviewed | 6 October 2026 |

## Preconditions

- An inventory balance or movement exists for the selected category and Branch.

## Trigger

Management selects `Adjust inventory`.

## Basic flow

1. Management selects a category and, where required, a use-by stock bucket.
2. Management selects Expired, Miscount Increase, Miscount Decrease, or Return from Charity.
3. Management enters the quantity and a reason.
4. The system validates and posts the adjustment.
5. Inventory is recalculated and the immutable adjustment remains available for audit.

## Business rules

- Only Management can adjust inventory.
- Every adjustment requires a reason.
- Adjustments are logged movements, never silent balance overwrites.
- Corrections to completed Intake use Miscount Increase or Decrease rather than editing the delivery.
- A negative adjustment cannot make the applicable stock balance negative unless an approved exception rule permits it.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Adjustment ID | System-generated | Auto | Primary key |
| Branch | Branch reference | Yes |  |
| Category | Category reference | Yes |  |
| Use-by Date/Batch | Reference or date | Conditional | Needed when inventory is date-bucketed |
| Type | Enum | Yes | Expired, Miscount Increase, Miscount Decrease, Return from Charity |
| Quantity | Positive decimal | Yes | Direction comes from Type |
| Unit | Enum | Yes | Crates initially; broader UOM is an open decision |
| Reason | Text | Yes |  |
| Adjusted By | User reference | Auto |  |
| Adjusted At | Date/time | Auto |  |

## Acceptance criteria

- `EF-004-AC1`: Posting an adjustment changes inventory by the correct signed quantity.
- `EF-004-AC2`: The source adjustment cannot be silently edited or deleted.
- `EF-004-AC3`: The inventory view links back to its contributing adjustment history.
- `EF-004-AC4`: A reason and responsible user are recorded for every adjustment.

## Open questions

- Is the adjustment unit always crates, or can users adjust kilograms or individual items?
- How should a correction be reversed if the original adjustment was wrong?

## Current implementation notes

- Management can post positive or negative whole-crate movements against a category/use-by lot with a mandatory reason.
- Negative adjustments cannot consume stock reserved by draft or loaded allocations.
- Movements are protected from update or deletion by a database trigger and the action is also written to audit history.
- Structured Expired/Miscount reason types and direct drill-through from a stock lot to all movements remain to be built.
