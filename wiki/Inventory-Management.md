# F-003: Inventory Management

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core flow built |
| Actor | Management, Volunteer |
| Last reviewed | 6 October 2026 |

## Preconditions

- At least one delivery has been completed or an inventory adjustment exists.

## Trigger

The user opens Inventory.

## Basic flow

1. The system calculates stock on hand from completed intake, redistribution, and adjustments.
2. The system displays crate count and calculated weight by category.
3. The system displays a use-by breakdown such as Today, This week, and Later.
4. The view updates when a stock movement is posted.

## Business rules

- Inventory is derived from an append-only movement history, not a silently editable balance.
- Only completed deliveries contribute inbound stock.
- Outbound movements and negative adjustments reduce stock.
- Returns and positive adjustments increase stock.
- Use-by information must remain available after intake so urgent stock can be prioritised.

## Data specification

| Field | Type | Notes |
| --- | --- | --- |
| Branch | Branch reference | Inventory is branch-specific |
| Category | Category reference |  |
| Crate Count on Hand | Calculated | Intake - redistribution +/- adjustments |
| Weight on Hand (kg) | Calculated | Based on movement quantities and category weights |
| Use-by Breakdown | Calculated | Today, this week, later; final bands to confirm |
| Last Updated | Date/time | Most recent contributing movement |

## Acceptance criteria

- `F-003-AC1`: Inventory reconciles to its contributing movements.
- `F-003-AC2`: Completing an intake delivery increases the correct category and use-by buckets.
- `F-003-AC3`: Adjustments update the view without rewriting source transactions.
- `F-003-AC4`: Users can identify stock requiring urgent action.

## Open questions

- Should inventory support units of measure other than crates?
- Should use-by be tracked at category/date level, crate level, or another batch level?
- How should stock that moves directly from intake to a waiting van appear in inventory?

## Current implementation notes

- Submission posts one immutable intake movement for each delivery category/use-by stock lot.
- Inventory shows on-hand, reserved, available, and calculated kg; it refreshes every twenty seconds.
- Dashboard alerts identify positive stock due today, within two days, or already expired.
- Reservations are excluded from available stock, and transaction locks prevent concurrent oversubscription.
