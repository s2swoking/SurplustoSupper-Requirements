# EF-004: Inventory Adjustment

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Partial |
| Actor | Management |
| Last reviewed | 19 October 2026 |

## Preconditions

- An inventory balance or movement exists for the selected category and Branch.

## Trigger

Management selects `Adjust inventory`.

## Basic flow

1. Management selects a category and, where required, a use-by stock bucket.
2. Management selects Expired, Miscount Increase, Miscount Decrease, or Return from Charity.
3. Management selects Crate, Kilogram, or Unit from the units permitted for the category.
4. Management enters the quantity, any required measured weight, and a reason.
5. The system previews the signed quantity and normalized kilogram effect, then validates and posts the adjustment.
6. Inventory is recalculated and the immutable adjustment remains available for audit.

## Business rules

- Only Management can adjust inventory.
- Every adjustment requires a reason.
- Adjustments are logged movements, never silent balance overwrites.
- Corrections to completed Intake use Miscount Increase or Decrease rather than editing the delivery.
- A negative adjustment cannot make the applicable stock balance negative unless an approved exception rule permits it.
- Adjustments can be entered in crates, kilograms, or individual units when that unit is enabled for the category.
- Crate adjustments use the category's effective average crate weight to calculate the normalized kilogram movement.
- Kilogram adjustments use the entered quantity directly as normalized weight and do not change crate or unit counts.
- Unit adjustments use the category's effective average unit weight or require the user to enter a measured total weight.
- The movement stores the entered quantity, entered unit, conversion source/value, and normalized weight used at posting time.
- Reversing an incorrect adjustment creates a linked compensating movement; the original adjustment remains unchanged.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Adjustment ID | System-generated | Auto | Primary key |
| Branch | Branch reference | Yes |  |
| Category | Category reference | Yes |  |
| Use-by Date/Batch | Reference or date | Conditional | Needed when inventory is date-bucketed |
| Type | Enum | Yes | Expired, Miscount Increase, Miscount Decrease, Return from Charity |
| Quantity | Positive decimal | Yes | Direction comes from Type; whole numbers required for Unit unless the category explicitly allows divisible units |
| Unit | Enum | Yes | Crate, Kilogram, Unit; limited by category configuration |
| Measured Weight (kg) | Positive decimal | Conditional | Required for unit movements without an average unit weight; optional override where weighing is available |
| Conversion Value | Decimal | Conditional | Snapshot of the crate/unit weight used to normalize the movement |
| Normalized Weight (kg) | Signed decimal | Yes | Direct kg or calculated/measured equivalent used for inventory and impact reporting |
| Reason | Text | Yes |  |
| Adjusted By | User reference | Auto |  |
| Adjusted At | Date/time | Auto |  |

## Acceptance criteria

- `EF-004-AC1`: Posting an adjustment changes inventory by the correct signed quantity.
- `EF-004-AC2`: The source adjustment cannot be silently edited or deleted.
- `EF-004-AC3`: The inventory view links back to its contributing adjustment history.
- `EF-004-AC4`: A reason and responsible user are recorded for every adjustment.
- `EF-004-AC5`: Management can post valid adjustments in crates, kilograms, or units.
- `EF-004-AC6`: The confirmation preview shows the signed native quantity and normalized kilogram effect before posting.
- `EF-004-AC7`: A unit adjustment without an average unit weight is rejected unless measured total weight is supplied.
- `EF-004-AC8`: Reversing an adjustment posts a linked compensating movement and preserves the original audit record.

## Open questions

- Should fractional units ever be permitted for divisible products?
- Should a measured weight override the configured crate or unit average, and what approval is required?

## Current implementation notes

- Management can currently post positive or negative whole-crate movements against a category/use-by lot with a mandatory reason.
- Negative adjustments cannot consume stock reserved by draft or loaded allocations.
- Movements are protected from update or deletion by a database trigger and the action is also written to audit history.
- Kilogram/unit adjustments, structured Expired/Miscount reason types, compensating reversals, and direct drill-through from a stock lot to all movements remain to be built.
