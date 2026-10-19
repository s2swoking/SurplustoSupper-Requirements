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
2. Management selects Expired, Miscount Increase, Miscount Decrease, or Return from Recipient.
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
- Unit adjustments are available only when the category has effective Units per Crate and Average kg per Crate values. They use the derived kg per Unit unless a permitted measured total weight is supplied.
- For a unit-enabled stock lot, crates and units share a canonical individual-unit balance: `canonical units = crates × units per crate`. A unit adjustment can therefore reduce stock originally received in crates without inventing a new quantity.
- A kilogram-only stock lot has no canonical unit count. It cannot be adjusted in crates or units until an authorised physical recount supplies and records that count.
- The movement stores the entered quantity/unit, units-per-crate snapshot where applicable, kg conversion source/value, measured-weight flag, and normalized weight used at posting time.
- Reversing an incorrect adjustment creates a linked compensating movement; the original adjustment remains unchanged.

Example: if a category is configured as 12 units per crate and 6 kg per crate, one unit normalizes to 0.5 kg. Reducing a two-crate stock lot by five units leaves 19 canonical units and reduces normalized weight by 2.5 kg. The ledger preserves that the adjustment was entered as five units.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Adjustment ID | System-generated | Auto | Primary key |
| Branch | Branch reference | Yes |  |
| Category | Category reference | Yes |  |
| Use-by Date/Batch | Reference or date | Conditional | Needed when inventory is date-bucketed |
| Type | Enum | Yes | Expired, Miscount Increase, Miscount Decrease, Return from Recipient |
| Quantity | Positive decimal | Yes | Direction comes from Type; whole numbers required for Unit unless the category explicitly allows divisible units |
| Unit | Enum | Yes | Crate, Kilogram, Unit; limited by category configuration |
| Measured Weight (kg) | Positive decimal | No | Optional approved override where weighing is available; does not change the entered physical count |
| Units per Crate | Positive whole number | Conditional | Snapshot used for crate/unit conversion on unit-enabled stock lots |
| Conversion Value | Decimal | Conditional | Snapshot of kg per crate or derived kg per unit used to normalize the movement |
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
- `EF-004-AC7`: A unit adjustment is rejected unless Unit is enabled and effective Units per Crate and Average kg per Crate values exist.
- `EF-004-AC8`: Reversing an adjustment posts a linked compensating movement and preserves the original audit record.
- `EF-004-AC9`: A crate or unit adjustment updates the same canonical unit balance for a unit-enabled stock lot and cannot make it negative.
- `EF-004-AC10`: A kilogram-only lot cannot be adjusted in crates or units without an authorised recount that establishes its physical count.
- `EF-004-AC11`: The confirmation preview shows the units-per-crate and kg conversion snapshots that will be stored with the movement.

## Open questions

- Should fractional units ever be permitted for divisible products?
- Should a measured weight override the configured crate or unit average, and what approval is required?

## Current implementation notes

- Management can currently post positive or negative whole-crate movements against a category/use-by lot with a mandatory reason.
- Negative adjustments cannot consume stock reserved by draft or loaded allocations.
- Movements are protected from update or deletion by a database trigger and the action is also written to audit history.
- Kilogram/unit adjustments, canonical unit balances, units-per-crate snapshots, structured Expired/Miscount reason types, compensating reversals, and direct drill-through from a stock lot to all movements remain to be built.
