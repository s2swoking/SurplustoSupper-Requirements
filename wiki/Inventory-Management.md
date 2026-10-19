# F-003: Inventory Management

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core flow built |
| Actor | Management, Volunteer |
| Last reviewed | 19 October 2026 |

## Preconditions

- At least one delivery has been completed or an inventory adjustment exists.

## Trigger

The user opens Inventory.

## Basic flow

1. The system calculates stock on hand from completed intake, redistribution, and adjustments.
2. The system displays on-hand quantities in the units used by each category: crates, kilograms, and/or individual units.
3. The system displays normalized weight in kilograms and calculated meal-equivalent and CO2e impact totals.
4. The system displays a use-by breakdown such as Today, This week, and Later.
5. The view updates when a stock movement is posted.

## Business rules

- Inventory is derived from an append-only movement history, not a silently editable balance.
- Only completed deliveries contribute inbound stock.
- Outbound movements and negative adjustments reduce stock.
- Returns and positive adjustments increase stock.
- Use-by information must remain available after intake so urgent stock can be prioritised.
- Every stock movement stores the quantity and unit entered by the user plus its normalized weight in kilograms where a valid conversion or measured weight is available.
- The system must not invent a crate or unit count from a kilogram-only adjustment.
- Crate-to-kilogram conversion uses the category's effective average crate weight; unit-to-kilogram conversion uses the effective average unit weight or a measured total weight supplied with the movement.
- Meal-equivalent and CO2e totals use normalized weight and the effective category factors at the configured recognition event; the same food is counted once.

## Data specification

| Field | Type | Notes |
| --- | --- | --- |
| Branch | Branch reference | Inventory is branch-specific |
| Category | Category reference |  |
| Crates on Hand | Calculated decimal | Intake - redistribution +/- crate movements; shown where applicable |
| Units on Hand | Calculated decimal | Unit movements; shown where applicable |
| Weight on Hand (kg) | Calculated decimal | Direct kg movements plus normalized crate/unit movements |
| Meal Equivalents | Calculated decimal | Eligible redistributed kg x effective meals-per-kg factor |
| CO2e Impact (kg) | Calculated decimal | Eligible redistributed kg x effective CO2e-per-kg factor |
| Use-by Breakdown | Calculated | Today, this week, later; final bands to confirm |
| Last Updated | Date/time | Most recent contributing movement |

## Acceptance criteria

- `F-003-AC1`: Inventory reconciles to its contributing movements.
- `F-003-AC2`: Completing an intake delivery increases the correct category and use-by buckets.
- `F-003-AC3`: Adjustments update the view without rewriting source transactions.
- `F-003-AC4`: Users can identify stock requiring urgent action.
- `F-003-AC5`: Inventory can accept and display adjustments entered in crates, kilograms, or units according to the category configuration.
- `F-003-AC6`: The inventory ledger preserves the entered quantity/unit and the normalized kilogram value used for reporting.
- `F-003-AC7`: A kilogram-only adjustment changes weight without creating a fictional crate or unit count.

## Open questions

- Should use-by be tracked at category/date level, crate level, or another batch level?
- How should stock that moves directly from intake to a waiting van appear in inventory?
- What rounding precision should be used for normalized weight and impact totals?

## Current implementation notes

- Submission posts one immutable intake movement for each delivery category/use-by stock lot.
- Inventory shows on-hand, reserved, available, and calculated kg; it refreshes every twenty seconds.
- Dashboard alerts identify positive stock due today, within two days, or already expired.
- Reservations are excluded from available stock, and transaction locks prevent concurrent oversubscription.
- The current implementation records whole crates only; kilogram and unit movements and impact calculations remain to be built.
