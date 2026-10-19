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
- Crate-to-kilogram conversion uses the category's effective average crate weight. For a unit-enabled category, crate-to-unit conversion uses effective Units per Crate and unit-to-kilogram conversion uses the derived kg per Unit or an approved measured total weight.
- A unit-enabled stock lot uses individual units as its canonical physical count; the interface may decompose that count into whole crates plus remaining loose units without storing a fictional fractional crate.
- A kilogram-only stock lot remains separately identifiable and cannot be shown as crates or units without an authorised physical recount.
- Meal-equivalent and CO2e totals use normalized weight and effective configured category factors at the configured recognition event; Unconfigured and Not Applicable factors never default to a numeric value and the same food is counted once.

## Data specification

| Field | Type | Notes |
| --- | --- | --- |
| Branch | Branch reference | Inventory is branch-specific |
| Category | Category reference |  |
| Whole Crates on Hand | Calculated integer | For unit-enabled lots, canonical units divided by Units per Crate; remainder is displayed separately |
| Loose Units on Hand | Calculated integer | Canonical-unit remainder for unit-enabled stock lots |
| Native Crates on Hand | Calculated integer | Crate-tracked lots for categories without individual-unit handling |
| Units on Hand | Calculated integer | Canonical count for unit-enabled stock lots |
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
- `F-003-AC8`: For a unit-enabled stock lot, receiving or adding one crate increases canonical units by the effective Units per Crate value.
- `F-003-AC9`: Removing individual units from crate-origin stock leaves a reconcilable whole-crate/loose-unit display and the correct normalized kg.
- `F-003-AC10`: Inventory distinguishes unit-enabled, crate-only, and kilogram-only stock lots so an unavailable conversion is never assumed.

## Open questions

- Should use-by be tracked at category/date level, crate level, or another batch level?
- How should stock that moves directly from intake to a waiting van appear in inventory?
- What rounding precision should be used for normalized weight and impact totals?

## Current implementation notes

- Submission posts one immutable intake movement for each delivery category/use-by stock lot.
- Inventory shows on-hand, reserved, available, and calculated kg; it refreshes every twenty seconds.
- Dashboard alerts identify positive stock due today, within two days, or already expired.
- Reservations are excluded from available stock, and transaction locks prevent concurrent oversubscription.
- The current implementation records whole crates only; kilogram and unit movements, canonical unit balances, units-per-crate conversion, and impact calculations remain to be built.
