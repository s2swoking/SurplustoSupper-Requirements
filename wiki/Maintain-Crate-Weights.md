# EF-003: Maintain Crate Weights

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Partial |
| Actor | Management |
| Last reviewed | 19 October 2026 |

## Preconditions

- The category exists.

## Trigger

Management updates the average crate weight for a category.

## Basic flow

1. Management selects a category.
2. Management enters the average weight in kilograms.
3. The system validates and saves the value.
4. Future intake calculations use the updated average.

## Business rules

- Only Management can maintain crate weights.
- Each category has one active average weight at a time.
- Weight must be greater than zero.
- Updating an average must not rewrite weight recorded on completed historical movements unless that behaviour is explicitly approved.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Category | Category reference | Yes |  |
| Average Weight (kg) | Positive decimal | Yes | Drives intake calculation |
| Updated By | User reference | Auto |  |
| Updated At | Date/time | Auto |  |

## Acceptance criteria

- `EF-003-AC1`: Intake weight equals crate count multiplied by the category's active average.
- `EF-003-AC2`: Zero, negative, and invalid values are rejected.
- `EF-003-AC3`: The change records who made it and when.

## Current implementation

Management can update or CSV-import a Branch's average crate weight, positive-number validation is enforced, and Intake uses the value. `Updated By`, `Updated At`, and an audit event are captured. Effective-dated weight history is not yet implemented.

## Open questions

- Should weight changes be effective-dated for historical reporting?
- When an actual measured crate weight is available, should it override the category average automatically or require Management approval?

Meal-equivalent and CO2e impact factors are maintained per kilogram on the category record; see [Maintain Categories](Maintain-Categories).
