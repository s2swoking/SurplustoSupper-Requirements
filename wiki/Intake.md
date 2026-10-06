# F-002: Intake

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core flow built |
| Actor | Volunteer |
| Last reviewed | 5 October 2026 |

## Preconditions

- Management has created an open Delivery ID.
- The category master and average crate weights are configured.
- The volunteer is signed in.

## Trigger

The volunteer selects an open Delivery ID to receive.

## Basic flow

1. The volunteer selects the delivery.
2. The volunteer selects a category.
3. The volunteer enters the crate count with the stepper or numeric input.
4. The system calculates weight as `crate count x category average crate weight`.
5. The volunteer selects a use-by date.
6. The system saves that category line immediately.
7. The volunteer repeats the flow for other categories and can review saved lines.
8. When receiving is complete, the volunteer submits the delivery.
9. The system marks the delivery complete and posts its lines to inventory.

## Postconditions

- The delivery is closed to further intake edits.
- Inventory is increased by the completed delivery's line items.

## Business rules

- Several volunteers may work against the same Delivery ID.
- Each line saves immediately and is shared through the server database.
- A delivery must contain at least one line before submission.
- A submitted delivery is read-only.
- Corrections after submission are Management adjustments, not edits to the original intake record.
- `Recorded By` is the signed-in volunteer.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Line Item ID | System-generated | Auto | Primary key |
| Delivery | Delivery reference | Yes |  |
| Category | Category reference | Yes |  |
| Crate Count | Positive integer | Yes |  |
| Weight (kg) | Calculated | Auto | Count x category average |
| Use-by Date | Date | Yes |  |
| Delivery Status | Open/Submitted | Auto | Submitted when intake closes |
| Submitted At | Date/time | Auto |  |
| Recorded By | User reference | Auto | Signed-in volunteer |

## Acceptance criteria

- `F-002-AC1`: Only open deliveries appear in the Intake selection list.
- `F-002-AC2`: Saving a category makes it visible when the delivery is reopened or refreshed.
- `F-002-AC3`: Re-selecting a saved category edits the existing line rather than creating a duplicate.
- `F-002-AC4`: Calculated weight uses the category's configured average.
- `F-002-AC5`: Submission makes the delivery and its line items read-only.
- `F-002-AC6`: Completing a delivery increases inventory once and only once.

## Current implementation

Delivery selection, immediate line saving, category editing, calculated weight, use-by capture, and submission are built. Google authentication, branch permissions, actor audit capture, and five-second shared-delivery refresh are also built. Posting to an inventory ledger is not yet built.
